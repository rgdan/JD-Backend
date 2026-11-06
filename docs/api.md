# Referencia de la API

[Volver al README](../README.md) · [Usuarios](#usuarios) · [Tareas](#tareas) · [Ejemplos con curl](#ejemplos-con-curl)

**URL base:** `http://localhost:8080/api/v1`. La API intercambia JSON y no requiere una sesión iniciada. Para enviar JSON, incluya `Content-Type: application/json`.

También puede importar la [colección de Postman](../postman/JustDoItCloudAPICollection.postman_collection.json).

## Usuarios

| Método | Endpoint | Descripción | Respuesta exitosa |
| --- | --- | --- | --- |
| `GET` | `/api/v1/users` | Obtener todos los usuarios. | `200 OK`, arreglo JSON |
| `GET` | `/api/v1/users/{id}` | Obtener un usuario por su ID. | `200 OK`, objeto de usuario; `404 Not Found` si no existe |
| `GET` | `/api/v1/users/byusername/{username}` | Obtener un usuario por su username. | `200 OK`, objeto de usuario; `404 Not Found` si no existe |
| `POST` | `/api/v1/users` | Crear un usuario. | `201 Created`, objeto del usuario creado |
| `PATCH` | `/api/v1/users/{id}` | Actualizar los campos enviados que sean editables. | `200 OK`, objeto del usuario actualizado |
| `DELETE` | `/api/v1/users/{id}` | Eliminar un usuario. | `204 No Content` |

### Campos de usuario

| Campo | Tipo | Notas |
| --- | --- | --- |
| `id` | integer | Lo genera el servidor. |
| `userName` | string | Obligatorio al crear un usuario; debe ser único. |
| `name` | string | Opcional. |
| `email` | string | Opcional. |
| `type` | string | `ADMIN` o `REGULAR`. |
| `createdAt` | string | Fecha y hora administradas por el servidor, en formato ISO-8601. |

`PATCH` permite actualizar `name`, `email` y `type`. Los campos omitidos no cambian. Este endpoint no modifica `userName`.

### Crear un usuario

```http
POST /api/v1/users
Content-Type: application/json

{
  "userName": "maria_rojas",
  "name": "María Rojas",
  "email": "maria@example.com",
  "type": "REGULAR"
}
```

### Actualizar un usuario

```http
PATCH /api/v1/users/1
Content-Type: application/json

{
  "email": "maria.new@example.com"
}
```

## Tareas

Los endpoints de tareas están asociados a la ruta de un usuario. `{userId}` es obligatorio en todos los endpoints de tareas.

| Método | Endpoint | Descripción | Respuesta exitosa |
| --- | --- | --- | --- |
| `GET` | `/api/v1/users/{userId}/tasks` | Obtener las tareas del usuario. | `200 OK`, arreglo JSON |
| `POST` | `/api/v1/users/{userId}/tasks` | Crear una tarea para el usuario. | `201 Created`, objeto de la tarea creada |
| `GET` | `/api/v1/users/{userId}/tasks/{id}` | Obtener una tarea por su ID. | `200 OK`, objeto de tarea; `404 Not Found` si no existe |
| `PATCH` | `/api/v1/users/{userId}/tasks/{id}` | Actualizar los campos de tarea admitidos. | `200 OK`, objeto de la tarea actualizada |
| `DELETE` | `/api/v1/users/{userId}/tasks/{id}` | Eliminar una tarea por su ID. | `204 No Content` |

### Campos de tarea

| Campo | Tipo | Notas |
| --- | --- | --- |
| `id` | integer | Lo genera el servidor. |
| `description` | string | Texto de la tarea; debe tener al menos 3 caracteres cuando se proporciona. |
| `createdAt` | string | Fecha y hora administradas por el servidor, en formato ISO-8601. |
| `deadline` | string | Fecha opcional en formato `YYYY-MM-DD`. |
| `status` | string | `PENDING`, `INPROGRESS` o `DONE`; obligatorio al crear una tarea. |
| `userId` | integer | Se establece a partir del parámetro `{userId}` de la ruta al crear la tarea. |

### Crear una tarea

```http
POST /api/v1/users/1/tasks
Content-Type: application/json

{
  "description": "Preparar las notas del proyecto",
  "status": "PENDING",
  "deadline": "2026-11-01"
}
```

### Generar una tarea aleatoria

Con `autogenerate=true` se ignora el cuerpo de la solicitud. Un valor vacío para `autogenerate` también activa la generación:

```http
POST /api/v1/users/1/tasks?autogenerate=true
```

### Actualizar una tarea

Los campos omitidos no cambian.

```http
PATCH /api/v1/users/1/tasks/12
Content-Type: application/json

{
  "description": "Revisar y actualizar las notas del proyecto",
  "status": "INPROGRESS"
}
```

## Ejemplos con curl

```sh
curl http://localhost:8080/api/v1/users

curl -X POST http://localhost:8080/api/v1/users/1/tasks \
  -H "Content-Type: application/json" \
  -d '{"description":"Preparar las notas del proyecto","status":"PENDING"}'

curl -X DELETE http://localhost:8080/api/v1/users/1/tasks/12
```

## Convenciones

- Reemplace los IDs de las rutas por los valores numéricos que devuelve la API.
- Para crear una tarea sin generación automática, debe enviar un cuerpo JSON con los datos de la tarea.
- Los IDs de usuarios y tareas los genera la base de datos. Al crear una tarea, el servidor establece `createdAt` y la asocia con el `userId` indicado en la ruta.
