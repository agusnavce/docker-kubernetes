# Resumen Completo de Docker

## Conceptos Fundamentales

- **Docker**: Plataforma de código abierto que automatiza el despliegue de aplicaciones dentro de contenedores de software.
- **Contenedor**: Unidad estándar de software que empaqueta el código y todas sus dependencias para que la aplicación se ejecute de manera rápida y confiable en diferentes entornos de computación.
- **Imagen**: Plantilla de solo lectura con instrucciones para crear un contenedor Docker. Contiene el código, runtime, librerías y dependencias necesarias para ejecutar una aplicación.
- **Dockerfile**: Archivo de texto que contiene todas las instrucciones necesarias para construir una imagen Docker de forma automatizada.
- **Docker Compose**: Herramienta para definir y ejecutar aplicaciones Docker multi-contenedor.
- **DockerHub**: Registro público de imágenes Docker, similar a un repositorio de GitHub para imágenes de contenedores.
- **Volumen**: Mecanismo preferido para persistir datos generados y utilizados por contenedores Docker.
- **Networks**: Sistema que permite la comunicación entre contenedores y con el mundo exterior.

## Comandos Docker Esenciales

- `docker container run`: Crea y ejecuta un nuevo contenedor.
- `docker container ls`: Lista los contenedores en ejecución.
- `docker container stop`: Detiene un contenedor en ejecución.
- `docker container rm`: Elimina un contenedor.
- `docker image ls`: Lista las imágenes disponibles localmente.
- `docker image rm`: Elimina una imagen local.
- `docker build`: Construye una imagen a partir de un Dockerfile.
- `docker pull`: Descarga una imagen desde un registro (como DockerHub).
- `docker push`: Sube una imagen a un registro.


## Almacenamiento en Docker

- **Volúmenes**: 
  - Mecanismo preferido para persistir datos.
  - Gestionados por Docker y almacenados en el sistema de archivos del host.
  - Fáciles de respaldar y migrar.
- **Bind mounts**: 
  - Montan un directorio o archivo específico del host en el contenedor.
  - Útiles para desarrollo y cuando se necesita acceso a archivos específicos del host.
- **tmpfs mounts**: 
  - Almacenamiento temporal en memoria.
  - Útil para datos sensibles o temporales que no deben persistir.

## Networking en Docker

- **Redes predeterminadas**:
  - `bridge`: Red predeterminada para contenedores. Proporciona conectividad NAT hacia el exterior.
  - `host`: El contenedor comparte la pila de red del host.
  - `none`: Aísla el contenedor, sin acceso a red.
- **Redes personalizadas**: Permiten aislar y conectar grupos específicos de contenedores.
- **Publicación de puertos**: Mapea puertos del contenedor a puertos del host para acceso externo.
- **DNS interno**: Los contenedores en la misma red pueden resolver nombres entre sí.



## Dockerfile y Construcción de Imágenes

- `FROM`: Especifica la imagen base.
- `RUN`: Ejecuta comandos en una nueva capa y crea una nueva imagen.
- `COPY` y `ADD`: Copian archivos desde el host a la imagen (ADD tiene funcionalidades adicionales).
- `ENV`: Establece variables de entorno.
- `EXPOSE`: Documenta los puertos en los que el contenedor escuchará.
- `CMD`: Define el comando predeterminado a ejecutar cuando se inicia el contenedor.
- `ENTRYPOINT`: Configura el contenedor para que se ejecute como un ejecutable.
- `VOLUME`: Crea un punto de montaje para volúmenes externos.
- `WORKDIR`: Establece el directorio de trabajo para las instrucciones siguientes.

## Docker Compose

- Herramienta para definir y ejecutar aplicaciones Docker multi-contenedor.
- Utiliza un archivo YAML (`docker-compose.yml`) para configurar los servicios, redes y volúmenes de la aplicación.
- Comandos principales:
  - `docker-compose up`: Inicia todos los servicios definidos.
  - `docker-compose down`: Detiene y elimina todos los recursos creados.
  - `docker-compose build`: Construye o reconstruye los servicios.
  - `docker-compose logs`: Muestra los logs de los servicios.

# Buenas Prácticas en Docker

## Construcción de Imágenes

1. **Usar imágenes base oficiales y confiables**
   - *Por qué*: Garantiza un punto de partida seguro y bien mantenido.

2. **Utilizar `.dockerignore`**
   - *Por qué*: Excluye archivos innecesarios, reduciendo el tamaño y evitando la inclusión de datos sensibles.

3. **Ordenar las instrucciones del Dockerfile eficientemente**
   - *Por qué*: Optimiza el uso de la caché, acelerando las construcciones posteriores.

