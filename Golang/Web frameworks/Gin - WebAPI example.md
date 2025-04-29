### Example: Web API in Gin

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

type Movie struct {
	ID     string `json:"id" binding:"required"`
	Title  string `json:"title" binding:"required"`
	Genre  string `json:"genre" binding:"required"`
	Year   int    `json:"year" binding:"required"`
}

var movies = []Movie{
	{ID: "1", Title: "The Shawshank Redemption", Genre: "Drama", Year: 1994},
	{ID: "2", Title: "The Godfather", Genre: "Crime", Year: 1972},
	{ID: "3", Title: "The Dark Knight", Genre: "Action", Year: 2008},
}

func main() {
	r := gin.Default()

	moviesGroup := r.Group("/Movies")
	{
		moviesGroup.GET("", listMovies)    
		moviesGroup.GET("/:id", getMovie)  
		moviesGroup.POST("", createMovie)  
		moviesGroup.PUT("/:id", updateMovie) 
		moviesGroup.DELETE("/:id", deleteMovie) 
	}

	r.Run(":8080")
}

// GET /Movies
func listMovies(c *gin.Context) {
	c.JSON(http.StatusOK, movies)
}

// GET /Movies/:id
func getMovie(c *gin.Context) {
	id := c.Param("id")
	for _, movie := range movies {
		if movie.ID == id {
			c.JSON(http.StatusOK, movie)
			return
		}
	}
	c.JSON(http.StatusNotFound, gin.H{"message": "Movie not found"})
}

// POST /Movies
func createMovie(c *gin.Context) {
	var newMovie Movie
	if err := c.ShouldBindJSON(&newMovie); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}

	movies = append(movies, newMovie)
	c.JSON(http.StatusCreated, newMovie)
}

// PUT /Movies/:id
func updateMovie(c *gin.Context) {
	id := c.Param("id")
	var updatedMovie Movie
	if err := c.ShouldBindJSON(&updatedMovie); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}

	for index, movie := range movies {
		if movie.ID == id {
			// Update the movie
			movies[index] = updatedMovie
			c.JSON(http.StatusOK, updatedMovie)
			return
		}
	}

	c.JSON(http.StatusNotFound, gin.H{"message": "Movie not found"})
}

// DELETE /Movies/:id
func deleteMovie(c *gin.Context) {
	id := c.Param("id")
	for index, movie := range movies {
		if movie.ID == id {
			// Delete movie
			movies = append(movies[:index], movies[index+1:]...)
			c.JSON(http.StatusOK, gin.H{"message": "Movie deleted"})
			return
		}
	}

	c.JSON(http.StatusNotFound, gin.H{"message": "Movie not found"})
}
```
