FROM python:3.11-slim

# Librerías de sistema que necesita el OCR (OpenCV / RapidOCR) y el manejo de imágenes.
# Estas son las que faltaban en Vercel (libGL, libxcb, etc.) y aquí sí las instalamos.
RUN apt-get update && apt-get install -y --no-install-recommends \
    libgl1 \
    libglib2.0-0 \
    libxcb1 \
    libsm6 \
    libxext6 \
    libxrender1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Precargar los modelos del OCR durante el build (no en la primera petición del usuario),
# así la primera lectura de foto no falla ni tarda de más.
RUN python -c "from rapidocr_onnxruntime import RapidOCR; RapidOCR()" || true

COPY . .

# Render entrega el puerto en la variable PORT.
ENV PORT=10000
EXPOSE 10000

CMD gunicorn app:app --bind 0.0.0.0:$PORT --workers 1 --threads 2 --timeout 180 --max-requests 40 --max-requests-jitter 10
