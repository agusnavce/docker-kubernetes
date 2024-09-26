# Ejercicio de Construcción de Aplicación Completa en Kubernetes con Minikube

## Escenario

Eres un desarrollador en una startup de comercio electrónico. Tu tarea es construir y desplegar una aplicación de carrito de compras en Kubernetes utilizando Minikube. La aplicación constará de un frontend, un backend API, una base de datos, y varios servicios auxiliares.

## Configuración Inicial

1. Inicia Minikube:
   ```
   minikube start
   ```

2. Habilita el addon Ingress:
   ```
   minikube addons enable ingress
   ```

3. Configura Docker para usar el daemon Docker de Minikube:
   ```
   eval $(minikube docker-env)
   ```

## Parte 1: Componente del Backend API

¿Qué recurso de Kubernetes es más adecuado para desplegar el backend API de la aplicación?

A) Pod
B) Deployment
C) StatefulSet
D) DaemonSet

<details>
<summary>Ver respuesta correcta</summary>

La respuesta correcta es B) Deployment.

Un Deployment es ideal para aplicaciones sin estado como un backend API. Proporciona capacidades de escalado, actualizaciones rolling y rollbacks, que son cruciales para mantener la disponibilidad y facilitar las actualizaciones de la aplicación.
</details>

### Tarea:
Completa el siguiente template de Deployment para el backend API. Asegúrate de usar la imagen que esta dentro de la carpeta Ejercicio.

### Pistas para completar el Deployment:

1. Recuerda especificar la imagen correcta y su política de extracción (pull policy).
2. La aplicación está escuchando en el puerto 5000, asegúrate de exponer este puerto.
3. Para trabajar con imágenes locales en Minikube, necesitas una configuración específica de la política de extracción.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: shopping-api
spec:
  replicas: 2
  selector:
    matchLabels:
      app: shopping-api
  template:
    metadata:
      labels:
        app: shopping-api
    spec:
      containers:
      - name: shopping-api
        # Completa las especificaciones aquí
```

### Solución:

<details>
    <summary>Solución</summary>

1. Construye la imagen Docker:

```bash
docker build -t shopping-api:v1 .
```

5. Completa el Deployment YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: shopping-api
spec:
  replicas: 2
  selector:
    matchLabels:
      app: shopping-api
  template:
    metadata:
      labels:
        app: shopping-api
    spec:
      containers:
      - name: shopping-api
        image: shopping-api:v1
        imagePullPolicy: Never
        ports:
        - containerPort: 5000
```

Aplica el Deployment:

```bash
kubectl apply -f backend-deployment.yaml
```
</details>

#### Preguntas
1. ¿Qué sucedería si no especificamos un `selector` en el Deployment?

   A) Kubernetes asignaría automáticamente un selector basado en el nombre del Deployment
   B) El Deployment no podría crearse y Kubernetes arrojaría un error
   C) Todos los pods en el namespace serían gestionados por este Deployment
   D) El Deployment se crearía pero no gestionaría ningún pod

   <details>
   <summary>Ver respuesta correcta</summary>

   La respuesta correcta es B) El Deployment no podría crearse y Kubernetes arrojaría un error.

   El `selector` es un campo obligatorio en la especificación del Deployment. Define cómo el Deployment identifica los pods que debe gestionar. Sin un selector, Kubernetes no sabría qué pods están asociados con el Deployment, por lo que no permitiría su creación.
   </details>

2. Si aumentamos el número de `replicas` en el Deployment de 2 a 5, ¿qué acción tomará Kubernetes?

   A) Eliminará los pods existentes y creará 5 nuevos
   B) Creará 3 pods adicionales, manteniendo los 2 existentes
   C) Ignorará el cambio hasta que se reinicie el cluster
   D) Mostrará un error indicando que no se puede cambiar el número de réplicas

   <details>
   <summary>Ver respuesta correcta</summary>

   La respuesta correcta es B) Creará 3 pods adicionales, manteniendo los 2 existentes.

   Kubernetes siempre intenta alcanzar y mantener el estado deseado especificado en el Deployment. Al aumentar el número de réplicas, Kubernetes creará pods adicionales para alcanzar el nuevo número deseado, sin afectar a los pods existentes que ya están en funcionamiento.
   </details>

