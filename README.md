# JustDoItCloud · Backend

API REST para gestionar usuarios y sus tareas. Java 21, Spring Boot y MySQL.

[Inicio rápido](#inicio-rápido) · [Compilar](#compilar) · [Referencia de la API](docs/api.md)

## Inicio rápido

Necesita un **JDK 21**, Docker activo y acceso a internet para descargar las dependencias. El proyecto incluye Maven Wrapper; no necesita instalar Maven.

### 1. Crear la base de datos

Ejecute este comando una sola vez:

```sh
docker run -d --name justdoit-db \
  -p 127.0.0.1:3306:3306 \
  -e MYSQL_ROOT_PASSWORD=justdoit_root_local \
  -e MYSQL_DATABASE=justdoitdb \
  -e MYSQL_USER=justdoit \
  -e MYSQL_PASSWORD=justdoit_local \
  -v justdoit-db-data:/var/lib/mysql \
  mysql:8.4
```

Revise `docker logs justdoit-db` y espere a que MySQL termine de iniciar.

| Acción | Comando |
| --- | --- |
| Detener MySQL | `docker stop justdoit-db` |
| Volver a iniciar MySQL | `docker start justdoit-db` |
| Ver los logs | `docker logs justdoit-db` |

El volumen `justdoit-db-data` conserva los datos. Las variables `MYSQL_*` solo inicializan una base vacía; cambiarlas no actualiza las credenciales existentes.

> Las contraseñas del ejemplo son para desarrollo local. [Documentación de la imagen de MySQL](https://hub.docker.com/_/mysql).

### 2. Iniciar el backend

Desde la carpeta que contiene ambos proyectos, entre a `JD-Backend`:

```sh
cd JD-Backend
bash mvnw -version
```

Compruebe que Maven use Java **21**. Si muestra una versión anterior, configure `JAVA_HOME` con la ruta de su JDK 21.

En esa misma terminal, ejecute:

```sh
DB_USR_NAME=justdoit DB_PWD=justdoit_local bash mvnw spring-boot:run
```

Las credenciales deben acompañar al comando de inicio. La conexión está configurada en [application.properties](src/main/resources/application.properties) para `localhost:3306/justdoitdb`.

Al iniciar, el backend crea las tablas y carga estos usuarios de ejemplo:

| Username | Tipo |
| --- | --- |
| `john_doe` | `REGULAR` |
| `ana_soto` | `ADMIN` |

### 3. Consultar la API

Con el backend en ejecución, abra otra terminal:

```sh
curl http://localhost:8080/api/v1/users/byusername/ana_soto
```

Consulte los endpoints, campos y ejemplos en la [referencia de la API](docs/api.md).

## Compilar

Desde `JD-Backend`, genere el JAR sin ejecutar pruebas:

```sh
bash mvnw clean package -DskipTests
```

Para ejecutar el JAR, con MySQL activo:

```sh
DB_USR_NAME=justdoit DB_PWD=justdoit_local \
  java -jar target/JustDoItCloud-0.0.1-SNAPSHOT.jar
```

Use esta opción o `spring-boot:run` para iniciar el backend; ambos usan el puerto `8080`.
