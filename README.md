# Exp-04-Spring-Boot-with-REST-API-and-Hibernate-Integration

## Name: Jayakumar B
## Reg. No: 212223040073
## Date: 19/08/2026


## AIM

To develop a Spring Boot application to store and retrieve data from a Movies database using Object Relational Mapping (ORM) with Hibernate and expose it via REST APIs.

## ALGORITHM

### 1. Create Spring Boot Project

Create the project directly using **IntelliJ IDEA**.

Select the following:

* Project Name: `Movie REST API`
* Build System: Maven
* Language: Java
* JDK: 26
* Group: `com.example`
* Artifact: `movie-rest-api`
* Package Name: `com.example.movieapi`

Add the following dependencies:

* Spring Web
* Spring Data JPA
* H2 Database

### 2. Configure `application.properties`

Configure the H2 database connection and Hibernate JPA settings.

Enable Hibernate automatic table creation using:

```properties id="j5r0dj"
spring.jpa.hibernate.ddl-auto=create
```

Use port `8081` for the application.

### 3. Create the Movie Entity

Create a `Movie` entity class using the `@Entity` annotation.

Define the following fields:

* `id`
* `title`
* `genre`
* `year`
* `rating`

Use `@Id` and `@GeneratedValue` for the primary key.

The `year` field is mapped to the database column `release_year` to avoid a reserved-word conflict in the H2 database.

### 4. Create MovieRepository

Create a repository interface extending:

```java id="cv4w5w"
JpaRepository<Movie, Long>
```

This provides the required database operations.

### 5. Create MovieController

Create a REST controller using:

```java id="zpl9pp"
@RestController
@RequestMapping("/movies")
```

Define the following REST API endpoints:

* `GET /movies` → Get all movies
* `GET /movies/{id}` → Get movie by ID
* `POST /movies` → Add a movie
* `PUT /movies/{id}` → Update a movie
* `DELETE /movies/{id}` → Delete a movie

### 6. Run the Application

Run `MovieRestApiApplication.java` from IntelliJ IDEA.

### 7. Test the REST API

Use Postman to test all CRUD operations.

### 8. Stop the Application

Stop the Spring Boot application after completing the experiment.

---

## PROGRAM CODE

### Project Structure

```text id="tzk8lz"
Movie REST API/
├── src/
│   └── main/
│       ├── java/
│       │   └── com.example.movieapi/
│       │       ├── MovieRestApiApplication.java
│       │       ├── model/
│       │       │   └── Movie.java
│       │       ├── repository/
│       │       │   └── MovieRepository.java
│       │       └── controller/
│       │           └── MovieController.java
│       │
│       └── resources/
│           └── application.properties
│
└── pom.xml
```

---

## pom.xml

```xml id="0c2zt2"
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>4.1.1</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>movie-rest-api</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <name>Movie REST API</name>
    <description>Spring Boot REST API with Hibernate and H2</description>

    <properties>
        <java.version>26</java.version>
    </properties>

    <dependencies>

        <!-- Spring Web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Spring Data JPA -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!-- H2 Database -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

---

## application.properties

```properties id="0r0t5z"
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.hibernate.ddl-auto=create
spring.jpa.show-sql=true

spring.h2.console.enabled=true

