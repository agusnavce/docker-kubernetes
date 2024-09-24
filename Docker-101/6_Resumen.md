# Resumen de Docker

## Conceptos básicos

- **Docker**: Plataforma para desarrollar, enviar y ejecutar aplicaciones en contenedores.
- **Contenedor**: Instancia ejecutable de una imagen, incluye una aplicación y sus dependencias.
- **Imagen**: Plantilla de solo lectura con instrucciones para crear un contenedor.
- **Dockerfile**: Archivo de texto que contiene instrucciones para construir una imagen Docker.
- **DockerHub**: Repositorio público de imágenes Docker.

## Networking en Docker

- Docker crea por defecto tres redes: `bridge`, `host`, y `none`.
- La red `bridge` es la predeterminada para los contenedores.
- Se pueden crear redes personalizadas para aislar grupos de contenedores.
- La publicación de puertos permite acceder a servicios dentro de los contenedores desde el exterior.

## Almacenamiento en Docker

- **Volúmenes**: Mecanismo preferido para persistir datos generados y utilizados por contenedores Docker.
- **Bind mounts**: Montan un directorio del host directamente en el contenedor.
- **tmpfs mounts**: Almacenamiento temporal en memoria.

## Dockerfile y construcción de imágenes

- `FROM`: Especifica la imagen base.
- `RUN`: Ejecuta comandos en una nueva capa.
- `COPY` y `ADD`: Copian archivos desde el host a la imagen.
- `ENV`: Establece variables de entorno.
- `EXPOSE`: Informa qué puertos escucha el contenedor.
- `CMD`: Especifica el comando a ejecutar cuando se inicia el contenedor.

## Docker Compose

- Herramienta para definir y ejecutar aplicaciones Docker multi-contenedor.
- Utiliza un archivo YAML para configurar los servicios de la aplicación.
- Permite gestionar todo el ciclo de vida de la aplicación: inicio, detención y reconstrucción de servicios.

Ciertamente. Aquí tienes una sección ampliada de buenas prácticas en formato Markdown:

## Buenas prácticas

### Construcción de imágenes

- **Usar imágenes base oficiales o de confianza**: 
  - Utiliza imágenes oficiales de Docker Hub cuando sea posible.
  - Si usas imágenes de terceros, verifica su procedencia y contenido.

- **Minimizar el número de capas**:
  - Combina comandos RUN usando `&&` para reducir el número de capas.
  - Ejemplo: `RUN apt-get update && apt-get install -y package1 package2`

- **Utilizar .dockerignore**:
  - Crea un archivo `.dockerignore` para excluir archivos y directorios innecesarios del contexto de construcción.
  - Esto reduce el tamaño de la imagen y evita la inclusión accidental de información sensible.

- **Ordenar las instrucciones del Dockerfile de manera eficiente**:
  - Coloca las instrucciones que cambian con menos frecuencia al principio del Dockerfile.
  - Esto optimiza el uso de la caché durante la construcción de la imagen.

- **Usar etiquetas específicas para las imágenes base**:
  - Evita usar la etiqueta `latest`, ya que puede causar inconsistencias.
  - Especifica una versión concreta, por ejemplo: `FROM ubuntu:20.04`

### Seguridad

- **No ejecutar contenedores como root**:
  - Usa la instrucción `USER` en el Dockerfile para cambiar a un usuario no privilegiado.
  - Crea un usuario específico para la aplicación si es necesario.

- **Minimizar la superficie de ataque**:
  - Instala solo los paquetes necesarios para la aplicación.
  - Usa imágenes base minimalistas cuando sea posible (por ejemplo, Alpine Linux).

- **Escanear imágenes en busca de vulnerabilidades**:
  - Utiliza herramientas como Docker Scan, Clair, o Trivy para detectar vulnerabilidades conocidas.

- **Utilizar secretos de manera segura**:
  - No incluyas secretos (contraseñas, tokens, etc.) directamente en el Dockerfile o en la imagen.
  - Usa Docker secrets o herramientas de gestión de secretos externas.

### Gestión de datos

- **Usar volúmenes para datos persistentes**:
  - Utiliza volúmenes Docker para almacenar datos que deben persistir entre ejecuciones de contenedores.
  - Evita almacenar datos importantes en la capa de escritura del contenedor.

- **Separar datos de aplicación**:
  - Mantén los datos de la aplicación separados del código y de la configuración.
  - Esto facilita las actualizaciones y el mantenimiento.

### Rendimiento y mantenibilidad

- **Implementar health checks**:
  - Usa la instrucción `HEALTHCHECK` en el Dockerfile para definir cómo Docker debe comprobar que tu contenedor está funcionando correctamente.

- **Optimizar el tamaño de las imágenes**:
  - Usa construcciones multi-etapa para crear imágenes más pequeñas.
  - Limpia caches y archivos temporales en la misma capa donde se crean.

- **Documentar la imagen**:
  - Usa la instrucción `LABEL` para añadir metadatos útiles a tu imagen (versión, mantenedor, etc.).
  - Incluye un README con instrucciones claras sobre cómo usar la imagen.

### Configuración y entorno

- **Usar variables de entorno para la configuración**:
  - Permite la configuración dinámica de la aplicación a través de variables de entorno.
  - Evita hardcodear valores de configuración en la imagen.

- **Exponer puertos de manera explícita**:
  - Usa la instrucción `EXPOSE` para documentar qué puertos utiliza tu aplicación.

### Networking

- **Usar redes personalizadas**:
  - Crea redes Docker personalizadas para aislar grupos de contenedores relacionados.
  - Esto mejora la seguridad y facilita la comunicación entre contenedores.