# Ejercicio Despliegue de una Aplicación Web Completa

## Escenario

Eres el DevOps engineer en una startup que ha desarrollado una aplicación web de comercio electrónico. La aplicación consiste en un frontend, un backend API, una base de datos, un sistema de logging, y algunas tareas programadas para mantenimiento. Tu tarea es desplegar esta aplicación en Kubernetes, aprovechando los diferentes objetos que hemos aprendido.

## Componentes de la Aplicación

1. Frontend (React): Deployment
2. Backend API (Node.js): Deployment
3. Base de datos (PostgreSQL): StatefulSet
4. Sistema de logging (Fluentd): DaemonSet
5. Tarea de limpieza de la base de datos: CronJob
6. Tarea de generación de informes: Job

## Pasos del Ejercicio

### 1. Crear un Namespace

Primero, crearemos un namespace para nuestra aplicación.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ecommerce
```

Guarda esto como `namespace.yaml` y aplícalo:

```bash
kubectl apply -f namespace.yaml
```

Después de aplicar el namespace, verifica su creación:

```bash
kubectl get namespaces
```

**Pregunta**: ¿Por qué es útil crear un namespace separado para nuestra aplicación?


<details>
<summary>Solución</summary>
<p>Los namespaces ayudan a organizar y aislar los recursos en un cluster. Esto es especialmente útil en entornos compartidos o para separar diferentes aplicaciones o entornos (como desarrollo, pruebas y producción).</p>
</details>

### 2. Desplegar la Base de Datos (StatefulSet)

Crea un archivo `postgres-statefulset.yaml`:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
  namespace: ecommerce
spec:
  serviceName: "postgres"
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:13
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_DB
          value: ecommercedb
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-password
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: postgres-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
---
apiVersion: v1
kind: Service
metadata:
  name: postgres
  namespace: ecommerce
spec:
  selector:
    app: postgres
  ports:
    - protocol: TCP
      port: 5432
      targetPort: 5432
```

Crea un Secret para la contraseña de PostgreSQL:

```bash
kubectl create secret generic postgres-secret --from-literal=postgres-password=mypassword -n ecommerce
```

Aplica el StatefulSet:

```bash
kubectl apply -f postgres-statefulset.yaml
```

Después de aplicar el StatefulSet, verifica su creación:

```bash
kubectl get statefulsets -n ecommerce
kubectl get pods -n ecommerce -l app=postgres
kubectl get pvc -n ecommerce
```

**Pregunta**: ¿Por qué usamos un StatefulSet para la base de datos en lugar de un Deployment?

<details>
<summary>Solución</summary>
<p>
 Los StatefulSets son ideales para aplicaciones que requieren identificadores de red estables, almacenamiento persistente y despliegue ordenado. Las bases de datos necesitan estas características para mantener la integridad de los datos y manejar correctamente las réplicas.</p>
</details>

### 3. Desplegar el Backend API (Deployment)

Crea un archivo `backend-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: ecommerce
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: your-backend-image:v1
        ports:
        - containerPort: 3000
        env:
        - name: DB_HOST
          value: postgres
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-password
---
apiVersion: v1
kind: Service
metadata:
  name: backend
  namespace: ecommerce
spec:
  selector:
    app: backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
```

Aplica el Deployment:

```bash
kubectl apply -f backend-deployment.yaml
```

Después de aplicar el Deployment, verifica su creación:

```bash
kubectl get deployments -n ecommerce
kubectl get pods -n ecommerce -l app=backend
kubectl get services -n ecommerce
```

**Pregunta**: ¿Cómo se comunica el backend con la base de datos PostgreSQL?

<details>
<summary>Solución</summary>
<p>El backend se comunica con la base de datos utilizando el nombre del Service de PostgreSQL como hostname. Esto se configura en las variables de entorno del Deployment del backend (DB_HOST: postgres).
</p></details>

### 4. Desplegar el Frontend (Deployment)

Crea un archivo `frontend-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: ecommerce
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: your-frontend-image:v1
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: frontend
  namespace: ecommerce
spec:
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

Aplica el Deployment:

```bash
kubectl apply -f frontend-deployment.yaml
```

Después de aplicar el Deployment, verifica su creación:

```bash
kubectl get deployments -n ecommerce
kubectl get pods -n ecommerce -l app=frontend
kubectl get services -n ecommerce
```

**Pregunta**: ¿Qué tipo de Service estamos usando para el frontend y por qué?

<details>
<summary>Solución</summary>
<p>: Estamos usando un Service de tipo LoadBalancer para el frontend. Esto permite que el frontend sea accesible desde fuera del cluster, lo cual es necesario para que los usuarios puedan acceder a la aplicación web.</p></details>

### 5. Configurar el Sistema de Logging (DaemonSet)

Crea un archivo `fluentd-daemonset.yaml`:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: ecommerce
  labels:
    app: fluentd
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1-debian-elasticsearch
        env:
          - name: FLUENT_ELASTICSEARCH_HOST
            value: "elasticsearch"
          - name: FLUENT_ELASTICSEARCH_PORT
            value: "9200"
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

Aplica el DaemonSet:

```bash
kubectl apply -f fluentd-daemonset.yaml
```

Después de aplicar el DaemonSet, verifica su creación:

```bash
kubectl get daemonsets -n ecommerce
kubectl get pods -n ecommerce -l app=fluentd
```

**Pregunta**: ¿Por qué usamos un DaemonSet para el sistema de logging?

<details>
<summary>Solución</summary>
<p> Un DaemonSet asegura que un Pod se ejecute en todos (o algunos) nodos del cluster. Esto es ideal para sistemas de logging, ya que queremos recoger logs de cada nodo en el cluster.</p></details>

### 6. Crear una Tarea de Limpieza de la Base de Datos (CronJob)

Crea un archivo `db-cleanup-cronjob.yaml`:

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: db-cleanup
  namespace: ecommerce
spec:
  schedule: "0 1 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: db-cleanup
            image: your-db-cleanup-image:v1
            env:
            - name: DB_HOST
              value: postgres
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: postgres-secret
                  key: postgres-password
          restartPolicy: OnFailure
```

