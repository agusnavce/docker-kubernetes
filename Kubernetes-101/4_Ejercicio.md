# Ejercicio de Opción Múltiple: Despliegue de una Aplicación en Kubernetes

Este ejercicio evalúa tu conocimiento sobre el despliegue y operación de una aplicación en Kubernetes, abarcando varios aspectos como escalabilidad, actualizaciones, recuperación ante fallos, y almacenamiento persistente.

---

## Paso 1: Configuración de Pods y Servicios

**Pregunta 1:**
Necesitas crear Pods para cada microservicio. Estos Pods deben ser accesibles entre ellos y para los usuarios externos. ¿Qué componente de Kubernetes utilizarías para hacer que los Pods sean accesibles y se descubran entre sí?

- A) **Solo ConfigMaps**
- B) **Servicios (Services)**
- C) **Ingress únicamente**
- D) **Solo Volúmenes Persistentes**

<details>
<summary><strong>Ver solución</strong></summary>
  
**Respuesta correcta:**  
B) **Servicios (Services)**  
**Explicación:** Los Services permiten que los Pods se descubran entre ellos y exponen los microservicios al exterior si es necesario, gestionando el tráfico y balanceo de carga entre ellos.

</details>

---

## Paso 2: Asignación de recursos

**Pregunta 2:**
Para garantizar que cada microservicio funcione dentro de los límites adecuados de CPU y memoria, necesitas establecer restricciones en los recursos que pueden usar. ¿Qué componentes de Kubernetes te permiten definir límites de recursos para los Pods?

- A) **Resource Quotas y Limit Ranges**
- B) **Horizontal Pod Autoscaler (HPA)**
- C) **NodeSelector y Taints**
- D) **Pod Disruption Budgets**

<details>
<summary><strong>Ver solución</strong></summary>
  
**Respuesta correcta:**  
A) **Resource Quotas y Limit Ranges**  
**Explicación:** Las Resource Quotas y Limit Ranges permiten definir cuánta CPU y memoria puede consumir un Pod o un conjunto de Pods, garantizando que cada microservicio se mantenga dentro de los límites establecidos.

</details>

---

## Paso 3: Escalabilidad

**Pregunta 3:**
Deseas mantener un número fijo de réplicas de tus Pods en ejecución para garantizar la alta disponibilidad de tus microservicios. Si algún Pod falla, debe ser reemplazado automáticamente para que siempre haya una cantidad mínima de réplicas en ejecución. ¿Qué componente de Kubernetes utilizarías para lograr esto?

- A) **DaemonSets**
- B) **ReplicaSets**
- C) **Horizontal Pod Autoscaler (HPA)**
- D) **StatefulSets**

<details>
<summary><strong>Ver solución</strong></summary>
  
**Respuesta correcta:**  
B) **ReplicaSets**  
**Explicación:** El **ReplicaSet** asegura que un número específico de Pods esté siempre en ejecución. Si un Pod falla, el ReplicaSet crea uno nuevo para mantener la cantidad deseada de réplicas.

</details>

---

## Paso 4: Actualización de Pods

**Pregunta 4:**
Deseas implementar una nueva versión de los microservicios sin interrumpir el servicio. ¿Qué estrategia de despliegue deberías usar para implementar una nueva versión sin tiempo de inactividad?

- A) **Rolling Updates con Deployments**
- B) **StatefulSets**
- C) **Reiniciar manualmente los Pods**
- D) **DaemonSets**

<details>
<summary><strong>Ver solución</strong></summary>
  
**Respuesta correcta:**  
A) **Rolling Updates con Deployments**  
**Explicación:** Los Rolling Updates permiten que los Pods antiguos sean reemplazados por los nuevos de forma gradual, evitando el tiempo de inactividad.

</details>

---

## Paso 5: Recuperación ante fallos

**Pregunta 5:**
Necesitas asegurarte de que los Pods fallidos se reinicien automáticamente sin afectar a otros componentes. ¿Qué mecanismo deberías implementar para garantizar la alta disponibilidad de los microservicios?

- A) **Liveness Probes y Readiness Probes**
- B) **StatefulSets con Volúmenes Persistentes**
- C) **ConfigMaps**
- D) **DaemonSets con HPA**

<details>
<summary><strong>Ver solución</strong></summary>
  
**Respuesta correcta:**  
A) **Liveness Probes y Readiness Probes**  
**Explicación:** Las Liveness Probes permiten que Kubernetes reinicie los Pods fallidos automáticamente, mientras que las Readiness Probes aseguran que los Pods estén listos para manejar tráfico antes de exponerlos.

</details>

---

## Paso 6: Persistencia de Datos

**Pregunta 6:**
Uno de los microservicios que estás desplegando necesita almacenar datos de manera persistente, incluso si los Pods fallan o se reinician. ¿Qué mecanismo deberías implementar en Kubernetes para garantizar la persistencia de los datos?

- A) **Volúmenes Persistentes (Persistent Volumes) y PersistentVolumeClaims (PVC)**
- B) **ConfigMaps**
- C) **Liveness Probes**
- D) **ReplicaSets**

<details>
<summary><strong>Ver solución</strong></summary>
  
**Respuesta correcta:**  
A) **Volúmenes Persistentes (Persistent Volumes) y PersistentVolumeClaims (PVC)**  
**Explicación:** Los Volúmenes Persistentes y PersistentVolumeClaims permiten que los datos de la aplicación persistan incluso si los Pods fallan o se reinician, asegurando que los datos no se pierdan.

</details>

---

## Paso 7: Gestión de Configuración

**Pregunta 7:**
Quieres que los microservicios utilicen un archivo de configuración que puedas cambiar sin reconstruir la imagen del contenedor. ¿Qué recurso de Kubernetes deberías usar para inyectar esta configuración en los Pods?

- A) **ConfigMaps**
- B) **Secrets**
- C) **Persistent Volumes**
- D) **ReplicaSets**

<details>
<summary><strong>Ver solución</strong></summary>
  
**Respuesta correcta:**  
A) **ConfigMaps**  
**Explicación:** Los ConfigMaps permiten inyectar datos de configuración en los Pods sin necesidad de reconstruir la imagen del contenedor, facilitando la gestión de configuraciones dinámicas.

</details>

---

## Paso 8: Seguridad

**Pregunta 8:**
Para manejar información sensible, como contraseñas o claves API, en un entorno seguro dentro de Kubernetes, ¿qué recurso deberías usar?

- A) **Secrets**
- B) **ConfigMaps**
- C) **Resource Quotas**
- D) **Ingress**

<details>
<summary><strong>Ver solución</strong></summary>
  
**Respuesta correcta:**  
A) **Secrets**  
**Explicación:** Los Secrets permiten almacenar y gestionar información sensible, como contraseñas y claves API, de forma segura en Kubernetes.

</details>

---

## Paso 9: Balanceo de carga externo

**Pregunta 9:**
Tu aplicación necesita estar disponible para usuarios externos y recibir tráfico HTTP. ¿Qué recurso de Kubernetes te permite gestionar el tráfico externo y balancearlo hacia los Pods correctos?

- A) **Ingress**
- B) **DaemonSets**
- C) **StatefulSets**
- D) **Pod Disruption Budgets**

<details>
<summary><strong>Ver solución</strong></summary>
  
**Respuesta correcta:**  
A) **Ingress**  
**Explicación:** Ingress permite gestionar el tráfico HTTP externo y redirigirlo a los Servicios internos correspondientes, ofreciendo balanceo de carga y enrutamiento.

</details>

