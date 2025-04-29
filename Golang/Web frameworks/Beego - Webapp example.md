
### Example: Web App in Beego

```go
package main

import (
	"github.com/beego/beego/v2/server/web"
	"html/template"
)

type Movie struct {
	ID       int
	Title    string
	Director string
	Year     int
}

var movies = []Movie{}

func main() {
	web.Router("/movies", &MovieController{}, "get:GetMovies;post:AddMovie")
	web.Router("/movies/:id", &MovieController{}, "get:GetMovieDetails")
	web.Router("/movies/create", &MovieController{}, "get:ShowAddMovieForm;post:AddMovie")
	web.Router("/movies/edit/:id", &MovieController{}, "get:ShowUpdateMovieForm;post:UpdateMovie")
	web.Router("/movies/delete/:id", &MovieController{}, "post:DeleteMovie")

	web.SetStaticPath("/static", "static")

	web.Run()
}

type MovieController struct {
	web.Controller
}

// GET /movies
func (mc *MovieController) GetMovies() {
	mc.Data["Movies"] = movies 
	mc.TplName = "movies.tpl"  
}

// GET /movies/:id
func (mc *MovieController) GetMovieDetails() {
	idStr := mc.Ctx.Input.Param(":id")
	id, err := strconv.Atoi(idStr)
	if err != nil {
		mc.Data["Error"] = "Invalid movie ID"
		mc.Abort("400")
		return
	}

	for _, movie := range movies {
		if movie.ID == id {
			mc.Data["Movie"] = movie
			mc.TplName = "movie_detail.tpl" 
			return
		}
	}

	mc.Data["Error"] = "Movie not found"
	mc.Abort("404")
}

// GET /movies/create
func (mc *MovieController) ShowAddMovieForm() {
	mc.TplName = "add_movie.tpl"
}

// POST /movies/create
func (mc *MovieController) AddMovie() {
	title := mc.GetString("title")       
	director := mc.GetString("director") 
	year, err := mc.GetInt("year")       

	if title == "" || director == "" || err != nil {
		mc.Data["Error"] = "All fields are required, and year must be a valid number!"
		mc.TplName = "add_movie.tpl"
		return
	}

	newMovie := Movie{
		ID:       len(movies) + 1,
		Title:    title,
		Director: director,
		Year:     year,
	}
	movies = append(movies, newMovie) 

	mc.Redirect("/movies", http.StatusFound)
}

// GET /movies/edit/:id
func (mc *MovieController) ShowUpdateMovieForm() {
	idStr := mc.Ctx.Input.Param(":id")
	id, err := strconv.Atoi(idStr)
	if err != nil {
		mc.Data["Error"] = "Invalid movie ID"
		mc.Abort("400")
		return
	}

	for _, movie := range movies {
		if movie.ID == id {
			mc.Data["Movie"] = movie
			mc.TplName = "update_movie.tpl"
			return
		}
	}

	mc.Data["Error"] = "Movie not found"
	mc.Abort("404")
}

// POST /movies/edit/:id
func (mc *MovieController) UpdateMovie() {
	idStr := mc.Ctx.Input.Param(":id")
	id, err := strconv.Atoi(idStr)
	if err != nil {
		mc.Data["Error"] = "Invalid movie ID"
		mc.Abort("400")
		return
	}

	title := mc.GetString("title")
	director := mc.GetString("director")
	year, err := mc.GetInt("year")

	if title == "" || director == "" || err != nil {
		mc.Data["Error"] = "All fields are required, and year must be a number."
		mc.Redirect("/movies/update/"+idStr, http.StatusFound)
		return
	}

	for i := range movies {
		if movies[i].ID == id {
			movies[i].Title = title
			movies[i].Director = director
			movies[i].Year = year
			break
		}
	}

	mc.Redirect("/movies", http.StatusFound)
}

// POST /movies/delete/:id
func (mc *MovieController) DeleteMovie() {
	idStr := mc.Ctx.Input.Param(":id")
	id, err := strconv.Atoi(idStr)
	if err != nil {
		mc.Data["Error"] = "Invalid movie ID"
		mc.Abort("400")
		return
	}

	for i, movie := range movies {
		if movie.ID == id {
			// Remove the movie by slicing
			movies = append(movies[:i], movies[i+1:]...)
			mc.Redirect("/movies", http.StatusFound) // Redirect to the movie list after deletion
			return
		}
	}

	mc.Data["Error"] = "Movie not found"
	mc.Abort("404") // Return 404 Not Found
}
```