Aplica el CronJob:

```bash
kubectl apply -f db-cleanup-cronjob.yaml
```

Después de aplicar el CronJob, verifica su creación:

```bash
kubectl get cronjobs -n ecommerce
```

**Pregunta**: ¿Con qué frecuencia se ejecutará esta tarea de limpieza?

<details>
<summary>Solución</summary>
<p> La tarea de limpieza se ejecutará todos los días a la 1:00 AM, según la programación cron "0 1 * * *".</p></details>

### 7. Crear una Tarea de Generación de Informes (Job)

Crea un archivo `report-generation-job.yaml`:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: report-generation
  namespace: ecommerce
spec:
  template:
    spec:
      containers:
      - name: report-generation
        image: your-report-generation-image:v1
        env:
        - name: DB_HOST
          value: postgres
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-password
      restartPolicy: Never
  backoffLimit: 4
```

Aplica el Job:

```bash
kubectl apply -f report-generation-job.yaml
```

Después de aplicar el Job, verifica su creación:

```bash
kubectl get jobs -n ecommerce
kubectl get pods -n ecommerce -l job-name=report-generation
```

## Verificación y Pruebas

1. Verifica que todos los componentes estén funcionando:

```bash
kubectl get all -n ecommerce
```

2. Comprueba los logs del backend:

```bash
kubectl logs -l app=backend -n ecommerce
```

3. Accede al frontend (si estás usando minikube):

```bash
minikube service frontend -n ecommerce
```

4. Verifica que el CronJob esté programado:

```bash
kubectl get cronjobs -n ecommerce
```

5. Comprueba el estado del Job de generación de informes:

```bash
kubectl get jobs -n ecommerce
```

6. Verifica que el DaemonSet de Fluentd esté ejecutándose en todos los nodos:

```bash
kubectl get pods -l app=fluentd -n ecommerce -o wide
```

## Limpieza

Cuando hayas terminado, puedes eliminar todos los recursos creados:

```bash
kubectl delete namespace ecommerce
```

Este comando eliminará todos los recursos dentro del namespace.

## Conclusión

Este ejercicio integral te ha permitido utilizar todos los objetos principales de Kubernetes que hemos cubierto:

## Verificación y Pruebas

Además de los comandos de verificación proporcionados en cada sección, aquí hay algunas pruebas adicionales que puedes realizar:

1. Verifica la conectividad entre el backend y la base de datos:

```bash
kubectl exec -it $(kubectl get pod -l app=backend -n ecommerce -o jsonpath='{.items[0].metadata.name}') -n ecommerce -- curl postgres:5432
```

Si la conexión es exitosa, deberías ver un mensaje de error de PostgreSQL, lo que indica que el backend puede alcanzar la base de datos.</p></details>

2. Comprueba que el frontend puede comunicarse con el backend:

```bash
kubectl exec -it $(kubectl get pod -l app=frontend -n ecommerce -o jsonpath='{.items[0].metadata.name}') -n ecommerce -- curl backend
```

Deberías ver una respuesta del backend, lo que indica que la comunicación entre frontend y backend está funcionando.

3. Verifica que el CronJob está programado correctamente:

```bash
kubectl get cronjob db-cleanup -n ecommerce -o jsonpath='{.spec.schedule}'
```

Esto debería mostrar la programación del CronJob.

4. Comprueba los logs del sistema de logging:

```bash
kubectl logs $(kubectl get pod -l app=fluentd -n ecommerce -o jsonpath='{.items[0].metadata.name}') -n ecommerce
```

Deberías ver logs relacionados con la recopilación de logs del sistema.

En una aplicación del mundo real, los diferentes objetos de Kubernetes trabajan juntos para proporcionar una infraestructura completa y robusta. Los Deployments manejan las partes de la aplicación que necesitan escalabilidad y actualizaciones fáciles (como el frontend y el backend). Los StatefulSets se utilizan para componentes que requieren estado y almacenamiento persistente (como bases de datos). Los DaemonSets aseguran que ciertos Pods se ejecuten en todos los nodos (útil para logging y monitoreo). Los Jobs y CronJobs manejan tareas únicas y recurrentes respectivamente. Los Services proporcionan abstracción de red y descubrimiento de servicios. Todos estos componentes trabajan juntos para crear una aplicación completa y escalable.


Has desplegado una aplicación web completa con todos sus componentes, demostrando cómo estos objetos de Kubernetes trabajan juntos en un escenario del mundo real.

| [&lt;-- Volver](2_Objetos.md) |