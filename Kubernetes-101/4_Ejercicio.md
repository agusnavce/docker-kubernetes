## Preguntas

1. **Configuración de Pods y Servicios**
   ¿Qué componente de Kubernetes utilizarías para hacer que los Pods sean accesibles, se descubran entre sí y proporcionen un punto de acceso estable para otros servicios?

   A) ConfigMaps
   B) Servicios (Services)
   C) Ingress
   D) Volúmenes Persistentes

   <details>
   <summary>Ver solución</summary>
   
   **Respuesta correcta: B) Servicios (Services)**
   
   Los Services en Kubernetes proporcionan una abstracción que define un conjunto lógico de Pods y una política para acceder a ellos. Ofrecen un punto de acceso estable (ClusterIP, NodePort, o LoadBalancer) que permite la comunicación entre diferentes partes de la aplicación, facilitando el descubrimiento de servicios y el balanceo de carga.
   </details>

2. **Asignación de Recursos**
   Para optimizar el uso de recursos en un cluster de Kubernetes, necesitas establecer límites y solicitudes de CPU y memoria para los Pods. ¿Qué combinación de objetos de Kubernetes te permite implementar una estrategia completa de gestión de recursos a nivel de namespace y Pod?

   A) Resource Quotas y Limit Ranges
   B) Horizontal Pod Autoscaler (HPA) y Vertical Pod Autoscaler (VPA)
   C) NodeSelector y Taints
   D) Pod Disruption Budgets y Priority Classes

   <details>
   <summary>Ver solución</summary>
   
   **Respuesta correcta: A) Resource Quotas y Limit Ranges**
   
   Resource Quotas permiten limitar el consumo agregado de recursos en un namespace, mientras que Limit Ranges establecen valores predeterminados y límites para las solicitudes y límites de recursos de los Pods individuales. Juntos, proporcionan un control granular sobre la asignación de recursos en el cluster.
   </details>

3. **Estrategias de Despliegue**
   En un entorno de producción crítico, necesitas implementar una nueva versión de tu aplicación con zero downtime, permitiendo un rollback rápido si se detectan problemas. ¿Qué estrategia de despliegue en Kubernetes es más adecuada para este escenario?

   A) Blue/Green Deployment utilizando Services
   B) Canary Releases con Ingress
   C) Rolling Updates con Deployments
   D) A/B Testing con Ingress y Service Mesh

   <details>
   <summary>Ver solución</summary>
   
   **Respuesta correcta: C) Rolling Updates con Deployments**
   
   Los Rolling Updates con Deployments permiten actualizar gradualmente los Pods de la aplicación, garantizando que siempre haya instancias disponibles durante el proceso de actualización. Esta estrategia ofrece zero downtime y la capacidad de realizar rollback rápidamente si se detectan problemas, simplemente revirtiendo el Deployment a la versión anterior.
   </details>

4. **Alta Disponibilidad y Recuperación ante Fallos**
   Para garantizar la alta disponibilidad y la rápida recuperación ante fallos de tus microservicios en Kubernetes, ¿qué combinación de mecanismos deberías implementar?

   A) Liveness Probes, Readiness Probes
   B) StatefulSets con Volúmenes Persistentes
   C) DaemonSets con Node Affinity
   D) CronJobs 

   <details>
   <summary>Ver solución</summary>
   
   **Respuesta correcta: A) Liveness Probes, Readiness Probes y PodDisruptionBudgets**
   
   Esta combinación proporciona una solución integral para la alta disponibilidad:
   - Liveness Probes aseguran que los Pods no funcionales se reinicien automáticamente.
   - Readiness Probes garantizan que solo los Pods completamente inicializados reciban tráfico.
   </details>

## Nuevas Preguntas

5. **Arquitectura de Cluster**
   En un cluster de Kubernetes, ¿qué componente es responsable de mantener y actualizar el estado deseado del cluster, y dónde se ejecuta típicamente?

   A) kubelet, en cada nodo del cluster
   B) kube-proxy, en el plano de control
   C) etcd, en los nodos de trabajo
   D) kube-apiserver, en el plano de control

   <details>
   <summary>Ver solución</summary>
   
   **Respuesta correcta: D) kube-apiserver, en el plano de control**
   
   El kube-apiserver es el componente central del plano de control de Kubernetes. Es responsable de exponer la API de Kubernetes, procesar las solicitudes de los clientes, y mantener y actualizar el estado deseado del cluster en etcd. Se ejecuta típicamente en los nodos del plano de control (anteriormente conocidos como nodos maestros).
   </details>