4. **Usar etiquetas específicas para las imágenes base**
   - *Por qué*: Evita inconsistencias y asegura reproducibilidad.

5. **Utilizar construcciones multi-etapa**
   - *Por qué*: Permite crear imágenes más pequeñas separando el entorno de construcción del de ejecución.


6. **Minimizar el número de paquetes instalados**
   - *Por qué*: Reduce el tamaño de la imagen y la superficie de ataque.

### Ejemplo de Construcción Multi-Etapa en Docker

Este ejemplo demuestra cómo usar la construcción multi-etapa en Docker para crear una imagen final más pequeña y eficiente de una aplicación Go.

#### Dockerfile

```dockerfile
# Etapa de compilación
FROM golang:1.17-alpine AS build

# Establecer el directorio de trabajo
WORKDIR /app

# Copiar los archivos de código fuente
COPY go.mod ./
COPY go.sum ./
COPY *.go ./

# Descargar dependencias
RUN go mod download

# Compilar la aplicación
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .

# Etapa final
FROM alpine:latest  

# Instalar certificados CA
RUN apk --no-cache add ca-certificates

WORKDIR /root/

# Copiar el ejecutable compilado desde la etapa de compilación
COPY --from=build /app/main .

# Comando para ejecutar la aplicación
CMD ["./main"]
```

- Etapa de compilación

  - Usa golang:1.17-alpine como imagen base para compilar.
Copia los archivos fuente de Go al contenedor.
Descarga las dependencias y compila la aplicación.

- Etapa final

  - Usa alpine:latest como imagen base, que es mucho más ligera. Instala los certificados CA necesarios. Copia solo el ejecutable compilado desde la etapa de compilación.
- Beneficios

**Imagen final más pequeña**: La imagen final solo contiene el ejecutable y lo mínimo necesario para ejecutarlo, no las herramientas de compilación.
**Seguridad mejorada**: Al no incluir las herramientas de compilación en la imagen final, se reduce la superficie de ataque.
**Separación de preocupaciones**: La compilación y la ejecución están claramente separadas.

## Seguridad

8. **Evitar ejecutar contenedores como root**
   - *Por qué*: Limita el potencial daño en caso de una brecha de seguridad.

9.  **Escanear imágenes en busca de vulnerabilidades**
    - *Por qué*: Identifica y mitiga riesgos de seguridad conocidos.

10. **Gestionar secretos de manera segura**
    - *Por qué*: Previene la exposición de información sensible.

11. **Utilizar imágenes base minimalistas**
    - *Por qué*: Reduce la superficie de ataque y el tamaño de la imagen.

12. **Actualizar regularmente las imágenes base**
    - *Por qué*: Asegura que se incluyan los últimos parches de seguridad.

## Gestión de Datos

14. **Usar volúmenes para datos persistentes**
    - *Por qué*: Facilita la gestión y el respaldo de datos importantes.

15. **Separar datos de aplicación del código y configuración**
    - *Por qué*: Mejora la mantenibilidad y facilita las actualizaciones.

16. **Utilizar volúmenes nombrados en lugar de bind mounts cuando sea posible**
    - *Por qué*: Proporciona mayor flexibilidad y portabilidad.

## Rendimiento y Mantenibilidad

17. **Implementar health checks**
    - *Por qué*: Permite una mejor monitorización y auto-recuperación de contenedores.

18. **Documentar la imagen con LABEL y README**
    - *Por qué*: Facilita el uso y mantenimiento por parte de otros desarrolladores.

19. **Utilizar el flag `--rm` para contenedores de un solo uso**
    - *Por qué*: Evita la acumulación de contenedores no utilizados.


## Networking

25. **Usar redes personalizadas**
    - *Por qué*: Permite un mejor control y aislamiento entre grupos de contenedores.

26. **Limitar el acceso a la red cuando sea posible**
    - *Por qué*: Mejora la seguridad reduciendo la exposición innecesaria.

27. **Utilizar DNS interno de Docker para la comunicación entre contenedores**
    - *Por qué*: Simplifica la configuración y mejora la flexibilidad.

## Desarrollo y CI/CD

28. **Usar Docker Compose para entornos de desarrollo**
    - *Por qué*: Facilita la configuración y gestión de entornos multi-contenedor.

29. **Implementar pruebas automatizadas en el proceso de construcción**
    - *Por qué*: Asegura la calidad y detecta problemas tempranamente.

30. **Versionar las imágenes de Docker**
    - *Por qué*: Facilita el seguimiento de cambios y el rollback si es necesario.

| [&lt;-- Volver](5_Docker-Compose.md) |