server.port=8081
```

---

## Movie.java

```java id="8f0go7"
package com.example.movieapi.model;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Movie {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String genre;

    @Column(name = "release_year")
    private int year;

    private double rating;

    public Movie() {
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getGenre() {
        return genre;
    }

    public void setGenre(String genre) {
        this.genre = genre;
    }

    public int getYear() {
        return year;
    }

    public void setYear(int year) {
        this.year = year;
    }

    public double getRating() {
        return rating;
    }

    public void setRating(double rating) {
        this.rating = rating;
    }
}
```

---

## MovieRepository.java

```java id="xg0o2o"
package com.example.movieapi.repository;

import com.example.movieapi.model.Movie;
import org.springframework.data.jpa.repository.JpaRepository;

public interface MovieRepository extends JpaRepository<Movie, Long> {
}
```

---

## MovieController.java

```java id="2x8u0o"
package com.example.movieapi.controller;

import com.example.movieapi.model.Movie;
import com.example.movieapi.repository.MovieRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/movies")
public class MovieController {

    @Autowired
    private MovieRepository repo;

    @GetMapping
    public List<Movie> getAllMovies() {
        return repo.findAll();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Movie> getMovieById(@PathVariable Long id) {
        return repo.findById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PostMapping
    public Movie addMovie(@RequestBody Movie movie) {
        return repo.save(movie);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Movie> updateMovie(
            @PathVariable Long id,
            @RequestBody Movie movieDetails) {

        return repo.findById(id).map(movie -> {

            movie.setTitle(movieDetails.getTitle());
            movie.setGenre(movieDetails.getGenre());
            movie.setYear(movieDetails.getYear());
            movie.setRating(movieDetails.getRating());

            return ResponseEntity.ok(repo.save(movie));

        }).orElse(ResponseEntity.notFound().build());
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteMovie(@PathVariable Long id) {

        return repo.findById(id).map(movie -> {
            repo.delete(movie);
            return ResponseEntity.ok().<Void>build();
        }).orElse(ResponseEntity.notFound().build());
    }
}
```

---

## MovieRestApiApplication.java

```java id="1k3u3b"
package com.example.movieapi;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MovieRestApiApplication {

    public static void main(String[] args) {
        SpringApplication.run(MovieRestApiApplication.class, args);
    }
}
```

---

# REST API REQUESTS

## 1. CREATE MOVIE

### Method

```text id="sqt6ex"
POST
```

### URL

```text id="2c3jyk"
http://localhost:8081/movies
```

### JSON Body

```json id="9c6rjj"
{
    "title": "Inception",
    "genre": "Science Fiction",
    "year": 2010,
    "rating": 8.8
}
```

---

## 2. GET ALL MOVIES

### Method

```text id="yr6kqm"
GET
```

### URL

```text id="grt8f9"
http://localhost:8081/movies
```

---

## 3. GET MOVIE BY ID

### Method

```text id="tt9kpr"
GET
```

### URL

```text id="xn3m5y"
http://localhost:8081/movies/1
```

---

## 4. UPDATE MOVIE

### Method

```text id="20v5r8"
PUT
```

### URL

```text id="dj6mqs"
http://localhost:8081/movies/1
```

### JSON Body

```json id="f9bq1j"
{
    "title": "Inception Updated",
    "genre": "Sci-Fi",
    "year": 2010,
    "rating": 9.0
}
```

---

## 5. DELETE MOVIE

### Method

```text id="96oy9e"
DELETE
```

### URL

```text id="ykkmvz"
http://localhost:8081/movies/1
```

---

# OUTPUT

## Screenshot 1 – POST /movies

<img width="1920" height="1080" alt="Screenshot 2026-09-03 101841" src="https://github.com/user-attachments/assets/a69174a4-c833-4424-8be5-ddd6e3033d4f" />


---

## Screenshot 2 – GET /movies

<img width="1920" height="1080" alt="Screenshot 2026-09-03 102010" src="https://github.com/user-attachments/assets/e6a37b03-6e33-4c13-950c-b327c82f1a37" />


---

## Screenshot 3 – GET /movies/{id}
<img width="1920" height="1080" alt="Screenshot 2026-09-03 102048" src="https://github.com/user-attachments/assets/58bf742c-380c-45a3-bbc1-c90cfe8d3b01" />


---

## Screenshot 4 – PUT /movies/{id}

<img width="1920" height="1080" alt="Screenshot 2026-09-05 201541" src="https://github.com/user-attachments/assets/e4a9d143-b787-4fb1-ba7d-41ec9c195304" />


---

## Screenshot 5 – DELETE /movies/{id}

<img width="1920" height="1080" alt="Screenshot 2026-09-05 201625" src="https://github.com/user-attachments/assets/17d133ca-999f-4b42-9fa6-87d967a73b34" />


---

# RESULT

Thus, a Spring Boot application was successfully developed to store and retrieve movie data using **REST API, Hibernate, Spring Data JPA, and H2 Database**. CRUD operations were successfully implemented for the `Movie` entity.
