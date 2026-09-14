# Exp-04-Spring-Boot-with-REST-API-and-Hibernate-Integration
# Name: V. POOJAA SREE
# Register Number:212223040147
## AIM:

To develop a Spring Boot application to store and retrieve data from a Movies database using Object Relational Mapping (ORM) with Hibernate and expose it via REST APIs.

## ALGORITHM:

Create Spring Boot project with dependencies:

- Spring Web
- Spring Data JPA
- H2 or MySQL Database

Configure application.properties with DB connection and JPA settings.

Create Movie entity with fields like id, title, genre, rating, and year.

Create MovieRepository interface extending JpaRepository.

Create MovieController to define REST endpoints for CRUD operations:

- GET /movies
- GET /movies/{id}
- POST /movies
- PUT /movies/{id}
- DELETE /movies/{id}

## Program:

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>4.1.1</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>ex4</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name/>
	<description/>
	<url/>
	<licenses>
		<license/>
	</licenses>
	<developers>
		<developer/>
	</developers>
	<scm>
		<connection/>
		<developerConnection/>
		<tag/>
		<url/>
	</scm>
	<properties>
		<java.version>26</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-h2console</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webmvc</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webmvc-test</artifactId>
			<scope>test</scope>
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

### application.properties

```properties
spring.application.name=ex4
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

server.port=8081

```

### Movie.java

```java
package com.example.ex4;

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
    private double rating;
    private int releaseYear;

    public Movie() {
    }

    public Movie(String title, String genre, double rating, int releaseYear) {
        this.title = title;
        this.genre = genre;
        this.rating = rating;
        this.releaseYear = releaseYear;
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

    public double getRating() {
        return rating;
    }

    public void setRating(double rating) {
        this.rating = rating;
    }

    public int getReleaseYear() {
        return releaseYear;
    }

    public void setReleaseYear(int releaseYear) {
        this.releaseYear = releaseYear;
    }
}

```

### MovieRepository.java

```java

package com.example.ex4;
import org.springframework.data.jpa.repository.JpaRepository;

public interface MovieRepository extends JpaRepository<Movie, Long> {
}

```

### MovieController.java

```java

package com.example.ex4;
import org.springframework.web.bind.annotation.*;
        import java.util.List;

@RestController
@RequestMapping("/movies")
public class MovieController {

    private final MovieRepository movieRepository;

    // Constructor Injection
    public MovieController(MovieRepository movieRepository) {
        this.movieRepository = movieRepository;
    }

    // 1. GET /movies
    // Get all movies
    @GetMapping
    public List<Movie> getAllMovies() {
        return movieRepository.findAll();
    }

    // 2. GET /movies/{id}
    // Get one movie by ID
    @GetMapping("/{id}")
    public Movie getMovieById(@PathVariable Long id) {
        return movieRepository.findById(id).orElse(null);
    }

    // 3. POST /movies
    // Add a new movie
    @PostMapping
    public Movie addMovie(@RequestBody Movie movie) {
        return movieRepository.save(movie);
    }

    // 4. PUT /movies/{id}
    // Update an existing movie
    @PutMapping("/{id}")
    public Movie updateMovie(
            @PathVariable Long id,
            @RequestBody Movie movie) {

        Movie existingMovie =
                movieRepository.findById(id).orElse(null);

        if (existingMovie != null) {

            existingMovie.setTitle(movie.getTitle());
            existingMovie.setGenre(movie.getGenre());
            existingMovie.setRating(movie.getRating());
            existingMovie.setReleaseYear(movie.getReleaseYear());

            return movieRepository.save(existingMovie);
        }

        return null;
    }

    // 5. DELETE /movies/{id}
    // Delete a movie
    @DeleteMapping("/{id}")
    public String deleteMovie(@PathVariable Long id) {

        movieRepository.deleteById(id);

        return "Movie deleted successfully";
    }
}

```

## Output

### POST /movies

<img width="1907" height="975" alt="ex4 post" src="https://github.com/user-attachments/assets/77daae4a-7b56-4951-a40a-2e220cd55434" />

### GET /movies

<img width="1892" height="997" alt="ex 4 get" src="https://github.com/user-attachments/assets/cba7b241-bb7c-49a1-8152-00b369aca567" />

### GET /movies/{id}

<img width="1912" height="982" alt="ex 4 get id" src="https://github.com/user-attachments/assets/94585eb0-bf69-416b-8f56-b89cd883a876" />


### PUT /movies/{id}

<img width="1005" height="658" alt="image" src="https://github.com/user-attachments/assets/2f3c262e-ca83-423b-8062-5f185a4c45bb" />

### DELETE /movies/{id}

<img width="1910" height="1012" alt="ex 4 delete" src="https://github.com/user-attachments/assets/31f66ea0-460a-45d2-bf28-c09565fc64e6" />

### H2 CONSOLE

<img width="1907" height="836" alt="EX 4 H2 CONSOLE" src="https://github.com/user-attachments/assets/ce5593db-dfca-4f4d-88cf-01d50310d26d" />


## Result

Thus the development of a Spring Boot application to store and retrieve data from a Movies database is completed successfully
