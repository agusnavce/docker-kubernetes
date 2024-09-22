# 5. Almacenamiento en Kubernetes

## 5.1 Introducción

El manejo del almacenamiento es un aspecto crucial en Kubernetes, especialmente para aplicaciones que necesitan persistir datos. Kubernetes proporciona varias abstracciones para manejar el almacenamiento de manera flexible y eficiente.

## 5.2 Conceptos clave

### 5.2.1 Volumes

Un Volume en Kubernetes es simplemente un directorio, que puede contener datos, accesible a los contenedores en un Pod. Cómo se crea ese directorio, el medio que lo respalda y su contenido son determinados por el tipo de volumen utilizado.

Tipos comunes de Volumes:
- emptyDir
- hostPath
- nfs
- configMap
- secret
- persistentVolumeClaim

### 5.2.2 PersistentVolumes (PV)

Un PersistentVolume es un recurso en el cluster, al igual que un nodo. Los PVs son plugins de volumen que tienen un ciclo de vida independiente de cualquier Pod individual que utilice el PV. Esta API objeto captura los detalles de la implementación del almacenamiento, ya sea NFS, iSCSI, o un sistema de almacenamiento específico del proveedor de nube.

### 5.2.3 PersistentVolumeClaims (PVC)

Un PersistentVolumeClaim es una solicitud de almacenamiento por parte de un usuario. Es similar a un Pod. Los Pods consumen recursos de nodo y los PVCs consumen recursos PV. Los Pods pueden solicitar niveles específicos de recursos (CPU y Memoria). Las Claims pueden solicitar tamaños y modos de acceso específicos.

### 5.2.4 StorageClasses

Un StorageClass proporciona una forma para que los administradores describan las "clases" de almacenamiento que ofrecen. Diferentes clases pueden mapear a niveles de calidad de servicio, políticas de respaldo, o a políticas arbitrarias determinadas por los administradores del cluster.

## 5.3 Ciclo de vida del almacenamiento

1. Provisioning: Creación de un PV. Puede ser estático (pre-aprovisionado por el administrador) o dinámico (basado en StorageClasses).
2. Binding: Un usuario crea un PVC y Kubernetes encuentra un PV que coincida con los requisitos.
3. Using: El Pod usa el PVC como un volumen.
4. Releasing: El usuario elimina el PVC y el PV se "libera".
5. Reclaiming: El PV es reclamado según la política de reclamación (Retain, Recycle, o Delete).

## 5.4 Modos de acceso

Los PersistentVolumes soportan diferentes modos de acceso:
- ReadWriteOnce (RWO): el volumen puede ser montado como lectura-escritura por un solo nodo.
- ReadOnlyMany (ROX): el volumen puede ser montado como solo lectura por muchos nodos.
- ReadWriteMany (RWX): el volumen puede ser montado como lectura-escritura por muchos nodos.

## 5.5 Políticas de retención

Cuando un usuario termina con su volumen, puede eliminar los objetos PVC del API que permite la reclamación del recurso. La política de reclamación para un PersistentVolume le dice al cluster qué hacer con el volumen después de que haya sido liberado de su claim. Las políticas de reclamación actuales son:

- Retain: conserva los datos manualmente.
- Recycle: limpia básicamente el contenido (`rm -rf /thevolume/*`).
- Delete: elimina el volumen de almacenamiento asociado.

## 5.6 Expansión de volumen

La capacidad de expandir volúmenes persistentes está disponible como una característica alfa desde Kubernetes 1.8 y se movió a beta en 1.11. Debe habilitarse explícitamente en el cluster.