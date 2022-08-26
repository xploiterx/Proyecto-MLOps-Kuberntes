# Deploying Machine Learning Models en Kubernetes

Un patrón común para implementar modelos de Machine Learning (ML) en entornos de producción, ejemplo, los modelos de ML entrenados con los paquetes SciKit Learn o Keras (para Python), que están listos para proporcionar predicciones sobre nuevos datos, es exponer estos ML como microservicios de API RESTful, alojados dentro de [Docker] (https://www.docker. com) contenedores. Luego, estos pueden implementarse en un entorno de nube para manejar todo lo necesario para mantener una disponibilidad continua, p. tolerancia a fallos, escalado automático, balanceo de carga y actualizaciones continuas de servicios.

Los detalles de configuración para una implementación en la nube disponible de forma continua son específicos de los proveedores de la nube de destino, ejemplo, el proceso de implementación y la topología de Amazon Web Services no es el mismo que el de Microsoft Azure, que a su vez no es el mismo que el de Google Cloud Platform. Esto constituye un conocimiento que debe adquirir cada proveedor de la nube. Además, es difícil (algunos dirían que casi imposible) probar estrategias de implementación completas localmente, lo que dificulta la depuración de problemas como las redes.

[Kubernetes](https://kubernetes.io) es una plataforma de orquestación de contenedores que busca abordar estos problemas. Brevemente, proporciona un mecanismo para definir **todas** las topologías de implementación de aplicaciones basadas en microservicios y sus requisitos de nivel de servicio para mantener la disponibilidad continua. Es independiente del proveedor de la nube de destino, se puede ejecutar en las instalaciones e incluso localmente en su computadora portátil; todo lo que se requiere es un clúster de máquinas virtuales que ejecutan Kubernetes, es decir, un clúster de Kubernetes.

Este Proyecto está diseñado para leerse junto con el código de este repositorio y el apoyo de videos, además el repo que usaremos en el proyecto contiene los módulos de Python, los archivos de configuración de Docker y las instrucciones de Kubernetes para demostrar cómo un modelo simple de Python ML se puede convertir en una puntuación de modelo RESTful de nivel de producción (o predicción) servicio API, utilizando Docker. 

Este proyecto NO es una guía completa de Kubernetes, Docker o ML; considérelo más como una intro a 'ML en Kubernetes' para demostrar la capacidad y permitir que los recién llegados a Kubernetes (por ejemplo, científicos de datos que están más enfocados en construir modelos en lugar de implementarlos) - para ponerse en marcha rápidamente y familiarizarse con los conceptos y patrones básicos.

Demostraremos la implementación del modelo ML usando dos enfoques diferentes: un enfoque de primeros principios usando Docker y Kubernetes; y luego una implementación usando el marco nativo de Kubernetes [Seldon-Core](https://www.seldon.io) para agilizar la implementación de los servicios de ML. El primero ayudará a apreciar el segundo, que constituye un marco poderoso para implementar y monitorear el rendimiento de muchas canalizaciones de modelos de ML complejas.

-------------------------------------

**IMPORTANTE**

```Para desarrollar este proyecto solo necesitas un PC con conexión a internet y un navegador ya que Xploiter te brinda acceso a sus Workspaces donde tendrás acceso a un entorno de desarrollo Full-Stack junto con Kuberntes para las pruebas e implementaciones. ```

-------------------------------------

# Proyecto & Temas a resolver

## Poner en contenedores un servicio de puntuación de modelo de ML simple usando Flask y Docker

```bash
py-flask-ml-score-api/
 | Dockerfile
 | Pipfile
 | Pipfile.lock
 | api.py
```

### Definición del servicio Flask en el módulo `api.py`

```python
from flask import Flask, jsonify, make_response, request

app = Flask(__name__)


@app.route('/score', methods=['POST'])
def score():
    features = request.json['X']
    return make_response(jsonify({'score': features}))


if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### Definición de la imagen de Docker con `Dockerfile`

```docker
FROM python:3.6-slim
WORKDIR /usr/src/app
COPY . .
RUN pip install pipenv
RUN pipenv install
EXPOSE 5000
CMD ["pipenv", "run", "python", "api.py"]
```

### Creación de una imagen de Docker para el servicio de puntuación de ML

```bash
docker build --tag alexioannides/test-ml-score-api py-flask-ml-score-api
```

#### Testing

```bash
docker run --rm --name test-api -p 5000:5000 -d alexioannides/test-ml-score-api
```

#### Empujar la imagen al registro de DockerHub

```bash
docker push alexioannides/test-ml-score-api
```

## Kubernetes para pruebas y desarrollo

```bash
kubectl cluster-info
```

### Implementación del servicio de puntuación de modelos de aprendizaje automático en contenedores en Kubernetes

```bash
kubectl create deployment test-ml-score-api --image=alexioannides/test-ml-score-api:latest
```

## Uso de archivos YAML para definir e implementar el servicio de puntuación de modelos de ML

```bash
kubectl apply -f py-flask-ml-score-api/py-flask-ml-score.yaml
```

## Uso de Helm Charts para definir e implementar el servicio de puntuación de modelos de ML

Escribir archivos YAML para Kubernetes puede volverse repetitivo y difícil de administrar, especialmente si hay mucho 'copiar y pegar' involucrado, cuando solo se necesita cambiar un puñado de parámetros de una implementación a la siguiente.

### Instalando Helm

```bash
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

### Deploying con Helm

```bash
helm create NAME-OF-YOUR-HELM-CHART
```

## Uso de Seldon para implementar el servicio de puntuación de modelos de ML en Kubernetes

La misión principal de Seldon es simplificar la implementación y administración repetidas de canalizaciones de predicción de ML complejas sobre Kubernetes. En esta demostración, nos centraremos en el ejemplo más simple posible, es decir, la API de puntuación del modelo ML simple que ya hemos estado usando

### Creación de un componente ML para Seldon

```bash
seldon-ml-score-component/
 | Dockerfile
 | MLScore.py
 | Pipfile
 | Pipfile.lock
```

#### Creación de la imagen de Docker para usar con Seldon

```python
class MLScore:
    """
    Model template. You can load your model parameters in __init__ from
    a location accessible at runtime
    """
```

### Implementación de un componente ML con Seldon Core

## Tear Down

## A dónde ir desde aquí

### Instalando Pipenv

```bash
pip3 install pipenv
```

### Instalación de dependencias de proyectos

```bash
pipenv install
```

### Pipenv Shells

```bash
pipenv shell
```