3. En el contexto de un Deployment de Kubernetes, ¿qué significa tener múltiples réplicas?

   A) Crear copias de seguridad de la aplicación
   B) Ejecutar múltiples instancias idénticas de la aplicación para alta disponibilidad y escalabilidad
   C) Duplicar el código fuente de la aplicación
   D) Crear múltiples versiones diferentes de la aplicación

   <details>
   <summary>Ver respuesta correcta</summary>

   La respuesta correcta es B) Ejecutar múltiples instancias idénticas de la aplicación para alta disponibilidad y escalabilidad.

   Al especificar múltiples réplicas en un Deployment, Kubernetes crea y mantiene varias instancias idénticas de tu aplicación. Esto proporciona alta disponibilidad (si una instancia falla, las otras siguen funcionando) y permite la escalabilidad horizontal (puedes aumentar o disminuir el número de réplicas según la demanda).
   </details>


## Parte 2: Componente de Base de Datos

Para una base de datos que requiere almacenamiento persistente y nombres de host estables, ¿qué recurso de Kubernetes deberías usar?

A) Deployment
B) ReplicaSet
C) StatefulSet
D) Job

<details>
<summary>Ver respuesta correcta</summary>

La respuesta correcta es C) StatefulSet.

Los StatefulSets son ideales para aplicaciones que requieren identidades de red estables, ordenamiento predecible de pods y almacenamiento persistente, características cruciales para la mayoría de las bases de datos.
</details>

### Tarea:
Completa el siguiente template de StatefulSet para una base de datos PostgreSQL.

### Pistas para Completar el StatefulSet de PostgreSQL

1. **Puertos**:
   - PostgreSQL usa un puerto específico por defecto. ¿Cuál es y cómo lo expondrías en el contenedor?

2. **Variables de Entorno**:
   - PostgreSQL necesita una contraseña para el usuario root. ¿Cómo podrías proporcionar esta de manera segura?

3. **Persistencia**:
   - Los datos de PostgreSQL deben persistir incluso si el pod se reinicia. ¿Dónde almacena PostgreSQL sus datos por defecto?
   - ¿Cómo podrías asegurarte de que este directorio se mantenga persistente?

4. **Volumen**:
   - El `volumeClaimTemplates` ya está definido en el YAML proporcionado. ¿Cómo lo conectarías con el contenedor de PostgreSQL?

5. **Imagen**:
   - La imagen de PostgreSQL ya está especificada. ¿Necesitas agregar algún tag específico o la versión por defecto es suficiente?

6. **Seguridad**:
   - ¿Hay alguna consideración de seguridad adicional que deberías tener en cuenta al ejecutar una base de datos en Kubernetes?

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: shopping-api-postgres
spec:
  serviceName: "postgres"
  replicas: 1
  selector:
    matchLabels:
      app: shopping-api
  template:
    metadata:
      labels:
        app: shopping-api
    spec:
      containers:
      - name: postgres
        image: postgres:13
        # Completa las especificaciones aquí
```

<details>
<summary>Ver respuesta correcta</summary>

1. Crea un Secret para las credenciales de la base de datos:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: postgres-secret
type: Opaque
stringData:
  POSTGRES_PASSWORD: mypassword
```

Aplica el Secret:

```bash
kubectl apply -f postgres-secret.yaml
```