6. **Tipos de Nodos**
   En un cluster de Kubernetes, ¿cuál es la principal diferencia entre los nodos del plano de control y los nodos de trabajo?

   A) Los nodos del plano de control ejecutan aplicaciones, mientras que los nodos de trabajo gestionan el cluster
   B) Los nodos del plano de control gestionan el estado del cluster, mientras que los nodos de trabajo ejecutan las cargas de trabajo de las aplicaciones
   C) Los nodos del plano de control almacenan datos persistentes, mientras que los nodos de trabajo procesan solicitudes de API
   D) No hay diferencia, todos los nodos pueden realizar todas las funciones indistintamente

   <details>
   <summary>Ver solución</summary>
   
   **Respuesta correcta: B) Los nodos del plano de control gestionan el estado del cluster, mientras que los nodos de trabajo ejecutan las cargas de trabajo de las aplicaciones**
   
   Los nodos del plano de control (anteriormente llamados nodos maestros) albergan componentes críticos como el kube-apiserver, el kube-scheduler y el kube-controller-manager, que son responsables de mantener el estado deseado del cluster. Los nodos de trabajo, por otro lado, ejecutan los Pods de las aplicaciones y servicios desplegados por los usuarios.
   </details>

7. **Objetos de Kubernetes**
   ¿Cuál de los siguientes objetos de Kubernetes se utiliza para garantizar que un Pod se ejecute en cada nodo del cluster, útil para tareas como la recolección de logs o el monitoreo de nodos?

   A) Deployment
   B) StatefulSet
   C) DaemonSet
   D) Job

   <details>
   <summary>Ver solución</summary>
   
   **Respuesta correcta: C) DaemonSet**
   
   Un DaemonSet asegura que todos (o algunos) nodos ejecuten una copia de un Pod. A medida que se añaden nodos al cluster, los Pods son añadidos a ellos. Cuando los nodos se eliminan del cluster, esos Pods se garbage-collect. Eliminar un DaemonSet limpiará los Pods que creó. Los casos de uso típicos incluyen ejecutar un recolector de logs en cada nodo o un agente de monitoreo de nodos.
   </details>

8. **Networking en Kubernetes**
   En el contexto de networking de Kubernetes, ¿qué afirmación es correcta sobre el modelo de red de Pods?

   A) Cada Pod tiene su propia dirección IP privada y no puede comunicarse directamente con otros Pods
   B) Todos los Pods en un nodo comparten la misma dirección IP
   C) Cada Pod tiene su propia dirección IP y puede comunicarse directamente con otros Pods en el cluster
   D) Los Pods no tienen direcciones IP asignadas y dependen completamente de los Services para la comunicación

   <details>
   <summary>Ver solución</summary>
   
   **Respuesta correcta: C) Cada Pod tiene su propia dirección IP y puede comunicarse directamente con otros Pods en el cluster**
   
   En Kubernetes, cada Pod recibe su propia dirección IP única dentro del cluster. Esto permite que los Pods se comuniquen directamente entre sí, sin necesidad de NAT, independientemente del nodo en el que estén ubicados. Este modelo simplifica significativamente la comunicación entre aplicaciones y servicios dentro del cluster.
   </details>

9. **Almacenamiento en Kubernetes**
   ¿Qué objeto de Kubernetes se utiliza para solicitar almacenamiento persistente para un Pod, independientemente del ciclo de vida del Pod?

   A) ConfigMap
   B) Secret
   C) PersistentVolume
   D) PersistentVolumeClaim

   <details>
   <summary>Ver solución</summary>
   
   **Respuesta correcta: D) PersistentVolumeClaim**
   
   Un PersistentVolumeClaim (PVC) es una solicitud de almacenamiento por parte de un usuario. Es similar a un Pod en el sentido de que los Pods consumen recursos de nodo y los PVCs consumen recursos de PersistentVolume. Los PVCs pueden solicitar tamaños y modos de acceso específicos (por ejemplo, pueden ser montados como ReadWriteOnce, ReadOnlyMany o ReadWriteMany). Los PVCs proporcionan a los Pods acceso a almacenamiento persistente, independientemente del ciclo de vida del Pod.
   </details>

