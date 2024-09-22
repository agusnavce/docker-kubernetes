# Introducción a Kubernetes

## Historia y evolución de Kubernetes

- Orígenes en Google: Kubernetes se basa en la experiencia de Google con Borg, su sistema interno de orquestación de contenedores.
- Lanzamiento como proyecto de código abierto en 2014.
- Donación a la Cloud Native Computing Foundation (CNCF) en 2015.
- Crecimiento rápido de la comunidad y adopción en la industria.

## Comparación con otras tecnologías

| Tecnología | Ventajas | Desventajas |
|------------|----------|-------------|
| Docker Swarm | Más simple de configurar | Menos potente y escalable |
| Apache Mesos | Más flexible | Curva de aprendizaje más pronunciada |
| Nomad (HashiCorp) | Más ligero y fácil de configurar | Menos funcionalidades |

## Casos de uso comunes

1. **Microservicios**: Ideal para arquitecturas de microservicios debido a su capacidad de gestión y escalado.
2. **CI/CD**: Facilita la implementación de pipelines de integración y despliegue continuos.
3. **Big Data**: Útil para el procesamiento de grandes volúmenes de datos con herramientas como Spark o Hadoop.
4. **Machine Learning**: Permite el despliegue y escalado eficiente de modelos de ML.

## Desafíos y consideraciones

- **Complejidad**: La curva de aprendizaje puede ser empinada para principiantes.
- **Sobrecarga**: Para aplicaciones pequeñas, Kubernetes puede ser excesivo.
- **Seguridad**: Requiere una configuración cuidadosa para garantizar la seguridad del cluster.
- **Costos**: La gestión y mantenimiento de clusters grandes puede ser costosa.

## Ecosistema de Kubernetes

- **Helm**: Gestor de paquetes para Kubernetes.
- **Prometheus**: Monitorización y alertas.
- **Istio**: Service mesh para mejorar la seguridad, observabilidad y gestión del tráfico.
- **Knative**: Plataforma serverless para Kubernetes.

## Mejores prácticas

1. Utilizar namespaces para organizar y aislar recursos.
2. Implementar límites de recursos para evitar el agotamiento de los mismos.
3. Utilizar etiquetas y anotaciones para una mejor organización y automatización.
4. Implementar políticas de seguridad de red con Network Policies.
5. Utilizar Persistent Volumes para el almacenamiento persistente.

## Futuro de Kubernetes

- Integración más profunda con tecnologías serverless.
- Mejoras en la gestión de estado para aplicaciones stateful.
- Mayor enfoque en la seguridad y el aislamiento.
- Simplificación de la experiencia del usuario para reducir la complejidad.

## Recursos adicionales para aprendizaje

- [Documentación oficial de Kubernetes](https://kubernetes.io/docs/)
- Libro: "Kubernetes: Up and Running"

## ¿Qué es Kubernetes?

Kubernetes, a menudo abreviado como K8s, es una plataforma de código abierto para automatizar la implementación, el escalado y la gestión de aplicaciones en contenedores. Fue originalmente diseñado por Google y ahora es mantenido por la Cloud Native Computing Foundation (CNCF).

### Puntos clave:
- Sistema de orquestación de contenedores
- Automatiza muchas tareas manuales involucradas en la implementación y escalado de aplicaciones
- Permite gestionar clusters de hosts en los que se ejecutan contenedores

## ¿Por qué usar Kubernetes?

Kubernetes ofrece varios beneficios que lo hacen atractivo para organizaciones de todos los tamaños:

1. **Escalabilidad**: Permite escalar aplicaciones y recursos de manera fácil y rápida.
2. **Portabilidad**: Las aplicaciones pueden moverse fácilmente entre diferentes proveedores de nube o infraestructuras on-premise.
3. **Alta disponibilidad**: Proporciona mecanismos para garantizar que las aplicaciones estén siempre disponibles.
4. **Eficiencia de recursos**: Optimiza el uso de recursos de hardware.
5. **Despliegue y rollback automáticos**: Facilita la implementación de nuevas versiones y la reversión a versiones anteriores si es necesario.
6. **Auto-reparación**: Puede reiniciar contenedores que fallan, reemplazar y reprogramar contenedores cuando los nodos mueren.
7. **Gestión de configuración y secretos**: Permite almacenar y gestionar información sensible.

## Conceptos clave y arquitectura

### Componentes principales de Kubernetes:

1. **Nodo maestro (Master Node)**:
   - API Server: Punto de entrada para todas las operaciones en el cluster.
   - Scheduler: Asigna pods a nodos.
   - Controller Manager: Gestiona controladores que regulan el estado del cluster.
   - etcd: Almacén de datos distribuido para mantener el estado del cluster.

2. **Nodos de trabajo (Worker Nodes)**:
   - Kubelet: Asegura que los contenedores estén funcionando en un pod.
   - Kube-proxy: Mantiene las reglas de red en los nodos.
   - Container Runtime: Software responsable de ejecutar contenedores (como Docker).

### Objetos básicos de Kubernetes:

1. **Pods**: La unidad más pequeña en Kubernetes, contiene uno o más contenedores.
2. **Services**: Abstracción que define un conjunto lógico de Pods y una política de acceso.
3. **Volumes**: Directorio accesible a los contenedores en un pod.
4. **Namespaces**: Forma de dividir los recursos del cluster entre múltiples usuarios.

### Arquitectura de alto nivel:

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

## Resumen

Kubernetes proporciona una plataforma robusta para la orquestación de contenedores, ofreciendo una serie de características que facilitan la gestión de aplicaciones a gran escala. Su arquitectura distribuida, compuesta por nodos maestros y de trabajo, permite una gestión eficiente de los recursos y una alta disponibilidad de las aplicaciones.

## Ejercicio: Instalación y configuración de Minikube

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