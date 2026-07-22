# Predicción de ingresos - MLOps Grupo 02

[![Integration](https://github.com/AdrianOropeza/pontia-mlops-IA0526-grupo2/actions/workflows/integration.yml/badge.svg)](https://github.com/AdrianOropeza/pontia-mlops-IA0526-grupo2/actions/workflows/integration.yml)
[![Build Model](https://github.com/AdrianOropeza/pontia-mlops-IA0526-grupo2/actions/workflows/build.yml/badge.svg)](https://github.com/AdrianOropeza/pontia-mlops-IA0526-grupo2/actions/workflows/build.yml)
[![Deploy Model](https://github.com/AdrianOropeza/pontia-mlops-IA0526-grupo2/actions/workflows/deploy.yml/badge.svg)](https://github.com/AdrianOropeza/pontia-mlops-IA0526-grupo2/actions/workflows/deploy.yml)

## Título y descripción breve

Proyecto MLOps para entrenar, evaluar, registrar y desplegar un modelo `RandomForestClassifier` capaz de predecir si los ingresos anuales de una persona superan los 50.000 dólares.

El proyecto utiliza el conjunto de datos **Adult Income** de UCI y automatiza la integración, construcción y puesta en producción mediante GitHub Actions, MLflow, Docker y Azure Container Apps.

## Estado del proyecto

**Terminado y desplegado para la evaluación.**

El repositorio dispone de pruebas automatizadas, tres pipelines de CI/CD, registro y versionado del modelo, una API de inferencia creada con FastAPI y despliegue en Azure.

## Características principales

- Descarga y procesamiento del conjunto de datos Adult Income.
- Limpieza de valores ausentes y codificación de variables categóricas.
- Estandarización de las características de entrada.
- Entrenamiento de un modelo `RandomForestClassifier`.
- Evaluación automática de precisión y generación del informe de clasificación.
- Registro de experimentos, métricas y artefactos mediante MLflow.
- Registro del modelo entrenado en Azure Machine Learning.
- API REST para realizar predicciones.
- Endpoint de salud con versión de la API y estado del modelo.
- Métrica del número total de predicciones realizadas.
- Imagen Docker publicada en GitHub Container Registry.
- Despliegue automatizado en Azure Container Apps.
- Versionado de la API basado en tags de Git.

## Estructura del repositorio

```text
.
├── .github/workflows/
│   ├── integration.yml
│   ├── build.yml
│   └── deploy.yml
├── deployment/
│   ├── app/
│   │   ├── main.py
│   │   └── version.py
│   └── Dockerfile
├── model_tests/
├── scripts/
│   └── register_model.py
├── src/
│   ├── data_loader.py
│   ├── evaluate.py
│   ├── main.py
│   └── model.py
├── unit_tests/
├── pytest.ini
└── requirements.txt
```

## Instalación

### Requisitos

- Git
- Python 3.10 o superior
- Docker, para construir y ejecutar el contenedor
- Una cuenta de Azure configurada, para registrar y desplegar el modelo

### Clonar el repositorio

```bash
git clone https://github.com/AdrianOropeza/pontia-mlops-IA0526-grupo2.git
cd pontia-mlops-IA0526-grupo2
```

### Crear y activar el entorno virtual

```bash
python -m venv .venv
```

En Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

En Linux o macOS:

```bash
source .venv/bin/activate
```

### Instalar las dependencias

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### Descargar los datos

En Linux o macOS:

```bash
mkdir -p data/raw
curl -o data/raw/adult.data https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.data
curl -o data/raw/adult.test https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.test
```

En Windows PowerShell:

```powershell
New-Item -ItemType Directory -Force data\raw
Invoke-WebRequest https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.data -OutFile data\raw\adult.data
Invoke-WebRequest https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.test -OutFile data\raw\adult.test
```

## Pruebas

Ejecutar las pruebas unitarias desde PowerShell:

```powershell
$env:PYTHONPATH = "src"
pytest unit_tests/ --cov=model --cov=evaluate --cov=data_loader --cov-report=term
```

En Linux o macOS:

```bash
PYTHONPATH=src pytest unit_tests/ --cov=model --cov=evaluate --cov=data_loader --cov-report=term
```

Las pruebas comprueban la carga y transformación de datos, el entrenamiento del modelo y su evaluación.

## Entrenamiento del modelo

Antes de entrenar se deben configurar el servidor de MLflow y los datos de Azure correspondientes.

Ejemplo en PowerShell:

```powershell
$env:MLFLOW_TRACKING_URL = "URL_DEL_SERVIDOR_MLFLOW"
$env:EXPERIMENT_NAME = "adult-income-model"
$env:RUN_NAME = "entrenamiento-local"
python src/main.py
```

El proceso:

1. Carga los datos de entrenamiento y prueba.
2. Limpia y transforma las variables.
3. Entrena el modelo Random Forest.
4. Calcula la precisión y el informe de clasificación.
5. Guarda el modelo, el escalador y los codificadores.
6. Registra las métricas y los artefactos en MLflow.

## Uso de la API

La API ofrece documentación OpenAPI interactiva en:

```text
https://ca-ml-api-grupo02.lemonhill-277c6ef0.spaincentral.azurecontainerapps.io
```

### Comprobar el estado del servicio

```bash
curl https://ca-ml-api-grupo02.lemonhill-277c6ef0.spaincentral.azurecontainerapps.io/health
```

Ejemplo de respuesta:

```json
{
  "status": "ok",
  "api_version": "v1.1.1",
  "worker_state": "ready",
  "model_loaded": true
}
```

### Realizar una predicción

```bash
curl -X POST "https://ca-ml-api-grupo02.lemonhill-277c6ef0.spaincentral.azurecontainerapps.io/predict" \
  -H "Content-Type: application/json" \
  -d '{
    "age": 39,
    "workclass": "State-gov",
    "fnlwgt": 77516,
    "education": "Bachelors",
    "education-num": 13,
    "marital-status": "Never-married",
    "occupation": "Adm-clerical",
    "relationship": "Not-in-family",
    "race": "White",
    "sex": "Male",
    "capital-gain": 2174,
    "capital-loss": 0,
    "hours-per-week": 40,
    "native-country": "United-States"
  }'
```

### Consultar las métricas

```bash
curl https://ca-ml-api-grupo02.lemonhill-277c6ef0.spaincentral.azurecontainerapps.io/metrics
```

## Pipelines CI/CD

### 1. Integration

Se ejecuta al crear o actualizar un Pull Request.

- Instala las dependencias.
- Ejecuta las pruebas unitarias.
- Calcula la cobertura.
- Publica los resultados como comentario en el Pull Request.
- Bloquea la integración si las pruebas fallan.

### 2. Build Model

Se ejecuta al integrar cambios en `main` o manualmente.

- Inicia sesión en Azure.
- Descarga el conjunto de datos.
- Entrena y evalúa el modelo.
- Ejecuta las pruebas específicas del modelo.
- Registra el modelo y sus artefactos mediante MLflow.

### 3. Deploy Model

Se inicia manualmente mediante `workflow_dispatch`.

- Obtiene todos los tags de Git para calcular la versión.
- Construye la imagen Docker.
- Publica la imagen en GitHub Container Registry.
- Actualiza la aplicación en Azure Container Apps.
- Espera hasta que `/health` devuelve HTTP 200.
- Muestra el enlace de Swagger al finalizar.

## Secretos y variables

Los pipelines utilizan secretos de GitHub para evitar guardar credenciales en el código.

### Secretos

- `AZURE_CREDENTIALS`: credenciales del servicio de Azure.
- `MLFLOW_TRACKING_URL`: dirección del servidor de seguimiento de MLflow.
- `GH_PAT`: token utilizado para publicar y descargar imágenes del registro.

### Variables

- `EXPERIMENT_NAME`: nombre del experimento de MLflow.
- `MODEL_NAME`: nombre del modelo registrado.

Los valores reales nunca deben incluirse en commits, capturas de pantalla ni documentación pública.

## Tecnologías

- Python
- pandas y NumPy
- scikit-learn
- pytest y pytest-cov
- MLflow
- Azure Machine Learning
- FastAPI y Pydantic
- Docker
- GitHub Actions
- GitHub Container Registry
- Azure Container Apps

## Contribución

Las modificaciones deben realizarse en una rama independiente y enviarse mediante Pull Request.

```bash
git switch main
git pull origin main
git switch -c tipo/descripcion-del-cambio
```

Después de modificar y verificar el proyecto:

```bash
git add .
git commit -m "Descripción clara del cambio"
git push -u origin tipo/descripcion-del-cambio
```

Antes de integrar un Pull Request se debe:

1. Comprobar que el pipeline de integración termina correctamente.
2. Solicitar la revisión de otro integrante del equipo.
3. Resolver los comentarios recibidos.
4. Obtener al menos una aprobación.
5. Integrar el cambio en `main`.

## Problemas encontrados y soluciones

### Nombre incorrecto de Azure Container App

Las primeras versiones utilizaban un nombre de recurso que no coincidía con el configurado en Azure. Se corrigió el nombre del contenedor mediante Pull Requests revisados antes de integrarlos.

### Obtención de la versión durante el despliegue

El checkout inicial no descargaba el historial completo ni los tags, por lo que `git describe` no podía calcular correctamente la versión. Se solucionó configurando `fetch-depth: 0` en el pipeline de despliegue.

### Tiempo de carga del modelo

La API puede tardar mientras descarga el modelo y los artefactos desde MLflow. El pipeline realiza comprobaciones periódicas sobre `/health` y solo finaliza correctamente cuando recibe HTTP 200.

### Gestión segura de credenciales

Las credenciales necesarias para MLflow, Azure y GitHub Container Registry se trasladaron a secretos y variables de GitHub para impedir que aparezcan en el repositorio.

## Licencia

Este repositorio se ha creado con fines académicos para la evaluación de la asignatura Introducción a DevOps.

No se ha declarado una licencia de código abierto. Salvo autorización expresa de sus autores, no se concede permiso para copiar, modificar o redistribuir el proyecto fuera de su finalidad educativa.