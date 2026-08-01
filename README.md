# Todolist

A Spring Boot REST API for managing todo tasks with user registration and Basic Auth-protected task operations.

## Features

- Register new users
- Create tasks for authenticated users
- List tasks owned by the authenticated user
- Update existing tasks owned by the authenticated user
- H2 in-memory database for development
- Basic authentication for task endpoints

## Requirements

- Java 17
- Maven (or use the bundled Maven wrapper)

## Run locally

From the project root:

Windows:
```powershell
./mvnw.cmd spring-boot:run
```

Unix/macOS:
```bash
./mvnw spring-boot:run
```

Or build and run the packaged JAR:

```bash
./mvnw clean package
java -jar target/todolist-0.0.1-SNAPSHOT.jar
```

## Configuration

Application configuration is defined in `src/main/resources/application.properties`.

Default database settings:

- H2 in-memory database: `jdbc:h2:mem:todolist`
- username: `admin`
- password: `admin`
- H2 console enabled

## API Endpoints

### User Registration

`POST /users/`

Request body:
```json
{
  "username": "johndoe",
  "name": "John Doe",
  "password": "secret"
}
```

Response:
- `201 Created` on success
- `400 Bad Request` if the username already exists

### Task Endpoints

All `/tasks/` endpoints require HTTP Basic authentication.
Use registered user credentials in the `Authorization` header.

#### Create Task

`POST /tasks/`

Request body:
```json
{
  "title": "Finish homework",
  "description": "Complete chapter 5 exercises",
  "startAt": "2026-08-01T10:00:00",
  "endAt": "2026-08-01T12:00:00",
  "priority": "HIGH"
}
```

Response:
- `200 OK` with created task
- `400 Bad Request` when dates are invalid or missing

#### List Tasks

`GET /tasks/`

Response:
- `200 OK` with list of tasks for the authenticated user

#### Update Task

`PUT /tasks/{id}`

Request body can include any task fields to update.

Response:
- `200 OK` with updated task
- `400 Bad Request` if the task is not found or if the authenticated user is not the owner

## Authentication

Task endpoints use Basic Auth.

Set the request header:

```
Authorization: Basic <base64(username:password)>
```

Example using `curl`:

```bash
curl -X POST http://localhost:8080/tasks/ \
  -H "Authorization: Basic $(echo -n 'johndoe:secret' | base64)" \
  -H "Content-Type: application/json" \
  -d '{"title":"Finish homework","description":"Complete chapter 5","startAt":"2026-08-01T10:00:00","endAt":"2026-08-01T12:00:00","priority":"HIGH"}'
```

## H2 Console

The H2 console is enabled and available at:

```
http://localhost:8080/h2-console
```

JDBC URL: `jdbc:h2:mem:todolist`

## Notes

- The app uses Lombok for model classes.
- Passwords are hashed with BCrypt before storing.
- Task titles are limited to 50 characters.
- Date validation prevents using a task start or end time in the past.
