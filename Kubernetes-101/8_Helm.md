#  Gestión de Paquetes en Kubernetes

##  Introducción a Helm

Helm es el gestor de paquetes de facto para Kubernetes. Simplifica la definición, instalación y actualización de aplicaciones Kubernetes complejas. Helm utiliza un formato de empaquetado llamado "charts".

## Conceptos clave de Helm

### Charts

Un chart es un paquete de Helm que contiene toda la información necesaria para crear una instancia de una aplicación Kubernetes.

### Repositorios

Los repositorios son lugares donde se almacenan y comparten los charts.

### Releases

Una release es una instancia de un chart ejecutándose en un cluster Kubernetes.

## Instalación de Helm

La instalación de Helm varía según el sistema operativo. Aquí hay un ejemplo para Linux:

```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

## Uso básico de Helm

### Agregar un repositorio

```bash
helm repo add stable https://charts.helm.sh/stable
```

### Actualizar repositorios

```bash
helm repo update
```

### Buscar charts

```bash
helm search repo stable
```

### Instalar un chart

```bash
helm install mi-release stable/mysql
```

### Listar releases

```bash
helm list
```

### Desinstalar una release

```bash
helm uninstall mi-release
```

## Creación de un chart personalizado

### Crear un nuevo chart

```bash
helm create mi-chart
```

### Estructura de un chart

```
mi-chart/
  Chart.yaml          # Información sobre el chart
  values.yaml         # Valores por defecto
  templates/          # Plantillas
  charts/             # Charts dependientes
```

### Validar un chart

```bash
helm lint mi-chart
```

### Empaquetar un chart

```bash
helm package mi-chart
```

## Helm y valores

Los values permiten personalizar un chart sin modificar sus plantillas.

### Sobreescribir valores al instalar

```bash
helm install mi-release mi-chart --set key1=value1,key2=value2
```

### Usar un archivo de valores personalizado

```bash
helm install mi-release mi-chart -f valores-personalizados.yaml
```

## Helm Hooks

Los hooks permiten intervenir en puntos específicos del ciclo de vida de una release.

Ejemplo de un hook pre-install:

```yaml
annotations:
  "helm.sh/hook": pre-install
```

## Mejores prácticas

1. Versionar los charts
2. Usar valores por defecto sensatos
3. Documentar el chart y sus valores
4. Usar subcarts para componentes reutilizables
5. Probar los charts en diferentes entornos antes de usarlos en producción

## Helm vs. Operadores

Mientras que Helm es excelente para despliegues declarativos, los Operadores de Kubernetes son más adecuados para aplicaciones que requieren lógica operacional compleja y manejo de estado.