10. **Pods en Kubernetes**
    ¿Qué es un Pod en Kubernetes?

    A) Un nodo físico en el cluster
    B) Un contenedor Docker
    C) La unidad más pequeña y básica en el modelo de objetos de Kubernetes
    D) Un servicio que expone una aplicación

    <details>
    <summary>Ver solución</summary>
    
    **Respuesta correcta: C) La unidad más pequeña y básica en el modelo de objetos de Kubernetes**
    
    Un Pod es la unidad de despliegue más pequeña y básica que se puede crear y gestionar en Kubernetes. Un Pod encapsula uno o más contenedores, recursos de almacenamiento, una IP de red única y opciones que gobiernan cómo deben ejecutarse los contenedores.
    </details>

11. **Servicios en Kubernetes**
    ¿Cuál es la función principal de un Service en Kubernetes?

    A) Ejecutar contenedores
    B) Almacenar datos persistentes
    C) Exponer una aplicación como un servicio de red
    D) Monitorear el rendimiento del cluster

    <details>
    <summary>Ver solución</summary>
    
    **Respuesta correcta: C) Exponer una aplicación como un servicio de red**
    
    Un Service en Kubernetes es una abstracción que define un conjunto lógico de Pods y una política para acceder a ellos. Services permiten que un grupo de Pods sea accesible a través de la red, ya sea dentro del cluster o desde el exterior, proporcionando una IP estable y un nombre DNS para acceder a la aplicación.
    </details>

12. **Deployments en Kubernetes**
    ¿Cuál es el propósito principal de un Deployment en Kubernetes?

    A) Crear volúmenes persistentes
    B) Gestionar la creación y actualización de Pods
    C) Configurar el networking del cluster
    D) Autenticar usuarios en el cluster

    <details>
    <summary>Ver solución</summary>
    
    **Respuesta correcta: B) Gestionar la creación y actualización de Pods**
    
    Un Deployment proporciona declaraciones sobre cómo crear y actualizar instancias de tu aplicación. Una vez que creas un Deployment, el Control Plane de Kubernetes programa las instancias de aplicación incluidas en ese Deployment para que se ejecuten en nodos individuales del cluster. El Deployment también maneja las actualizaciones de la aplicación de manera controlada.
    </details>

13. **Namespaces en Kubernetes**
    ¿Qué son los Namespaces en Kubernetes?

    A) Espacios de almacenamiento físico en los nodos
    B) Clusters virtuales dentro de un mismo cluster físico
    C) Tipos de contenedores
    D) Mecanismos de autenticación

    <details>
    <summary>Ver solución</summary>
    
    **Respuesta correcta: B) Clusters virtuales dentro de un mismo cluster físico**
    
    Los Namespaces proporcionan un mecanismo para aislar grupos de recursos dentro de un mismo cluster físico. Son útiles en entornos con muchos usuarios distribuidos en múltiples equipos o proyectos, permitiendo dividir los recursos del cluster entre múltiples usos.
    </details>

14. **ConfigMaps en Kubernetes**
    ¿Para qué se utilizan principalmente los ConfigMaps en Kubernetes?

    A) Para almacenar contraseñas y tokens
    B) Para definir límites de recursos de los Pods
    C) Para almacenar datos de configuración no confidenciales
    D) Para crear copias de seguridad del cluster

    <details>
    <summary>Ver solución</summary>
    
    **Respuesta correcta: C) Para almacenar datos de configuración no confidenciales**
    
    Los ConfigMaps permiten desacoplar la configuración de la aplicación de las imágenes de los contenedores. Se utilizan para almacenar datos no confidenciales en formato clave-valor. Las aplicaciones pueden consumir ConfigMaps como variables de entorno, argumentos de línea de comandos o como archivos de configuración en un volumen.
    </details>

15. **Componentes Principales de Kubernetes**
    ¿Cuál de los siguientes NO es un componente principal del plano de control (control plane) de Kubernetes?

    A) kube-apiserver
    B) etcd
    C) kube-scheduler
    D) kubelet

    <details>
    <summary>Ver solución</summary>
    
    **Respuesta correcta: D) kubelet**
    
    El kubelet no es parte del plano de control de Kubernetes. Es un agente que se ejecuta en cada nodo del cluster y se asegura de que los contenedores estén corriendo en un Pod. Los componentes principales del plano de control son:
    
    - kube-apiserver: El servidor de API que sirve como punto de entrada para el cluster.
    - etcd: La base de datos de clave-valor que almacena la configuración del cluster.
    - kube-scheduler: Responsable de programar Pods en nodos.
    - kube-controller-manager: Ejecuta los controladores del cluster.

    El kubelet, por otro lado, es un componente que se ejecuta en cada nodo trabajador y se comunica con el plano de control para gestionar los Pods en su nodo.
    </details>