# 2. Configuración de un Cluster de Kubernetes

## 2.1 Opciones para configurar un cluster de Kubernetes

Existen varias formas de configurar un cluster de Kubernetes, dependiendo de tus necesidades y recursos. Vamos a explorar tres opciones principales:

1. Desarrollo local
2. Clusters gestionados en la nube
3. Clusters on-premise

## 2.2 Desarrollo local

Para desarrollo y aprendizaje, existen herramientas que permiten crear un cluster de Kubernetes en tu máquina local.

### 2.2.1 Minikube

Minikube es una herramienta que permite ejecutar un cluster de Kubernetes de un solo nodo en tu máquina local.

#### Instalación de Minikube:

```bash
# Para Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Para macOS
brew install minikube

# Para Windows (con Chocolatey)
choco install minikube
```

#### Uso básico de Minikube:

```bash
# Iniciar un cluster
minikube start

# Verificar el estado
minikube status

# Detener el cluster
minikube stop

# Eliminar el cluster
minikube delete
```

### 2.2.2 kind (Kubernetes in Docker)

kind es otra herramienta para ejecutar clusters de Kubernetes locales usando contenedores Docker como "nodos".

#### Instalación de kind:

```bash
# Para Linux
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# Para macOS
brew install kind

# Para Windows (con Chocolatey)
choco install kind
```

#### Uso básico de kind:

```bash
# Crear un cluster
kind create cluster

# Listar clusters
kind get clusters

# Eliminar un cluster
kind delete cluster
```

### 2.2.3 Docker Desktop

Docker Desktop para Windows y Mac viene con Kubernetes integrado, que se puede habilitar fácilmente.

1. Abrir Docker Desktop
2. Ir a Preferencias/Configuración
3. En la pestaña Kubernetes, seleccionar "Enable Kubernetes"
4. Aplicar y reiniciar

## 2.3 Clusters gestionados en la nube

Los proveedores de nube ofrecen servicios de Kubernetes gestionados, que simplifican la creación y mantenimiento de clusters.

### 2.3.1 Amazon Elastic Kubernetes Service (EKS)

Amazon EKS es el servicio de Kubernetes gestionado de AWS.

#### Creación de un cluster EKS (usando eksctl):

```bash
# Instalar eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin

# Crear un cluster
eksctl create cluster --name my-cluster --region us-west-2 --nodes 2
```

### 2.3.2 Google Kubernetes Engine (GKE)

GKE es el servicio de Kubernetes gestionado de Google Cloud.

#### Creación de un cluster GKE (usando gcloud):

```bash
# Instalar gcloud (sigue las instrucciones específicas de tu sistema operativo)

# Configurar el proyecto
gcloud config set project YOUR_PROJECT_ID

# Crear un cluster
gcloud container clusters create my-cluster --num-nodes=2 --zone=us-central1-a
```

### 2.3.3 Azure Kubernetes Service (AKS)

AKS es el servicio de Kubernetes gestionado de Microsoft Azure.

#### Creación de un cluster AKS (usando az):

```bash
# Instalar Azure CLI (sigue las instrucciones específicas de tu sistema operativo)

# Crear un grupo de recursos
az group create --name myResourceGroup --location eastus

# Crear un cluster
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 2
```

## 2.4 Clusters on-premise

Para entornos on-premise, puedes configurar tu propio cluster de Kubernetes utilizando herramientas como kubeadm.

### Instalación de kubeadm:

```bash
# Instalar kubeadm, kubelet y kubectl
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
```

### Inicialización del cluster:

```bash
# En el nodo maestro
sudo kubeadm init

# En los nodos trabajadores
sudo kubeadm join <master-ip>:<master-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

## 2.5 Gestión básica del cluster

Independientemente de cómo hayas creado tu cluster, puedes gestionarlo utilizando kubectl.

```bash
# Verificar los nodos del cluster
kubectl get nodes

# Obtener información del cluster
kubectl cluster-info

# Listar todos los recursos en todos los namespaces
kubectl get all --all-namespaces
```

## Ejercicio 2: Desplegar una aplicación simple

Ahora que tenemos nuestro cluster funcionando, vamos a desplegar una aplicación simple.

### Paso 1: Crear un Deployment

Un Deployment es un objeto de Kubernetes que define una aplicación. Vamos a crear un Deployment que ejecuta un servidor de eco simple:

```bash
kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
```

Este comando crea un Deployment llamado "hello-node" utilizando la imagen "k8s.gcr.io/echoserver:1.4".

### Paso 2: Verificar el Deployment

Verifique que el Deployment se ha creado correctamente:

```bash
kubectl get deployments
```

Y verifique que el Pod asociado está en ejecución:

```bash
kubectl get pods
```

### Paso 3: Crear un Service

Para hacer que nuestra aplicación sea accesible desde fuera del cluster, necesitamos crear un Service:

```bash
kubectl expose deployment hello-node --type=LoadBalancer --port=8080
```

Este comando crea un Service de tipo LoadBalancer que expone el puerto 8080 de nuestra aplicación.

### Paso 4: Acceder a la aplicación

En un entorno de nube, un LoadBalancer crearía un balanceador de carga externo. Como estamos usando Minikube, necesitamos usar un comando especial para acceder al servicio:

```bash
minikube service hello-node
```

Este comando abrirá automáticamente un navegador con la URL de la aplicación.

## Ejercicio 3: Escalar la aplicación

Vamos a aprender cómo escalar nuestra aplicación.

### Paso 1: Escalar el Deployment

Aumente el número de réplicas de su aplicación:

```bash
kubectl scale deployment hello-node --replicas=3
```

Este comando escala el Deployment "hello-node" a 3 réplicas.

### Paso 2: Verificar el escalado

Verifique que ahora tiene 3 Pods ejecutándose:

```bash
kubectl get pods
```

Debería ver 3 Pods con el prefijo "hello-node".

## Conclusión

En estos ejercicios, ha aprendido a:

1. Instalar y configurar Minikube y kubectl
2. Crear un cluster de Kubernetes local
3. Desplegar una aplicación simple
4. Exponer la aplicación mediante un Service
5. Escalar la aplicación

Estos son los primeros pasos en el mundo de Kubernetes. A medida que se sienta más cómodo con estos conceptos, podrá explorar características más avanzadas y desplegar aplicaciones más complejas.