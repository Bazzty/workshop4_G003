FROM python:3.12-alpine

WORKDIR /app

# Variables de entorno para optimizar Python en Docker
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Copiar solo requirements.txt primero para optimizar capas (caché)
COPY docker_with_problems/src/requirements.txt .

# Crear entorno e instalar dependencias montando temporalmente el binario de uv
RUN --mount=from=ghcr.io/astral-sh/uv:latest,source=/uv,target=/bin/uv \
    /bin/uv venv && /bin/uv pip install --no-cache -r requirements.txt

# Configurar el PATH para que use el entorno virtual por defecto
ENV PATH="/app/.venv/bin:$PATH"

# Copiar el código fuente
COPY docker_with_problems/src/ .

# Exponer el puerto donde corre la aplicación
EXPOSE 5001

# Comando para ejecutar la aplicación
CMD ["python", "app.py"]

