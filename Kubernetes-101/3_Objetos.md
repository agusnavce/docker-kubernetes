# 3. Objetos y Cargas de Trabajo de Kubernetes

## 3.1 Introducción

En Kubernetes, las aplicaciones se despliegan, gestionan y escalan utilizando varios tipos de objetos y cargas de trabajo. Estos objetos representan el estado deseado del sistema y Kubernetes trabaja constantemente para mantener ese estado. Entender estos objetos es fundamental para trabajar eficazmente con Kubernetes.

## 3.2 Conceptos clave

### 3.2.1 Pods

- Un Pod es la unidad más pequeña y básica en Kubernetes.
- Representa un conjunto de uno o más contenedores que se ejecutan juntos en el mismo host.
- Los contenedores en un Pod comparten recursos como almacenamiento y red.
- Los Pods son efímeros por naturaleza y pueden ser reemplazados en cualquier momento.

### 3.2.2 ReplicaSets

- Un ReplicaSet asegura que un número específico de réplicas de un Pod esté ejecutándose en todo momento.
- Proporciona capacidades de auto-reparación y escalado horizontal.
- Se utiliza generalmente a través de Deployments, que ofrecen capacidades adicionales.

### 3.2.3 Deployments

- Un Deployment proporciona actualizaciones declarativas para Pods y ReplicaSets.
- Permite realizar actualizaciones rolling y rollbacks de aplicaciones.
- Gestiona la creación y escalado de ReplicaSets.

### 3.2.4 DaemonSets

- Un DaemonSet asegura que todos (o algunos) nodos ejecuten una copia de un Pod.
- Útil para servicios de cluster como recolectores de logs o agentes de monitoreo.
- A medida que se añaden nodos al cluster, los Pods se añaden a ellos automáticamente.

### 3.2.5 StatefulSets

- Un StatefulSet es utilizado para aplicaciones que requieren un estado persistente.
- Proporciona garantías sobre el orden y la unicidad de los Pods.
- Útil para aplicaciones como bases de datos que requieren identificadores de red estables y almacenamiento persistente.

### 3.2.6 Jobs y CronJobs

- Un Job crea uno o más Pods y se asegura de que un número específico de ellos termine con éxito.
- Útil para tareas batch o de una sola vez.
- Un CronJob crea Jobs en una base programada, similar a las tareas cron en sistemas Unix.

## 3.3 Relaciones entre objetos

- Los Deployments gestionan ReplicaSets, que a su vez gestionan Pods.
- Los DaemonSets y StatefulSets gestionan directamente los Pods, pero con comportamientos específicos.
- Los Jobs y CronJobs crean y gestionan Pods para tareas específicas o programadas.

Entender estas relaciones es crucial para diseñar y gestionar aplicaciones en Kubernetes de manera eficiente.

## Ejercicio: Trabajando con Objetos y Cargas de Trabajo de Kubernetes

### Objetivo
En este ejercicio, vamos a crear y manipular diferentes tipos de objetos y cargas de trabajo en Kubernetes. Trabajaremos con Pods, Deployments, Services y DaemonSets para entender cómo funcionan y se relacionan entre sí.

### Pasos

1. **Crear un Pod simple**

   Crea un archivo llamado `mi-pod.yaml` con el siguiente contenido:

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: mi-pod
   spec:
     containers:
     - name: nginx
       image: nginx:1.14.2
       ports:
       - containerPort: 80
   ```

   Aplica el archivo:
   ```
   kubectl apply -f mi-pod.yaml
   ```

   Verifica que el Pod está corriendo:
   ```
   kubectl get pods
   ```

2. **Crear un Deployment**

   Crea un archivo llamado `mi-deployment.yaml` con el siguiente contenido:

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: mi-deployment
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: mi-app
     template:
       metadata:
         labels:
           app: mi-app
       spec:
         containers:
         - name: nginx
           image: nginx:1.14.2
           ports:
           - containerPort: 80
   ```

   Aplica el archivo:
   ```
   kubectl apply -f mi-deployment.yaml
   ```

   Verifica que el Deployment y sus Pods están corriendo:
   ```
   kubectl get deployments
   kubectl get pods
   ```

3. **Crear un Service**

   Crea un archivo llamado `mi-service.yaml` con el siguiente contenido:

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: mi-service
   spec:
     selector:
       app: mi-app
     ports:
       - protocol: TCP
         port: 80
         targetPort: 80
   ```

   Aplica el archivo:
   ```
   kubectl apply -f mi-service.yaml
   ```

   Verifica que el Service está corriendo:
   ```
   kubectl get services
   ```

4. **Crear un DaemonSet**

   Crea un archivo llamado `mi-daemonset.yaml` con el siguiente contenido:

   ```yaml
   apiVersion: apps/v1
   kind: DaemonSet
   metadata:
     name: mi-daemonset
   spec:
     selector:
       matchLabels:
         name: mi-daemonset
     template:
       metadata:
         labels:
           name: mi-daemonset
       spec:
         containers:
         - name: fluentd
           image: fluentd:v1.7
   ```

   Aplica el archivo:
   ```
   kubectl apply -f mi-daemonset.yaml
   ```

   Verifica que el DaemonSet está corriendo:
   ```
   kubectl get daemonsets
   ```

5. **Escalar el Deployment**

   Escala el Deployment a 5 réplicas:
   ```
   kubectl scale deployment mi-deployment --replicas=5
   ```

   Verifica que ahora hay 5 Pods corriendo:
   ```
   kubectl get pods
   ```

6. **Actualizar el Deployment**

   Edita el archivo `mi-deployment.yaml` y cambia la versión de la imagen de nginx a `1.16.1`.

   Aplica el cambio:
   ```
   kubectl apply -f mi-deployment.yaml
   ```

   Observa el proceso de actualización:
   ```
   kubectl rollout status deployment mi-deployment
   ```

7. **Limpiar**

   Elimina todos los recursos creados:
   ```
   kubectl delete -f mi-pod.yaml
   kubectl delete -f mi-deployment.yaml
   kubectl delete -f mi-service.yaml
   kubectl delete -f mi-daemonset.yaml
   ```

### Preguntas de reflexión

1. ¿Qué diferencias observaste entre crear un Pod directamente y crear un Deployment?
2. ¿Cómo afectó el Service a la forma en que podías acceder a los Pods del Deployment?
3. ¿Qué sucedió cuando escalaste el Deployment? ¿Cómo se reflejó esto en los Pods?
4. ¿Qué ventajas crees que ofrece un DaemonSet sobre un Deployment para ciertos tipos de aplicaciones?
5. Durante la actualización del Deployment, ¿notaste alguna interrupción en el servicio? ¿Por qué crees que es así?

Este ejercicio te ha permitido trabajar con varios objetos clave de Kubernetes, entender cómo se relacionan entre sí y cómo se pueden manipular para gestionar aplicaciones en un cluster de Kubernetes.