# Coffee Cherry Detection API

API para detección y clasificación de cerezos de café usando YOLOv8n-seg.

## Clases Detectadas
- 0: Maduro (rojo)
- 1: Pinton (amarillo)
- 2: Seco (marrón)
- 3: Sobremaduro (morado)
- 4: Verde (verde)

## Archivos Necesarios para Despliegue

1. `main.py` - Código del servidor FastAPI
2. `best.pt` - Modelo YOLOv8n-seg entrenado (6.5 MB)
3. `requirements.txt` - Dependencias de Python
4. `Procfile` - Comando para ejecutar en Render
5. `render.yaml` - Configuración de Render (opcional)
6. `.gitignore` - Archivos a ignorar en Git

## Cómo Desplegar en Render

### Paso 1: Preparar Repositorio en GitHub
1. Crea un repositorio en GitHub
2. Sube TODOS los archivos (excepto venv y archivos de prueba)
3. Asegúrate de subir `best.pt` (el modelo)

### Paso 2: Conectar con Render
1. Ve a https://render.com
2. Regístrate con tu cuenta de GitHub
3. Click en "New +" → "Web Service"
4. Conecta tu repositorio
5. Configuración:
   - Name: coffee-detection-api
   - Environment: Python 3
   - Build Command: `pip install -r requirements.txt`
   - Start Command: `uvicorn main:app --host 0.0.0.0 --port $PORT`
   - Plan: Free

### Paso 3: Desplegar
- Click en "Create Web Service"
- Espera 5-10 minutos mientras se despliega
- Obtendrás una URL como: `https://coffee-detection-api.onrender.com`

## Endpoints

### GET /
Verifica que la API está en línea
```
https://tu-url.onrender.com/
```

### POST /predict
Envía una imagen y recibe las detecciones
```python
import requests

url = "https://tu-url.onrender.com/predict"
files = {"file": open("imagen.jpg", "rb")}
response = requests.post(url, files=files)
result = response.json()
```

Respuesta:
```json
{
  "success": true,
  "image_size": {"width": 382, "height": 886},
  "total_detections": 24,
  "avg_confidence": 0.916,
  "class_counts": {
    "maduro": 8,
    "pinton": 12,
    "seco": 0,
    "sobremaduro": 1,
    "verde": 3
  },
  "detections": [
    {
      "class_id": 4,
      "class": "verde",
      "confidence": 0.919,
      "polygon": [[x1, y1], [x2, y2], ...],
      "bbox": [x1, y1, x2, y2]
    }
    ...
  ]
}
```

## Optimizaciones para Internet Lento
- El servidor recibe imágenes comprimidas (JPEG, calidad 70%)
- Responde solo con JSON (coordenadas, no imágenes procesadas)
- Tamaño típico de respuesta: ~20 KB
- Consumo total: ~60-80 KB por predicción
