# Ejercicio Guiado de Resolución de Problemas en Kubernetes con Comandos de Diagnóstico

## Escenario

Eres un administrador de Kubernetes en una startup de tecnología. El equipo de desarrollo ha creado una nueva aplicación web y te ha proporcionado un conjunto de archivos YAML para desplegarla en el cluster de Kubernetes. Sin embargo, cuando intentas desplegar la aplicación, notas que hay varios problemas. Vamos a resolverlos paso a paso, utilizando comandos de Kubernetes para diagnosticar y verificar cada problema.

## Preparación

Antes de comenzar, asegúrate de tener acceso a un cluster de Kubernetes y que kubectl esté configurado correctamente. Guarda cada archivo YAML en tu directorio de trabajo.

## Parte 1: Corregir el Deployment

Examina el siguiente archivo `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labes:
    app: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: 50Mi
            cpu: 50m
          limits:
            memory: 100Mi
            cpu: 100m
        env:
        - name: DB_PASSWORD
          value: "supersecret"
        - name: APP_COLOR
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_COLOR
```

### Tarea 1: Corrige los errores en el Deployment

Primero, intenta aplicar el Deployment:

```bash
kubectl apply -f deployment.yaml
```

Verás un error debido al typo en las etiquetas. Para diagnosticar más a fondo:

```bash
kubectl get deployments
kubectl describe deployment web-app
```

**Pista:** Hay un error tipográfico en la sección de metadatos y un problema de seguridad con cómo se maneja la contraseña de la base de datos.

Después de corregir el Deployment, aplícalo de nuevo y verifica su estado:

```bash
kubectl apply -f deployment.yaml
kubectl get pods
kubectl describe deployment web-app
```

<details>
<summary>Ver solución</summary>

Corrige el archivo `deployment.yaml` y aplica los cambios:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: 50Mi
            cpu: 50m
          limits:
            memory: 100Mi
            cpu: 100m
        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secrets
              key: DB_PASSWORD
        - name: APP_COLOR
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_COLOR
```

Aplica el Deployment corregido:

```bash
kubectl apply -f deployment.yaml
```

</details>

## Parte 2: Ajustar el Service

Examina el archivo `service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
spec:
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

### Tarea 2: Corrige el Service para que se conecte correctamente con el Deployment

Aplica el Service y verifica su estado:

```bash
kubectl apply -f service.yaml
kubectl get services
kubectl describe service web-app-service
```

Para verificar si el Service está seleccionando los Pods correctamente:

```bash
kubectl get pods --selector=app=frontend
kubectl get pods --selector=app=web-app
```

**Pista:** El selector del Service no coincide con las etiquetas del Deployment, y hay un problema con los puertos.

<details>
<summary>Ver solución</summary>

Corrige el archivo `service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
spec:
  selector:
    app: web-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

Aplica el Service corregido:

```bash
kubectl apply -f service.yaml
kubectl get services
kubectl describe service web-app-service
```

Verifica que los Pods están siendo seleccionados correctamente:

```bash
kubectl get pods --selector=app=web-app
```

</details>

## Parte 3: Configurar el Ingress

Examina el archivo `ingress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - host: myapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-app-service
            port: 
              number: 8080
```

### Tarea 3: Ajusta el Ingress para que se conecte correctamente con el Service

Aplica el Ingress y verifica su estado:

```bash
kubectl apply -f ingress.yaml
kubectl get ingress
kubectl describe ingress web-app-ingress
```

**Pista:** Hay un problema con el número de puerto especificado en el backend.

<details>
<summary>Ver solución</summary>

Corrige el archivo `ingress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - host: myapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-app-service
            port: 
              number: 80
```

Aplica el Ingress corregido:

```bash
kubectl apply -f ingress.yaml
kubectl get ingress
kubectl describe ingress web-app-ingress
```

</details>

## Parte 4: Manejar Secretos de forma segura

### Tarea 4: Crea un archivo Secret para manejar la contraseña de la base de datos

**Pista:** Debes crear un nuevo archivo YAML para el Secret y referenciarlo en el Deployment.

Para verificar los Secrets existentes:

```bash
kubectl get secrets
```

<details>
<summary>Ver solución</summary>

Crea un nuevo archivo llamado `secret.yaml` con el siguiente contenido:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secrets
type: Opaque
data:
  DB_PASSWORD: c3VwZXJzZWNyZXQ=  # "supersecret" codificado en base64
```

Aplica el Secret:

```bash
kubectl apply -f secret.yaml
kubectl get secrets
kubectl describe secret db-secrets
```

Asegúrate de que el Deployment referencia este Secret como se mostró en la solución de la Tarea 1.

Aplica el Deployment actualizado:

```bash
kubectl apply -f deployment.yaml
```

Verifica que el Deployment está utilizando el Secret:

```bash
kubectl describe deployment web-app
```

</details>

## Conclusión

Has completado el ejercicio guiado de resolución de problemas en Kubernetes. A lo largo de este ejercicio, has:

1. Corregido errores en un Deployment
2. Ajustado un Service para que se conecte correctamente con un Deployment
3. Configurado un Ingress para exponer la aplicación
4. Creado un Secret para manejar información sensible de forma segura

Además, has aprendido a utilizar varios comandos de kubectl para diagnosticar y verificar el estado de tus recursos de Kubernetes. Estos son problemas y técnicas de diagnóstico comunes que podrías encontrar al trabajar con Kubernetes en el mundo real.