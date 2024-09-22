# 4. Servicios y Redes en Kubernetes

## 4.1 Introducción

En Kubernetes, los Servicios y las Redes juegan un papel crucial en cómo las aplicaciones se comunican entre sí y con el mundo exterior. Esta sección cubre los conceptos fundamentales de networking en Kubernetes y cómo los Servicios facilitan la comunicación entre componentes de una aplicación.

## 4.2 Conceptos clave

### 4.2.1 Servicios

Un Servicio en Kubernetes es una abstracción que define un conjunto lógico de Pods y una política para acceder a ellos. Los Servicios permiten que un grupo de Pods sea accesible en la red, ya sea dentro del cluster o externamente.

Tipos de Servicios:

1. **ClusterIP**: Expone el Servicio en una IP interna del cluster. Es el tipo por defecto.
2. **NodePort**: Expone el Servicio en el mismo puerto de cada nodo seleccionado en el cluster.
3. **LoadBalancer**: Expone el Servicio externamente usando el balanceador de carga del proveedor de nube.
4. **ExternalName**: Mapea el Servicio al contenido del campo `externalName`.

### 4.2.2 Ingress

Ingress es un objeto API que gestiona el acceso externo a los servicios en un cluster, típicamente HTTP. Ingress puede proporcionar balanceo de carga, terminación SSL y hospedaje virtual basado en nombres.

### 4.2.3 NetworkPolicies

Las NetworkPolicies son especificaciones de cómo grupos de pods pueden comunicarse entre sí y con otros endpoints de red. NetworkPolicies proporcionan una forma de filtrar el tráfico de red.

## 4.3 Funcionamiento de la red en Kubernetes

Kubernetes sigue un modelo de red "IP-per-Pod", lo que significa que cada Pod obtiene su propia dirección IP. Esto permite tratar los Pods muy parecido a las VM o hosts físicos desde la perspectiva de asignación de puertos, nombramiento, service discovery, balanceo de carga, configuración de aplicaciones y migración.

Principios del modelo de red de Kubernetes:

1. Todos los Pods pueden comunicarse con todos los otros Pods sin NAT.
2. Todos los nodos pueden comunicarse con todos los Pods sin NAT.
3. La IP que un Pod ve para sí mismo es la misma IP que otros Pods ven para él.

## 4.4 Descubrimiento de servicios

Kubernetes soporta dos modos principales de encontrar un Servicio:

1. **Variables de entorno**: Cuando un Pod se ejecuta en un Nodo, el kubelet añade un conjunto de variables de entorno para cada Servicio activo.
2. **DNS**: Un add-on DNS puede ser instalado en el cluster para detectar automáticamente nuevos Servicios y crear registros DNS para ellos.

## 4.5 Balanceo de carga

Los Servicios en Kubernetes proporcionan balanceo de carga integrado. El tráfico que llega a un Servicio se distribuye automáticamente entre los Pods que son seleccionados por el Servicio.

## 4.6 Redes CNI (Container Network Interface)

Kubernetes soporta varios plugins de red que implementan la especificación CNI. Estos plugins manejan la configuración de red para los contenedores, incluyendo la asignación de direcciones IP y la configuración de rutas.

Algunos plugins CNI populares incluyen:

- Calico
- Flannel
- Weave Net
- Cilium

La elección del plugin CNI depende de los requisitos específicos del cluster y puede afectar características como el rendimiento de la red, la seguridad y la escalabilidad.