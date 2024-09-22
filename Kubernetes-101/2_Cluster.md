# Configuración y Gestión de Clusters Kubernetes

## Componentes de un Cluster

Un cluster de Kubernetes está compuesto por varios componentes que trabajan juntos:

1. **Plano de Control (Control Plane)**: 
   - API Server: El punto de entrada para todas las operaciones REST.
   - etcd: Almacén de datos distribuido que guarda el estado del cluster.
   - Scheduler: Asigna pods a nodos.
   - Controller Manager: Ejecuta controladores de cluster.

2. **Nodos de Trabajo**:
   - Kubelet: Asegura que los contenedores estén corriendo en un pod.
   - Kube-proxy: Mantiene las reglas de red en los nodos.
   - Container Runtime: Software que ejecuta los contenedores (ej. Docker).


## Opciones para configurar un cluster de Kubernetes

Existen varias formas de configurar un cluster de Kubernetes, cada una adaptada a diferentes necesidades y escenarios:

1. Desarrollo local
2. Clusters gestionados en la nube
3. Clusters on-premise

## Desarrollo local

Para desarrollo y aprendizaje, estas herramientas permiten crear clusters de Kubernetes en tu máquina local:

### Minikube

Minikube ejecuta un cluster de Kubernetes de un solo nodo en tu máquina local.

#### Instalación de Minikube:

```bash
# Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# macOS
brew install minikube

# Windows (con Chocolatey)
choco install minikube
```

#### Uso básico de Minikube:

```bash
minikube start
minikube status
minikube stop
minikube delete
```

### kind (Kubernetes in Docker)

kind ejecuta clusters de Kubernetes usando contenedores Docker como "nodos".

#### Instalación de kind:

```bash
# Linux
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# macOS
brew install kind

# Windows (con Chocolatey)
choco install kind
```

#### Uso básico de kind:

```bash
kind create cluster
kind get clusters
kind delete cluster
```

### Docker Desktop

Docker Desktop para Windows y Mac incluye Kubernetes integrado:

1. Abrir Docker Desktop
2. Ir a Preferencias/Configuración
3. En la pestaña Kubernetes, seleccionar "Enable Kubernetes"
4. Aplicar y reiniciar

## Clusters gestionados en la nube

Los proveedores de nube ofrecen servicios de Kubernetes gestionados que simplifican la creación y mantenimiento de clusters.

### Amazon Elastic Kubernetes Service (EKS)

```bash
# Instalar eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin

# Crear un cluster
eksctl create cluster --name my-cluster --region us-west-2 --nodes 2
```

### Google Kubernetes Engine (GKE)

```bash
# Configurar el proyecto
gcloud config set project YOUR_PROJECT_ID

# Crear un cluster
gcloud container clusters create my-cluster --num-nodes=2 --zone=us-central1-a
```

### Azure Kubernetes Service (AKS)

```bash
# Crear un grupo de recursos
az group create --name myResourceGroup --location eastus

# Crear un cluster
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 2
```

## Clusters on-premise

Para entornos on-premise, puedes configurar tu propio cluster usando kubeadm.

### Instalación de kubeadm:

```bash
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

## Gestión básica del cluster

Usa kubectl para gestionar tu cluster:

```bash
kubectl get nodes
kubectl cluster-info
kubectl get all --all-namespaces
```

### Ejercicio 1: Desplegar una aplicación simple

1. Crear un Deployment:
   ```bash
   kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
   ```

2. Verificar el Deployment:
   ```bash
   kubectl get deployments
   kubectl get pods
   ```

3. Crear un Service:
   ```bash
   kubectl expose deployment hello-node --type=LoadBalancer --port=8080
   ```

4. Acceder a la aplicación:
   ```bash
   minikube service hello-node
   ```

### Ejercicio 2: Escalar la aplicación

1. Escalar el Deployment:
   ```bash
   kubectl scale deployment hello-node --replicas=3
   ```

2. Verificar el escalado:
   ```bash
   kubectl get pods
   ```



