# Exp-04-Spring-Boot-with-REST-API-and-Hibernate-Integration

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
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

### pom.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">

	<modelVersion>4.0.0</modelVersion>

	<!-- Parent -->
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.5.0</version>
		<relativePath/>
	</parent>

	<!-- Project Details -->
	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>demo</name>
	<description>Spring Boot REST API with Hibernate</description>

	<!-- Java Version -->
	<properties>
		<java.version>17</java.version>
	</properties>

	<!-- Dependencies -->
	<dependencies>

		<!-- Spring Boot Web -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<!-- Spring Boot JPA -->
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

		<!-- Spring Boot Test -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

	</dependencies>

	<!-- Build -->
	<build>
		<plugins>

			<!-- Spring Boot Maven Plugin -->
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>

		</plugins>
	</build>

</project>
```

### Movie.java

```
package com.example.movieapi.entity;

import jakarta.persistence.*;

@Entity
@Table(name = "movies")
public class Movie {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    private String genre;

    private int releaseYear;

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

    public int getReleaseYear() {
        return releaseYear;
    }

    public void setReleaseYear(int releaseYear) {
        this.releaseYear = releaseYear;
    }

    public double getRating() {
        return rating;
    }

    public void setRating(double rating) {
        this.rating = rating;
    }
}
```
### MovieRepository.java

```
package com.example.movieapi.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import com.example.movieapi.entity.Movie;

public interface MovieRepository extends JpaRepository<Movie, Long> {

}
```

### MovieController.java

```
package com.example.movieapi.controller;

import com.example.movieapi.entity.Movie;
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

    // GET ALL MOVIES
    @GetMapping
    public List<Movie> getAllMovies() {
        return repo.findAll();
    }

    // GET MOVIE BY ID
    @GetMapping("/{id}")
    public ResponseEntity<Movie> getMovieById(@PathVariable Long id) {

        return repo.findById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    // POST METHOD
    @PostMapping
    public Movie addMovie(@RequestBody Movie movie) {
        return repo.save(movie);
    }

    // PUT METHOD
    @PutMapping("/{id}")
    public ResponseEntity<Movie> updateMovie(
            @PathVariable Long id,
            @RequestBody Movie movieDetails) {

        return repo.findById(id).map(movie -> {

            movie.setTitle(movieDetails.getTitle());
            movie.setGenre(movieDetails.getGenre());
            movie.setReleaseYear(movieDetails.getReleaseYear());
            movie.setRating(movieDetails.getRating());

            return ResponseEntity.ok(repo.save(movie));

        }).orElse(ResponseEntity.notFound().build());
    }

    // DELETE METHOD
    @DeleteMapping("/{id}")
    public ResponseEntity<?> deleteMovie(@PathVariable Long id) {

        return repo.findById(id).map(movie -> {

            repo.delete(movie);

            return ResponseEntity.ok("Deleted Successfully!!!");

        }).orElse(ResponseEntity.notFound().build());
    }
}
```

### MovieApiApplication.java
```
package com.example.movieapi;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MovieApiApplication {

    public static void main(String[] args) {

        SpringApplication.run(MovieApiApplication.class, args);
    }
}
```

### Output
<img width="1414" height="751" alt="Screenshot 2026-05-22 135500" src="https://github.com/user-attachments/assets/a4811106-56f1-4f5e-80b6-f9a309a95aa7" />
<img width="1397" height="825" alt="Screenshot 2026-05-22 135517" src="https://github.com/user-attachments/assets/17cfeed4-9e5c-4492-a613-5e8b9f44a37e" />
<img width="1421" height="754" alt="Screenshot 2026-05-22 135731" src="https://github.com/user-attachments/assets/b378b8d2-63ba-486e-8a5e-0644634cf9f7" />
<img width="1425" height="730" alt="Screenshot 2026-05-22 135529" src="https://github.com/user-attachments/assets/1b56a013-cde3-4a4e-ae7d-5ecbea292858" />


### Result
Thus, the Spring Boot application using Hibernate and REST APIs was successfully developed and executed for performing CRUD operations on the Movies database.
The application successfully stores, retrieves, updates, and deletes movie records using Spring Data JPA and H2/MySQL database.
