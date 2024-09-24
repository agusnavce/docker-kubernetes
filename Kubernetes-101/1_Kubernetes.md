# Introducción a Kubernetes

## ¿Qué es Kubernetes?

Kubernetes, comúnmente abreviado como K8s, es una plataforma de código abierto diseñada para automatizar la implementación, el escalado y la gestión de aplicaciones en contenedores. Desarrollado originalmente por Google y ahora mantenido por la Cloud Native Computing Foundation (CNCF), Kubernetes se ha convertido en el estándar de facto para la orquestación de contenedores en entornos de producción.

### Puntos clave:
- Sistema de orquestación de contenedores de nivel empresarial
- Automatiza tareas complejas relacionadas con la implementación y el escalado de aplicaciones
- Gestiona clusters de hosts en los que se ejecutan contenedores de aplicaciones
- Proporciona un framework para sistemas distribuidos resilientes

## Historia y evolución de Kubernetes

- **Orígenes en Google**: Kubernetes se basa en más de 15 años de experiencia de Google con su sistema interno de orquestación llamado Borg.
- **Lanzamiento como proyecto de código abierto**: Google liberó Kubernetes en 2014, marcando el inicio de una nueva era en la gestión de contenedores.
- **Donación a la CNCF**: En 2015, Google donó Kubernetes a la recién formada Cloud Native Computing Foundation, fomentando un ecosistema neutral y diverso.
- **Rápida adopción**: Desde su lanzamiento, Kubernetes ha experimentado un crecimiento exponencial en adopción y contribuciones de la comunidad.
- **Evolución continua**: Con lanzamientos trimestrales, Kubernetes continúa evolucionando y añadiendo nuevas características para satisfacer las necesidades cambiantes de la industria.

## ¿Por qué usar Kubernetes?

Kubernetes ofrece numerosos beneficios que lo hacen atractivo para organizaciones de todos los tamaños:

1. **Escalabilidad dinámica**: Permite escalar aplicaciones y recursos de manera automática basándose en la demanda.
2. **Portabilidad entre nubes**: Facilita la migración de aplicaciones entre diferentes proveedores de nube o infraestructuras on-premise.
3. **Alta disponibilidad**: Implementa mecanismos para garantizar que las aplicaciones estén siempre disponibles y operativas.
4. **Eficiencia de recursos**: Optimiza el uso de recursos de hardware, mejorando la utilización y reduciendo costos.
5. **Despliegue y rollback automáticos**: Simplifica la implementación de nuevas versiones y permite revertir a versiones anteriores de manera rápida y confiable.
6. **Auto-reparación**: Monitorea constantemente la salud de las aplicaciones y puede reiniciar, reemplazar o reprogramar contenedores que fallan.
7. **Gestión de configuración y secretos**: Ofrece mecanismos seguros para almacenar y gestionar información sensible.
8. **Service discovery y load balancing**: Facilita la comunicación entre servicios y distribuye el tráfico de manera eficiente.
9. **Gestión declarativa**: Permite describir el estado deseado del sistema, y Kubernetes se encarga de mantener ese estado.
10. **Comunidad activa**: Cuenta con una de las comunidades de código abierto más grandes y activas, lo que garantiza soporte continuo y mejoras.

## Conceptos clave y arquitectura

### Componentes principales de Kubernetes:

![alt text](Imagenes/arch.png "Arquitectura de Microservicios")

1. **Plano de control (Control Plane)**:
   - **API Server**: Punto de entrada para todas las operaciones en el cluster.
   - **Scheduler**: Asigna pods a nodos basándose en recursos disponibles y políticas.
   - **Controller Manager**: Ejecuta controladores que regulan el estado del cluster.
   - **etcd**: Almacén de datos distribuido que mantiene el estado del cluster.

2. **Nodos de trabajo (Worker Nodes)**:
   - **Kubelet**: Asegura que los contenedores estén ejecutándose en un pod.
   - **Kube-proxy**: Mantiene las reglas de red en los nodos.
   - **Container Runtime**: Software responsable de ejecutar contenedores (e.g., Docker, containerd).



### Objetos básicos de Kubernetes:

1. **Pods**: La unidad más pequeña y básica en Kubernetes, que encapsula uno o más contenedores.
2. **Services**: Abstracción que define un conjunto lógico de Pods y una política de acceso.
3. **Volumes**: Directorio accesible a los contenedores en un pod, que puede persistir más allá del ciclo de vida de un pod.
4. **Namespaces**: Mecanismo para dividir los recursos del cluster entre múltiples usuarios o proyectos.
5. **Deployments**: Describen el estado deseado para Pods y ReplicaSets, facilitando actualizaciones declarativas.
6. **StatefulSets**: Gestionan el despliegue y escalado de un conjunto de Pods, con garantías sobre el orden y la unicidad.
7. **DaemonSets**: Aseguran que todos (o algunos) nodos ejecuten una copia de un Pod específico.

## Ejercicio práctico: Desplegando una aplicación web en Minikube

En este ejercicio, vamos a desplegar una aplicación web simple en un cluster local de Kubernetes usando Minikube. Este proceso nos ayudará a entender los conceptos básicos de Kubernetes en la práctica.

### Paso 1: Instalación de herramientas

Primero, asegúrate de tener instalados Minikube y kubectl. Si no los tienes, sigue estos pasos:

1. Instalar Minikube:
   https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Farm64%2Fstable%2Fbinary+download

2. Instalar kubectl:
   https://kubernetes.io/docs/tasks/tools/

### Paso 2: Iniciar Minikube

Inicia tu cluster local de Kubernetes:

```bash
minikube start
```

Verifica que el cluster está funcionando:

```bash
kubectl get nodes
```

### Paso 3: Crear un Deployment

Vamos a crear un Deployment para una aplicación web simple. Crea un archivo llamado `web-deployment.yml` con el siguiente contenido:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web
        image: nginx:alpine
        ports:
        - containerPort: 80
```

Aplica el Deployment:

```bash
kubectl apply -f web-deployment.yml
```

### Paso 4: Crear un Service

Para exponer nuestra aplicación, creamos un Service. Crea un archivo `web-service.yml`:

```yml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
  type: NodePort
```

Aplica el Service:

```bash
kubectl apply -f web-service.yml
```

### Paso 5: Verificar el despliegue

Verifica que los Pods están ejecutándose:

```bash
kubectl get pods
```

Obtén la URL de tu servicio:

```bash
minikube service web-service --url
```

Abre esta URL en tu navegador para ver la página por defecto de Nginx.

### Paso 6: Escalar la aplicación

Escala el Deployment a 5 réplicas:

```bash
kubectl scale deployment web-app --replicas=5
```

Verifica que se han creado nuevos Pods:

```bash
kubectl get pods
```

### Paso 7: Actualizar la aplicación

Edita `web-deployment.yml` y cambia `nginx:alpine` a `httpd:alpine`. Luego, aplica los cambios:

```bash
kubectl apply -f web-deployment.yml
```

Observa el proceso de actualización:

```bash
kubectl rollout status deployment/web-app
```

### Paso 8: Limpieza

Cuando hayas terminado, elimina los recursos creados:

```bash
kubectl delete -f web-deployment.yml
kubectl delete -f web-service.yml
```

Detén Minikube:

```bash
minikube stop
```