2. Completa el StatefulSet YAML:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
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
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: POSTGRES_PASSWORD
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
```

Aplica el StatefulSet:

```bash
kubectl apply -f postgres-statefulset.yaml
```
</details>

#### Preguntas:

1. ¿Cuál es la principal diferencia entre un Deployment y un StatefulSet?

   A) Los StatefulSets solo se usan para bases de datos
   B) Los Deployments no pueden tener volúmenes persistentes
   C) Los StatefulSets mantienen una identidad de red estable para cada pod
   D) Los Deployments son más rápidos de escalar que los StatefulSets

   <details>
   <summary>Ver respuesta correcta</summary>

   La respuesta correcta es C) Los StatefulSets mantienen una identidad de red estable para cada pod.

   Los StatefulSets se utilizan para aplicaciones que requieren identificadores de red estables, ordenamiento predecible de pods y almacenamiento persistente. Esto los hace ideales para bases de datos y otras aplicaciones con estado, aunque no se limitan solo a bases de datos.
   </details>

2. ¿Por qué es importante usar un Secret para almacenar la contraseña de la base de datos en lugar de incluirla directamente en el StatefulSet?

   A) Los Secrets son más fáciles de actualizar que los StatefulSets
   B) Los Secrets proporcionan encriptación y mejoran la seguridad de los datos sensibles
   C) Kubernetes requiere el uso de Secrets para todas las contraseñas
   D) Los Secrets permiten compartir la misma contraseña entre múltiples pods

   <details>
   <summary>Ver respuesta correcta</summary>

   La respuesta correcta es B) Los Secrets proporcionan encriptación y mejoran la seguridad de los datos sensibles.

   Los Secrets están diseñados para almacenar y gestionar información sensible, como contraseñas. Proporcionan un nivel adicional de seguridad al encriptar los datos en reposo y limitar el acceso a esta información sensible. Esto es crucial para mantener la seguridad de las credenciales de la base de datos.
   </details>

3. En el contexto de un StatefulSet para una base de datos, ¿qué proporciona el `volumeClaimTemplate`?

   A) Una forma de compartir datos entre todos los pods del StatefulSet
   B) Un mecanismo para hacer copias de seguridad automáticas de la base de datos
   C) Una plantilla para crear volúmenes persistentes para cada réplica del StatefulSet
   D) Un método para limitar el espacio de almacenamiento usado por la base de datos

   <details>
   <summary>Ver respuesta correcta</summary>

   La respuesta correcta es C) Una plantilla para crear volúmenes persistentes para cada réplica del StatefulSet.

   El `volumeClaimTemplate` en un StatefulSet define cómo se deben crear los Persistent Volume Claims (PVCs) para cada réplica del StatefulSet. Esto asegura que cada instancia de la base de datos tenga su propio almacenamiento persistente, manteniendo así la integridad y la persistencia de los datos incluso si los pods se reinician o se mueven a diferentes nodos.
   </details>

## Parte 3: Componente del Frontend

### Pregunta:
¿Qué recurso de Kubernetes es más apropiado para manejar la configuración externa de una aplicación, como URLs de API?

A) Secret
B) ConfigMap
C) PersistentVolume
D) ServiceAccount

<details>
<summary>Ver respuesta correcta</summary>

La respuesta correcta es B) ConfigMap.

Los ConfigMaps son ideales para almacenar datos de configuración no confidenciales en formato clave-valor. Son perfectos para manejar configuraciones como URLs de API, que pueden necesitar cambios sin requerir una reconstrucción de la imagen del contenedor.
</details>

### Tarea:
Completa el siguiente template de Deployment para el frontend. Usa un ConfigMap para la URL del API.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: shopping-frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: shopping-api
  template:
    metadata:
      labels:
        app: shopping-api
    spec:
      containers:
      - name: shopping-frontend
        # Completa las especificaciones aquí
```

1. Construye la imagen Docker:

```bash
docker build -t shopping-frontend:v1 frontend
```

4. Crea un ConfigMap para la URL del API:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: frontend-config
data:
  API_URL: "http://shopping-api-service:5000"
```

Aplica el ConfigMap:

```bash
kubectl apply -f frontend-config.yaml
```

5. Completa el Deployment YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: shopping-api
spec:
  replicas: 2
  selector:
    matchLabels:
      app: shopping-api
  template:
    metadata:
      labels:
        app: shopping-api
    spec:
      containers:
      - name: shopping-frontend
        image: shopping-frontend:v1
        imagePullPolicy: Never
        ports:
        - containerPort: 80
        env:
        - name: API_URL
          valueFrom:
            configMapKeyRef:
              name: frontend-config
              key: API_URL
```

Aplica el Deployment:

```bash
kubectl apply -f frontend-deployment.yaml
```

### Pregunta:
¿Cuál es la ventaja principal de usar un ConfigMap para la URL del API en lugar de codificarla directamente en el Deployment?

A) Los ConfigMaps son más seguros que las variables de entorno en el Deployment
B) Permite cambiar la configuración sin necesidad de reconstruir o redesplegar la aplicación
C) Los ConfigMaps mejoran el rendimiento de la aplicación
D) Kubernetes requiere el uso de ConfigMaps para variables de entorno

