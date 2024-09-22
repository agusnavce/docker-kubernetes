# Configuración y Secretos en Kubernetes

##  Introducción

La gestión de la configuración y los secretos es crucial para mantener las aplicaciones flexibles y seguras en Kubernetes. Esta sección cubre cómo Kubernetes maneja la configuración de las aplicaciones y los datos sensibles.

## ConfigMaps

Los ConfigMaps permiten desacoplar la configuración de las imágenes de contenedor, lo que hace que las aplicaciones sean más portátiles.

###  Creación de ConfigMaps

Los ConfigMaps pueden crearse de varias maneras:
- A partir de archivos
- A partir de directorios
- A partir de valores literales

Ejemplo de creación de un ConfigMap:

```bash
kubectl create configmap mi-config --from-literal=APP_COLOR=blue --from-literal=APP_MOD=prod
```

### Uso de ConfigMaps

Los ConfigMaps pueden ser utilizados por Pods de varias formas:
- Como variables de entorno
- Como argumentos de comando
- Como volúmenes de configuración

## Secretos

Los Secretos son objetos que contienen datos sensibles como contraseñas, tokens o claves. Usar Secretos evita incluir datos confidenciales en el código de la aplicación.

### Tipos de Secretos

- Opaque (default)
- kubernetes.io/service-account-token
- kubernetes.io/dockercfg
- kubernetes.io/dockerconfigjson
- kubernetes.io/basic-auth
- kubernetes.io/ssh-auth
- kubernetes.io/tls

### Creación de Secretos

Los Secretos pueden crearse de manera similar a los ConfigMaps:

```bash
kubectl create secret generic mi-secreto --from-literal=username=admin --from-literal=password=t0p-Secret
```

### Uso de Secretos

Los Secretos pueden ser montados como volúmenes o expuestos como variables de entorno en un Pod.

## sVariables de Entorno

Kubernetes permite definir variables de entorno para los contenedores en los Pods. Estas pueden ser:

- Valores estáticos
- Referencias a otros campos en la definición del Pod
- Referencias a ConfigMaps o Secretos

Ejemplo:

```yaml
env:
  - name: MI_VARIABLE
    value: "mi valor"
  - name: POD_NAME
    valueFrom:
      fieldRef:
        fieldPath: metadata.name
```

## Mejores Prácticas

1. Utilizar ConfigMaps para configuraciones no sensibles y Secretos para datos sensibles.
2. Limitar el acceso a los Secretos utilizando RBAC (Control de Acceso Basado en Roles).
3. Encriptar los Secretos en reposo (at rest).
4. Considerar el uso de soluciones externas de gestión de secretos para entornos de producción críticos.
5. Rotar regularmente los secretos y las credenciales.
6. Utilizar variables de entorno para configuraciones que puedan cambiar entre diferentes entornos.

## Consideraciones de Seguridad

- Los Secretos se almacenan en etcd, por lo que es importante asegurar y encriptar este almacenamiento.
- Los Secretos no están encriptados por defecto cuando se transmiten a través de la red del cluster.
- Considerar el uso de políticas de seguridad de red para restringir qué Pods pueden acceder a qué Secretos.