---

#### Directory Structure

```
webapp/
├── conf/
│   └── app.conf
├── controllers/
│   └── movie.go
├── models/
├── static/
│   ├── css/
│   │   └── style.css
├── views/
│   ├── movies.tpl
│   ├── movie_detail.tpl
│   ├── add_movie.tpl
│   └── update_movie.tpl
└── main.go
```

---

#### HTML Template (`views/movies.tpl`)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Movie List</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <h1>Movie List</h1>
    <table border="1">
        <thead>
            <tr>
                <th>ID</th>
                <th>Title</th>
                <th>Director</th>
                <th>Year</th>
            </tr>
        </thead>
        <tbody>
            {{range .Movies}}
            <tr>
                <td>{{.ID}}</td>
                <td>{{.Title}}</td>
                <td>{{.Director}}</td>
                <td>{{.Year}}</td>
            </tr>
            {{end}}
        </tbody>
    </table>

    <h2>Add a New Movie</h2>
    <form method="post" action="/movies">
        <label for="title">Title:</label>
        <input type="text" id="title" name="title" required><br>
        <label for="director">Director:</label>
        <input type="text" id="director" name="director" required><br>
        <label for="year">Year:</label>
        <input type="number" id="year" name="year" required><br>
        <button type="submit">Add Movie</button>
    </form>
</body>
</html>
```

---

#### HTML Template (`views/movie_detail.tpl`)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Movie Details</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    {{if .Error}}
    <h1>Error</h1>
    <p>{{.Error}}</p>
    {{else}}
    <h1>Movie Details</h1>
    <table border="1">
        <tr>
            <th>ID</th>
            <td>{{.Movie.ID}}</td>
        </tr>
        <tr>
            <th>Title</th>
            <td>{{.Movie.Title}}</td>
        </tr>
        <tr>
            <th>Director</th>
            <td>{{.Movie.Director}}</td>
        </tr>
        <tr>
            <th>Year</th>
            <td>{{.Movie.Year}}</td>
        </tr>
    </table>
    <br>
    <a href="/movies">Back to Movie List</a>
    {{end}}
</body>
</html>
```

---

#### `views/add_movie.tpl` (Add New Movie Page)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Add New Movie</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <h1>Add a New Movie</h1>

    <!-- Show validation error, if any -->
    {{if .Error}}
    <p style="color: red;">{{.Error}}</p>
    {{end}}

    <!-- Form for adding a new movie -->
    <form method="post" action="/movies/add">
        <label for="title">Title:</label><br>
        <input type="text" id="title" name="title" required><br><br>

        <label for="director">Director:</label><br>
        <input type="text" id="director" name="director" required><br><br>

        <label for="year">Year:</label><br>
        <input type="number" id="year" name="year" required><br><br>

        <button type="submit">Add Movie</button>
    </form>

    <br>
    <a href="/movies">Back to Movie List</a>
</body>
</html>
```

---

#### `views/update_movie.tpl` (Update Movie Page)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Update Movie</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <h1>Update Movie</h1>

    <!-- Show error if exists -->
    {{if .Error}}
    <p style="color: red;">{{.Error}}</p>
    {{end}}

    <!-- Update form populated with current values -->
    {{if .Movie}}
    <form method="post" action="/movies/update/{{.Movie.ID}}">
        <label for="title">Title:</label><br>
        <input type="text" id="title" name="title" value="{{.Movie.Title}}" required><br><br>

        <label for="director">Director:</label><br>
        <input type="text" id="director" name="director" value="{{.Movie.Director}}" required><br><br>

        <label for="year">Year:</label><br>
        <input type="number" id="year" name="year" value="{{.Movie.Year}}" required><br><br>

        <button type="submit">Update Movie</button>
    </form>

    <br>
    <a href="/movies">Back to Movie List</a>
    {{end}}
</body>
</html>
```

---

#### Static CSS (`static/css/style.css`)

```css
body {
    font-family: Arial, sans-serif;
    margin: 20px;
}

table {
    width: 100%;
    border-collapse: collapse;
}

table, th, td {
    border: 1px solid black;
}

th, td {
    padding: 10px;
    text-align: left;
}

form {
    margin-top: 20px;
}

input, button {
    margin-bottom: 10px;
}
```
