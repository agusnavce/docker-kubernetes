# 1. Introducción a Kubernetes

## 1.1 ¿Qué es Kubernetes?

Kubernetes, a menudo abreviado como K8s, es una plataforma de código abierto para automatizar la implementación, el escalado y la gestión de aplicaciones en contenedores. Fue originalmente diseñado por Google y ahora es mantenido por la Cloud Native Computing Foundation (CNCF).

### Puntos clave:
- Sistema de orquestación de contenedores
- Automatiza muchas tareas manuales involucradas en la implementación y escalado de aplicaciones
- Permite gestionar clusters de hosts en los que se ejecutan contenedores

## 1.2 ¿Por qué usar Kubernetes?

Kubernetes ofrece varios beneficios que lo hacen atractivo para organizaciones de todos los tamaños:

1. **Escalabilidad**: Permite escalar aplicaciones y recursos de manera fácil y rápida.
2. **Portabilidad**: Las aplicaciones pueden moverse fácilmente entre diferentes proveedores de nube o infraestructuras on-premise.
3. **Alta disponibilidad**: Proporciona mecanismos para garantizar que las aplicaciones estén siempre disponibles.
4. **Eficiencia de recursos**: Optimiza el uso de recursos de hardware.
5. **Despliegue y rollback automáticos**: Facilita la implementación de nuevas versiones y la reversión a versiones anteriores si es necesario.
6. **Auto-reparación**: Puede reiniciar contenedores que fallan, reemplazar y reprogramar contenedores cuando los nodos mueren.
7. **Gestión de configuración y secretos**: Permite almacenar y gestionar información sensible.

## 1.3 Conceptos clave y arquitectura

### 1.3.1 Componentes principales de Kubernetes:

1. **Nodo maestro (Master Node)**:
   - API Server: Punto de entrada para todas las operaciones en el cluster.
   - Scheduler: Asigna pods a nodos.
   - Controller Manager: Gestiona controladores que regulan el estado del cluster.
   - etcd: Almacén de datos distribuido para mantener el estado del cluster.

2. **Nodos de trabajo (Worker Nodes)**:
   - Kubelet: Asegura que los contenedores estén funcionando en un pod.
   - Kube-proxy: Mantiene las reglas de red en los nodos.
   - Container Runtime: Software responsable de ejecutar contenedores (como Docker).

### 1.3.2 Objetos básicos de Kubernetes:

1. **Pods**: La unidad más pequeña en Kubernetes, contiene uno o más contenedores.
2. **Services**: Abstracción que define un conjunto lógico de Pods y una política de acceso.
3. **Volumes**: Directorio accesible a los contenedores en un pod.
4. **Namespaces**: Forma de dividir los recursos del cluster entre múltiples usuarios.

### 1.3.3 Arquitectura de alto nivel:

```
+--------------------------------------------------+
|                  Cluster Kubernetes              |
|                                                  |
|  +----------------+        +------------------+  |
|  |  Nodo Maestro  |        |  Nodo de Trabajo |  |
|  |                |        |                  |  |
|  | +------------+ |        | +--------------+ |  |
|  | | API Server | |        | |   Kubelet    | |  |
|  | +------------+ |        | +--------------+ |  |
|  |                |        |                  |  |
|  | +------------+ |        | +--------------+ |  |
|  | | Scheduler  | |        | |  Kube-proxy  | |  |
|  | +------------+ |        | +--------------+ |  |
|  |                |        |                  |  |
|  | +------------+ |        | +--------------+ |  |
|  | | Controller | |        | |  Container   | |  |
|  | |  Manager   | |        | |   Runtime    | |  |
|  | +------------+ |        | +--------------+ |  |
|  |                |        |                  |  |
|  | +------------+ |        | +--------------+ |  |
|  | |    etcd    | |        | |     Pods     | |  |
|  | +------------+ |        | +--------------+ |  |
|  +----------------+        +------------------+  |
|                                                  |
+--------------------------------------------------+
```

Este diagrama muestra la arquitectura básica de un cluster de Kubernetes, incluyendo el nodo maestro y un nodo de trabajo. Cada componente principal está representado en su ubicación correspondiente.

## 1.4 Resumen

Kubernetes proporciona una plataforma robusta para la orquestación de contenedores, ofreciendo una serie de características que facilitan la gestión de aplicaciones a gran escala. Su arquitectura distribuida, compuesta por nodos maestros y de trabajo, permite una gestión eficiente de los recursos y una alta disponibilidad de las aplicaciones.

En las próximas secciones, profundizaremos en cada uno de estos conceptos y aprenderemos cómo implementarlos en la práctica.

# Ejercicio

## Objetivo

El objetivo de estos ejercicios es familiarizarse con los conceptos básicos de Kubernetes y obtener experiencia práctica en la creación y gestión de un cluster local, así como en el despliegue de una aplicación simple.

## Ejercicio 1: Instalación y configuración de Minikube

Minikube es una herramienta que le permite ejecutar Kubernetes localmente. Minikube ejecuta un cluster de Kubernetes de un solo nodo dentro de una máquina virtual (VM) en su computadora.

### Paso 1: Instalar Minikube

1. Visite la [página oficial de Minikube](https://minikube.sigs.k8s.io/docs/start/)
2. Seleccione su sistema operativo y siga las instrucciones de instalación

Para Linux, puede usar estos comandos:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### Paso 2: Instalar kubectl

kubectl es la herramienta de línea de comandos para interactuar con clusters de Kubernetes.

Para Linux:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### Paso 3: Iniciar Minikube

Inicie su cluster local de Kubernetes con el siguiente comando:

```bash
minikube start
```

Este proceso puede tardar unos minutos, ya que Minikube descargará las imágenes necesarias y configurará el cluster.

### Paso 4: Verificar la instalación

Verifique que su cluster está funcionando correctamente:

```bash
kubectl get nodes
```

Debería ver una salida similar a esta:

```
NAME       STATUS   ROLES    AGE   VERSION
minikube   Ready    master   1m    v1.20.0
```