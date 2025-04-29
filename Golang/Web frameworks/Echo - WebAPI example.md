### Example: Web API in Echo

```go
package main

import (
	"net/http"

	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
)

type Movie struct {
	ID    string `json:"id"`
	Title string `json:"title"`
	Year  int    `json:"year"`
	Genre string `json:"genre"`
}

var movies = []Movie{
	{ID: "1", Title: "Inception", Year: 2010, Genre: "Sci-Fi"},
	{ID: "2", Title: "The Dark Knight", Year: 2008, Genre: "Action"},
}

func main() {
	e := echo.New()
	e.Use(middleware.Logger())
	e.Use(middleware.Recover())

	e.GET("/Movies", getMovies)         
	e.GET("/Movies/:id", getMovieByID)  
	e.POST("/Movies", createMovie)      
	e.PUT("/Movies/:id", updateMovie)   
	e.DELETE("/Movies/:id", deleteMovie) 

	e.Logger.Fatal(e.Start(":8080"))
}

// GET /Movies
func getMovies(c echo.Context) error {
	return c.JSON(http.StatusOK, movies)
}

// GET /Movies/:id
func getMovieByID(c echo.Context) error {
	id := c.Param("id")
	for _, movie := range movies {
		if movie.ID == id {
			return c.JSON(http.StatusOK, movie)
		}
	}
	return c.JSON(http.StatusNotFound, map[string]string{"message": "Movie not found"})
}

// POST /Movies
func createMovie(c echo.Context) error {
	var newMovie Movie
	if err := c.Bind(&newMovie); err != nil {
		return c.JSON(http.StatusBadRequest, map[string]string{"message": "Invalid payload"})
	}
	newMovie.ID = string(len(movies) + 1)
	newMovie.ID = string(len(movies) + 1)
	movies = append(movies, newMovie)
	return c.JSON(http.StatusCreated, newMovie)
}

// PUT /Movies/:id
func updateMovie(c echo.Context) error {
	id := c.Param("id")
	var updatedMovie Movie
	if err := c.Bind(&updatedMovie); err != nil {
		return c.JSON(http.StatusBadRequest, map[string]string{"message": "Invalid payload"})
	}

	for i, movie := range movies {
		if movie.ID == id {
			movies[i] = updatedMovie
			updatedMovie.ID = id
			return c.JSON(http.StatusOK, updatedMovie)
		}
	}
	return c.JSON(http.StatusNotFound, map[string]string{"message": "Movie not found"})
}

// DELETE /Movies/:id
func deleteMovie(c echo.Context) error {
	id := c.Param("id")
	for i, movie := range movies {
		if movie.ID == id {
			movies = append(movies[:i], movies[i+1:]...)
			return c.JSON(http.StatusOK, map[string]string{"message": "Movie deleted"})
		}
	}
	return c.JSON(http.StatusNotFound, map[string]string{"message": "Movie not found"})
}

```
