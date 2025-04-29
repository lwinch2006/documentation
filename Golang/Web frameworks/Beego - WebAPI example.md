### Example: Web API in Beego

```go
package main

import (
	"encoding/json"
	"github.com/beego/beego/v2/server/web"
	"net/http"
	"strconv"
)

type MovieController struct {
	web.Controller
}

type Movie struct {
	ID       int    `json:"id"`
	Title    string `json:"title"`
	Director string `json:"director"`
	Year     int    `json:"year"`
}

var movies = []Movie{}

func main() {
	web.Router("/Movies", &MovieController{}, "post:CreateMovie;get:GetMovies")
	web.Router("/Movies/:id", &MovieController{}, "get:GetMovies;put:UpdateMovie;delete:DeleteMovie")

	web.Run()
}

// GET /Movies 
// GET /Movies/:id
func (mc *MovieController) GetMovies() {
	id := mc.Ctx.Input.Param(":id")
	if id == "" {
		mc.Data["json"] = movies
		mc.ServeJSON()
		return
	}

	movieID, err := strconv.Atoi(id)
	if err != nil || movieID <= 0 {
		mc.CustomAbort(http.StatusBadRequest, "Invalid movie ID")
		return
	}

	for _, movie := range movies {
		if movie.ID == movieID {
			mc.Data["json"] = movie
			mc.ServeJSON()
			return
		}
	}

	mc.CustomAbort(http.StatusNotFound, "Movie not found")
}

// POST /Movies
func (mc *MovieController) CreateMovie() {
	var movie Movie
	err := json.Unmarshal(mc.Ctx.Input.RequestBody, &movie)
	if err != nil {
		mc.CustomAbort(http.StatusBadRequest, "Invalid input data")
		return
	}

	movie.ID = len(movies) + 1
	movies = append(movies, movie)
	mc.Data["json"] = movie
	mc.ServeJSON()
}

// PUT /Movies/:id
func (mc *MovieController) UpdateMovie() {
	id := mc.Ctx.Input.Param(":id")
	movieID, err := strconv.Atoi(id)
	if err != nil || movieID <= 0 {
		mc.CustomAbort(http.StatusBadRequest, "Invalid movie ID")
		return
	}

	var updatedMovie Movie
	err = json.Unmarshal(mc.Ctx.Input.RequestBody, &updatedMovie)
	if err != nil {
		mc.CustomAbort(http.StatusBadRequest, "Invalid input data")
		return
	}

	for i, movie := range movies {
		if movie.ID == movieID {
			updatedMovie.ID = movieID
			movies[i] = updatedMovie
			mc.Data["json"] = updatedMovie
			mc.ServeJSON()
			return
		}
	}

	mc.CustomAbort(http.StatusNotFound, "Movie not found")
}

// DELETE /Movies/:id
func (mc *MovieController) DeleteMovie() {
	id := mc.Ctx.Input.Param(":id")
	movieID, err := strconv.Atoi(id)
	if err != nil || movieID <= 0 {
		mc.CustomAbort(http.StatusBadRequest, "Invalid movie ID")
		return
	}

	for i, movie := range movies {
		if movie.ID == movieID {
			// Delete the movie
			movies = append(movies[:i], movies[i+1:]...)
			mc.Data["json"] = map[string]string{"message": "Movie deleted"}
			mc.ServeJSON()
			return
		}
	}

	mc.CustomAbort(http.StatusNotFound, "Movie not found")
}
```
