# student-Redis
 
A Spring Boot REST API that stores and retrieves student data using **Redis** as the primary data store. This project is a hands-on example of how to use Spring Data Redis with `@RedisHash` — useful if you're learning how Redis works as a NoSQL database behind a real HTTP API.
 
---
 
## What This Project Does
 
It exposes a simple CRUD API for a `Student` entity. Instead of a relational database like MySQL or PostgreSQL, all data is stored directly in Redis as hashes. Spring Data Redis handles serialization and the repository pattern, so the code looks very similar to what you'd write with JPA/Hibernate — just pointed at Redis instead.
 
---
 
## Tech Stack
 
| Technology | Version |
|---|---|
| Java | 17 |
| Spring Boot | 4.0.6 |
| Spring Data Redis | (managed by Spring Boot) |
| Spring Web MVC | (managed by Spring Boot) |
| Lombok | (optional, compile-time only) |
| Maven | Wrapper included |
 
---
 
## Project Structure
 
```
student-Redis/
├── src/
│   └── main/
│       ├── java/com/praveen/
│       │   ├── StudentRedisApplication.java   # Entry point
│       │   ├── controller/
│       │   │   └── StudentController.java     # HTTP endpoints
│       │   ├── model/
│       │   │   └── Student.java               # Data model + @RedisHash
│       │   ├── repository/
│       │   │   └── StudentRepository.java     # CrudRepository for Redis
│       │   └── service/
│       │       └── StudentService.java        # Business logic layer
│       └── resources/
│           └── application.properties         # Redis connection config
└── pom.xml
```
---
 
## The Data Model
 
The `Student` class represents the object stored in Redis:
 
```java
@RedisHash("Student")
public class Student implements Serializable {
    @Id
    private Long id;
    private String name;
    private String email;
    private String course;
}
```
 
`@RedisHash("Student")` tells Spring Data Redis to store each object as a hash in Redis under keys like `Student:<id>`. The `@Id` field becomes part of the key. `Serializable` is required for objects stored in Redis.
 
---
 
## Prerequisites
 
Before running this project, make sure you have:
 
- **Java 17** or later installed
- **Maven** (or use the included `./mvnw` wrapper — no separate install needed)
- A **running Redis instance** — either local or remote
If you don't have Redis installed locally, the easiest option is Docker:
 
```bash
docker run -d -p 6379:6379 redis
```
 
---
 
## Configuration
 
Redis connection settings live in `src/main/resources/application.properties`:
 
```properties
spring.application.name=student-Redis
 
spring.data.redis.host=<your-redis-host>
spring.data.redis.port=<your-redis-port>
spring.data.redis.password=<your-redis-password>
```
 
> **Important:** The `application.properties` file currently contains credentials for a remote Redis instance. Before committing this project to any public repository, replace those values with your own, or better yet, use environment variables:
>
> ```properties
> spring.data.redis.host=${REDIS_HOST:localhost}
> spring.data.redis.port=${REDIS_PORT:6379}
> spring.data.redis.password=${REDIS_PASSWORD:}
> ```
 
If you're running Redis locally with no password, leave `spring.data.redis.password` blank or remove that line.
 
---
 
## Running the Application
 
Clone the repo and run:
 
```bash
# Using the Maven wrapper (no Maven install needed)
./mvnw spring-boot:run
 
# On Windows
mvnw.cmd spring-boot:run
```
 
The server starts on `http://localhost:8080` by default.
 
---
## API Reference
 
All endpoints are under `/students`.
 
### Create a Student
 
```
POST /students
Content-Type: application/json
 
{
  "id": 1,
  "name": "praveen",
  "email": "praveen@example.com",
  "course": "java, spring boot"
}
```
 
Returns the saved student object.
 
---
 
### Get a Student by ID
 
```
GET /students/{id}
```
 
Returns the student with that ID. Throws a `RuntimeException` (HTTP 500) if not found — you could improve this with a proper `@ExceptionHandler` returning a 404.
 
---
 
### Get All Students
 
```
GET /students
```
 
Returns a list of all stored students.
 
---
 
### Update a Student
 
```
PUT /students/{id}
Content-Type: application/json
 
{
  "name": "praveen kumar.",
  "email": "praveen@example.com",
  "course": "microservices"
}
```
 
Fetches the existing record, applies the new values, and saves it back to Redis.
 
---
 
### Delete a Student
 
```
DELETE /students/{id}
```
 
Returns the string `"Student Deleted"` on success.
 
---
 
## How the Layers Work Together
 
```
HTTP Request
    └── StudentController     (receives request, calls service)
            └── StudentService    (business logic, error handling)
                    └── StudentRepository  (talks to Redis via Spring Data)
                                └── Redis
```

This is a standard three-layer architecture. The `StudentRepository` extends `CrudRepository<Student, Long>` — Spring Data Redis auto-generates all the save/find/delete methods at startup. You don't write any Redis commands manually.

---
## Redis Commands
After Installation : This process we are doing for local set up 
```
redis-server
```
Open another terminal:
```
redis-cli
```
Test:
```
ping
```
Output:
```
PONG
```
## Redis CLI, Client, or Insight
properties
```
> spring.data.redis.host=${REDIS_HOST:localhost}
> spring.data.redis.port=${REDIS_PORT:6379}
> spring.data.redis.password=${REDIS_PASSWORD:}
 ```

Redis Insight Desktop Setup

Redis Insight is a graphical tool that helps you view and manage Redis databases without using commands.

**Step 1: Install Redis Insight**

Download Redis Insight from:
https://redis.io/insight/
Install the application.
Open Redis Insight after installation.
Create a Redis Cloud Database
**Step 1: Create a Redis Cloud Account**

Visit:
https://redis.io/cloud/
Sign up or log in.

**Step 2: Create a Database**
Click Create Database.
Enter a database name.

Example:

student-redis-db
Select the free plan.
Click Create Database.
**Step 3: Get Connection Details**
-we have select vendor-aws , region-india-mumbai and type-Redis

After the database is created, open it and copy:

Host
Port
Username
Password

Example:
```
Host     : xyz-12345.db.redis.io
Port     : *****
Username : default
Password : ********
```

Keep these details safe because they are required for Spring Boot and Redis Insight.


<img width="1588" height="901" alt="Screenshot 2026-06-07 165447" src="https://github.com/user-attachments/assets/5ee8322d-a0c3-4663-994b-9b35cadf4b82" />
