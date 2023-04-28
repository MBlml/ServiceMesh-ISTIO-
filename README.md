# Service Mesh, principalmente de Istio
#### BARAJAS GOMEZ JUAN MANUEL | 216557005 | COMPUTACION TOLERANTE A FALLAS | 28/04/2023

### OBJETIVO:
Genera un reporte y ejemplo sobre Service Mesh, principalmente de Istio

### DESARROLLO:
#### ¿Que es Istio?
Istio es una plataforma de servicio de malla de servicios (service mesh) de código abierto que se utiliza para simplificar la conexión, el aseguramiento y la administración de servicios en un entorno de nube y microservicios. Istio ofrece un conjunto de características para el tráfico de red, la seguridad, la observabilidad y el control de tráfico de servicios.

#### Ejemplo practico:
Primero debemos crear un archivo de especificación de despliegue para una aplicación de prueba en Node.js:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-app
spec:
  selector:
    matchLabels:
      app: test-app
  replicas: 2
  template:
    metadata:
      labels:
        app: test-app
    spec:
      containers:
      - name: test-app
        image: node:latest
        ports:
        - containerPort: 3000
        command: ["node"]
        args: ["app.js"]
```
Este archivo creará un despliegue llamado test-app con dos réplicas. La imagen utilizada es la última versión de Node.js y se espera que la aplicación esté escuchando en el puerto 3000.

Luego, debemos crear un archivo de servicio para exponer la aplicación:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: test-app
spec:
  selector:
    app: test-app
  ports:
  - name: http
    port: 80
    targetPort: 3000
  type: ClusterIP
```
Este archivo creará un servicio llamado test-app que se encargará de enrutar el tráfico al puerto 3000 de las réplicas del despliegue.

Seguido de esto, creamos un archivo de gateway para Istio:
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: test-app-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
```

Este archivo creará un gateway llamado test-app-gateway que escuchará en el puerto 80 de Istio y enrutará el tráfico a cualquier host.

Despues, creamos un archivo de virtualservice para Istio:
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: test-app-virtual-service
spec:
  hosts:
  - "*"
  gateways:
  - test-app-gateway
  http:
  - match:
    - uri:
        prefix: /
    route:
    - destination:
        host: test-app
        port:
          number: 80
```
Este archivo creará un virtualservice llamado test-app-virtual-service que enruta todo el tráfico HTTP recibido por el gateway a la aplicación test-app.

Finalmente aplicamos todos los archivos creados:
```
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f gateway.yaml
kubectl apply -f virtualservice.yaml
```
Con estos pasos, se pudo crear una aplicación de prueba en Node.js y hemos utilizado Istio para balancear el tráfico entre las réplicas del despliegue. Solo un ejemplo básico, pero muestra cómo Istio puede ser utilizado para mejorar la gestión y seguridad del tráfico en una aplicación de Kubernetes.

### CONCLUSIÓN:
En conclusión, Istio es una herramienta de servicio de malla que se utiliza para mejorar la comunicación entre los servicios y aplicaciones en una infraestructura de nube. Proporciona una gran cantidad de características de seguridad, observabilidad, control de tráfico y gestión de servicios, lo que facilita el despliegue y la administración de aplicaciones en un entorno de nube distribuido y complejo. Con Istio, los desarrolladores y los equipos de operaciones pueden concentrarse en sus tareas principales de desarrollo de aplicaciones y dejar que la herramienta maneje la complejidad de la infraestructura subyacente. En resumen, Istio permite a las organizaciones crear, ejecutar y administrar aplicaciones de forma más segura, escalable y eficiente en un entorno de nube complejo.

### REFERENCIAS:
_Pelado Nerd. (2021, 16 febrero). Introducción a ISTIO / Service Mesh [Vídeo]. YouTube. https://www.youtube.com/watch?v=ofJ5swfP2kQ_
