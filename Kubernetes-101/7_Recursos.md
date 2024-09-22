# 7. Gestión de Recursos y Programación en Kubernetes

## Introducción

La gestión eficiente de recursos y la programación inteligente de cargas de trabajo son fundamentales para optimizar el rendimiento y la utilización de un cluster de Kubernetes. Esta sección cubre cómo Kubernetes maneja la asignación de recursos y la programación de Pods.

## Solicitudes y Límites de Recursos

Kubernetes permite especificar cuántos recursos computacionales requiere un contenedor (solicitudes) y cuántos puede consumir como máximo (límites).

### Solicitudes de Recursos (Requests)

Las solicitudes de recursos especifican la cantidad mínima de recursos que se deben proporcionar para un contenedor.

```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
```

### Límites de Recursos (Limits)

Los límites de recursos especifican la cantidad máxima de recursos que un contenedor puede consumir.

```yaml
resources:
  limits:
    memory: "128Mi"
    cpu: "500m"
```

## Namespaces

Los Namespaces proporcionan una forma de dividir los recursos del cluster entre múltiples usuarios, equipos o proyectos.

```bash
kubectl create namespace mi-namespace
```

## Etiquetas y Selectores

Las etiquetas son pares clave-valor que se adjuntan a los objetos. Los selectores se utilizan para identificar un conjunto de objetos.

### Etiquetas

```yaml
metadata:
  labels:
    app: myapp
    environment: production
```

### Selectores

```yaml
selector:
  matchLabels:
    app: myapp
```

## Afinidad y Anti-afinidad

La afinidad y anti-afinidad permiten influir en cómo se programan los Pods en relación con otros Pods o nodos del cluster.

### Afinidad de Nodos

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/e2e-az-name
          operator: In
          values:
          - e2e-az1
          - e2e-az2
```

### Afinidad de Pods

```yaml
affinity:
  podAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchExpressions:
        - key: security
          operator: In
          values:
          - S1
      topologyKey: topology.kubernetes.io/zone
```

## Taints y Tolerations

Los Taints permiten a un nodo repeler un conjunto de Pods. Las Tolerations se aplican a los Pods y permiten (pero no requieren) que los Pods se programen en nodos con Taints coincidentes.

### Aplicar un Taint a un Nodo

```bash
kubectl taint nodes node1 key=value:NoSchedule
```

### Definir una Toleration en un Pod

```yaml
tolerations:
- key: "key"
  operator: "Equal"
  value: "value"
  effect: "NoSchedule"
```

## Programador (Scheduler)

El programador de Kubernetes es responsable de decidir dónde se ejecutan los Pods. Considera factores como:

- Recursos disponibles
- Restricciones de hardware/software
- Afinidad y anti-afinidad
- Taints y tolerations
- Prioridad del Pod

## Cuotas de Recursos

Las cuotas de recursos permiten limitar el consumo de recursos agregados por Namespace.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
```