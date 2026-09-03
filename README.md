# Exp-04-Spring-Boot-with-REST-API-and-Hibernate-Integration
# Name: V. POOJAA SREE
# Register Number:212223040147
## AIM:
To develop a Spring Boot application to store and retrieve data from a Movies database using Object Relational Mapping (ORM) with Hibernate and expose it via REST APIs.

## ALGORITHM:
Create Spring Boot project with dependencies:

Spring Web

Spring Data JPA

H2 or MySQL Database

Configure application.properties with DB connection and JPA settings.

Create Movie entity with fields like id, title, genre, rating, and year.

Create MovieRepository interface extending JpaRepository.

Create MovieController to define REST endpoints for CRUD operations:

GET /movies

GET /movies/{id}

POST /movies

PUT /movies/{id}

DELETE /movies/{id}


## PROGRAM CODE (Main Files):
### application.properties
```
spring.application.name=MOVIE

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

```
package com.example.MOVIE;

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
```
package com.example.MOVIE;

import org.springframework.data.jpa.repository.JpaRepository;

public interface MovieRepository extends JpaRepository<Movie, Long> {
}
```
### MovieController.java
```
package com.example.MOVIE;

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
### MovieApplication.java
```
package com.example.MOVIE;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MovieApplication {

	public static void main(String[] args) {
		SpringApplication.run(MovieApplication.class, args);
	}
}
```
## OUTPUT:
## POST /movies
<img width="1920" height="1080" alt="Screenshot (1037)" src="https://github.com/user-attachments/assets/42a02114-1679-4e7f-a4f3-41d55a9a9c68" />

## GET /movies
<img width="1920" height="1080" alt="Screenshot (1038)" src="https://github.com/user-attachments/assets/9039d85b-26e1-4a3c-9ac4-5cfd30345223" />

## PUT /movies/{id}
<img width="1920" height="1080" alt="Screenshot (1040)" src="https://github.com/user-attachments/assets/aa5caa34-c694-40b3-84e1-82b4b01086e0" />

## DELETE /movies/{id}
<img width="1920" height="1080" alt="Screenshot (1041)" src="https://github.com/user-attachments/assets/f72f3625-ffe8-4370-a4f9-2a764cc85a98" />

## Result
Thus the development of a Spring Boot application to store and retrieve data from a Movies database is completed successfully
