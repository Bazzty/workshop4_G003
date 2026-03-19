# Taller Evaluado 03 - Optimización de Dockerfile

**Estudiantes**: Bastian Contreras / Ignacio Ovalle

## Optimizaciones y Resultados

Este documento cumple con la rúbrica exigida para evidenciar los resultados obtenidos tras aplicar las optimizaciones sobre la imagen de Docker usando `uv` y configuración de caché avanzado.

### Comparación de Tamaños de la Imagen

| Etapa | Tamaño Aproximado | Detalles y Justificación |
| :--- | :--- | :--- |
| **Antes de las optimizaciones** | **~1.0 GB** | Las imágenes por defecto (`python:3.12` basadas en Debian) junto a instalaciones de `pip` sin caché optimizado generan un volumen altísimo. Los problemas de sintaxis y el orden de copiado además impedían reutilizar capas. |
| **Después de las optimizaciones** | **82.5 MB** | Se logró reducir significativamente el peso total y el tiempo de compilación. Cumpliendo con creces el requerimiento de pesar **menos de 200 MB** y también solicitudes más estrictas de menor peso. |

### Lista de Mejoras Realizadas en el `Dockerfile`:

1.  **Reducción de tamaño base**: Sustitución a `python:3.12-alpine`, el cual reduce la imagen base en más del 85%.
2.  **Transición a `uv` (Sin guardar binario en capa)**: Se reemplazó `pip` por `uv`. Además, usamos una característica avanzada (`RUN --mount`) para ejecutar el binario virtual de `uv` temporalmente, sin copiarlo a las capas finales, ahorrando casi ~45MB extras de la etapa final.
3.  **Optimización de caché (Capas ordenadas)**: El archivo `requirements.txt` se copia primero de manera aislada, garantizando que el paso de instalación se guarde en caché. Sólo cuando se modifican las dependencias Docker vuelve a ejecutar la instalación.
4.  **Gestión de variables de entorno**: Se incluyeron directivas avanzadas (`PYTHONDONTWRITEBYTECODE` y `PYTHONUNBUFFERED`) para que la imagen no cree archivos compilados basura (`.pyc`) que aumentan el peso.
5.  **Limpieza del Contexto**: Implementación del `.dockerignore` previniendo la subida al contexto de carpetas enormes como `.venv`, `.git` y el directorio `__pycache__`.

---

> El contenedor ha sido probado satisfactoriamente en `http://127.0.0.1:5001` respondiendo al endpoint principal `<p>Hello, World!</p>`.