<details>
<summary>Ver respuesta correcta</summary>

La respuesta correcta es B) Permite cambiar la configuración sin necesidad de reconstruir o redesplegar la aplicación.

Los ConfigMaps permiten separar la configuración del código de la aplicación, lo que facilita la modificación de parámetros como URLs sin necesidad de cambiar la imagen del contenedor o redesplegar la aplicación completa. Esto proporciona mayor flexibilidad y facilita la gestión de diferentes entornos.
</details>

## Parte 4: Exposición de Servicios

¿Qué recurso de Kubernetes deberías usar para exponer tu aplicación fuera del cluster?

A) Pod
B) Deployment
C) Service
D) ConfigMap

<details>
<summary>Ver respuesta correcta</summary>

La respuesta correcta es C) Service.

Los Services en Kubernetes son utilizados para exponer aplicaciones dentro del cluster y, dependiendo del tipo de Service, también fuera del cluster. Proporcionan una abstracción que define un conjunto lógico de Pods y una política para acceder a ellos.
</details>

### Tarea:
Crea Services para el backend y frontend, y un Ingress para exponer el frontend.

### Solución:

1. Crea un Service para el backend:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: shopping-api-service
spec:
  selector:
    app: shopping-api
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
```

2. Crea un Service para el frontend:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: shopping-frontend-service
spec:
  selector:
    app: shopping-frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

3. Crea un Ingress:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: shopping-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: shopping-frontend-service
            port: 
              number: 80
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: shopping-api-service
            port: 
              number: 5000
```

Aplica estos recursos:

```bash
kubectl apply -f backend-service.yaml
kubectl apply -f frontend-service.yaml
kubectl apply -f ingress.yaml
```

### Preguntas Finales:

1. ¿Por qué usamos un Service de tipo ClusterIP para el backend y NodePort para el frontend?

   A) ClusterIP es más rápido que NodePort
   B) NodePort permite el acceso desde fuera del cluster, mientras que ClusterIP es solo para acceso interno
   C) ClusterIP usa menos recursos que NodePort
   D) NodePort es el único tipo de Service que funciona con aplicaciones web

   <details>
   <summary>Ver respuesta correcta</summary>

   La respuesta correcta es B) NodePort permite el acceso desde fuera del cluster, mientras que ClusterIP es solo para acceso interno.

   ClusterIP expone el Service en una IP interna del cluster, lo cual es suficiente para el backend que solo necesita ser accesible por otros componentes dentro del cluster. NodePort, por otro lado, expone el Service en cada IP de nodo en un puerto estático, permitiendo el acceso desde fuera del cluster, lo cual es necesario para el frontend.
   </details>

2. ¿Qué ventaja proporciona usar Services en lugar de acceder directamente a los Pods?

   A) Los Services son más rápidos que los Pods
   B) Los Services proporcionan una abstracción que permite el descubrimiento y balanceo de carga
   C) Los Services usan menos recursos del sistema
   D) Los Services son el único modo de comunicación entre Pods en Kubernetes

   <details>
   <summary>Ver respuesta correcta</summary>

   La respuesta correcta es B) Los Services proporcionan una abstracción que permite el descubrimiento y balanceo de carga.

   Los Services proporcionan una abstracción estable para un conjunto de Pods, permitiendo el descubrimiento de servicios y el balanceo de carga. Esto es crucial para mantener la estabilidad y la escalabilidad de las aplicaciones en Kubernetes, ya que los Pods pueden ser creados, destruidos o movidos dinámicamente.
   </details>

3. En el contexto de Minikube, ¿cómo podrías acceder al frontend Service desde tu máquina local?

   A) Usando la IP del cluster directamente
   B) A través de un Ingress
   C) Usando el comando `minikube service shopping-frontend-service`
   D) Los Services en Minikube no son accesibles desde fuera del cluster

   <details>
   <summary>Ver respuesta correcta</summary>

   La respuesta correcta es C) Usando el comando `minikube service shopping-frontend-service`.

   Minikube proporciona el comando `minikube service` que permite acceder fácilmente a los Services de tipo NodePort o LoadBalancer desde fuera del cluster. Este comando configura automáticamente el reenvío de puertos necesario y proporciona una URL accesible desde tu máquina local.
   </details>