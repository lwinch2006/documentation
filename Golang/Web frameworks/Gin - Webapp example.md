
### Example: Web App in Gin

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

movies := []map[string]interface{}{
   {"ID": 1, "Title": "The Shawshank Redemption", "Genre": "Drama", "Year": 1994},
   {"ID": 2, "Title": "The Godfather", "Genre": "Crime", "Year": 1972},
   {"ID": 3, "Title": "The Dark Knight", "Genre": "Action", "Year": 2008},
}

func main() {
	r := gin.Default()
	r.LoadHTMLGlob("templates/*")
	r.Static("/static", "./static")

	r.GET("/movies", moviesPage)
	r.GET("/movies/:id", movieDetailPage)

	r.GET("/movies/create", showCreateMovieForm)   
	r.POST("/movies/create", handleCreateMovie)           

	r.GET("/movies/edit/:id", showUpdateMovieForm) 
	r.POST("/movies/edit/:id", handleUpdateMovie)  
	
	r.POST("/movies/delete/:id", deleteMovie)     

	r.Run(":8080")
}

// GET /movies
func moviesPage(c *gin.Context) {
	c.HTML(http.StatusOK, "movies.html", gin.H{
		"Title":  "Movies List",
		"Movies": movies,
	})
}

// GET /movies/:id
func movieDetailPage(c *gin.Context) {
	movieID := c.Param("id")

	movie, exists := movies[movieID]
	if !exists {
		c.HTML(http.StatusNotFound, "error.html", gin.H{
			"Message": "Movie not found!",
		})
		return
	}

	c.HTML(http.StatusOK, "movie_detail.html", gin.H{
		"Title":  movie["Title"],
		"Movie":  movie,
	})
}

// GET /movies/create
func showCreateMovieForm(c *gin.Context) {
	c.HTML(http.StatusOK, "create_movie.html", nil)
}

// POST /movies/create
func handleCreateMovie(c *gin.Context) {
	var movie Movie

	if err := c.ShouldBind(&movie); err != nil {
		c.HTML(http.StatusBadRequest, "create_movie.html", gin.H{
			"error": "Validation failed: " + err.Error(),
		})
		return
	}

	movies = append(movies, movie)

	c.HTML(http.StatusOK, "create_movie.html", gin.H{
		"success":   "Movie created successfully!",
		"movieList": movies,
	})
}

// GET /movies/edit/:id
func showUpdateMovieForm(c *gin.Context) {
	movieID := c.Param("id")

	var movie *Movie
	for _, m := range movies {
		if m.ID == movieID {
			movie = &m
			break
		}
	}

	if movie == nil {
		c.HTML(http.StatusNotFound, "error.html", gin.H{
			"Message": "Movie not found!",
		})
		return
	}

	c.HTML(http.StatusOK, "update_movie.html", gin.H{
		"Movie": movie,
	})
}

// POST /movies/edit/:id
func handleUpdateMovie(c *gin.Context) {
	movieID := c.Param("id")
	var updatedMovie Movie

	if err := c.ShouldBind(&updatedMovie); err != nil {
		c.HTML(http.StatusBadRequest, "error.html", gin.H{
			"Message": "Validation failed: " + err.Error(),
		})
		return
	}

	updated := false
	for i, movie := range movies {
		if movie.ID == movieID {
			// Update movie fields
			movies[i].Title = updatedMovie.Title
			movies[i].Genre = updatedMovie.Genre
			movies[i].Year = updatedMovie.Year
			updated = true
			break
		}
	}

	if !updated {
		c.HTML(http.StatusNotFound, "error.html", gin.H{
			"Message": "Movie not found!",
		})
		return
	}

	c.HTML(http.StatusOK, "update_movie.html", gin.H{
		"Success": "Movie updated successfully!",
		"Movie":   updatedMovie,
	})
}

// POST /movies/delete/:id
func deleteMovie(c *gin.Context) {
	movieID := c.Param("id")

	isDeleted := false
	for i, movie := range movies {
		if movie.ID == movieID {
			// Remove the movie from the slice
			movies = append(movies[:i], movies[i+1:]...)
			isDeleted = true
			break
		}
	}

	if !isDeleted {
		c.HTML(http.StatusNotFound, "error.html", gin.H{
			"Message": "Movie not found!",
		})
		return
	}

	c.Redirect(http.StatusSeeOther, "/movies")
}
```

---

### Directory Structure

```plaintext
.
├── main.go
├── templates          
│   ├── home.html
│   ├── movies.html
│   ├── movie_detail.html
│   ├── create_movie.html
│   ├── update_movie.html
│   └── error.html
├── static             
│   ├── css
│   │   └── style.css
│   └── js
│       └── app.js
```

---

### HTML Templates

**1. `movies.html` (Movies List Page)**

```html
<!DOCTYPE html>
<html>
<head>
    <title>{{ .Title }}</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <h1>{{ .Title }}</h1>
    <ul>
        {{ range .Movies }}
            <li>
                <a href="/movies/{{ .ID }}">{{ .Title }} ({{ .Year }})</a>
            </li>
        {{ end }}
    </ul>
    <a href="/">Go Back to Home</a>
</body>
</html>
```

---

**2. `movie_detail.html` (Movie Detail Page)**

```html
<!DOCTYPE html>
<html>
<head>
    <title>{{ .Title }}</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <h1>{{ .Movie.Title }}</h1>
    <p><strong>Genre:</strong> {{ .Movie.Genre }}</p>
    <p><strong>Year:</strong> {{ .Movie.Year }}</p>
    <p><strong>Description:</strong> {{ .Movie.Description }}</p>
    <a href="/movies">Back to Movies List</a>
</body>
</html>
```

---

**3. `create_movie.html` (Create Movie Page)**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Create Movie</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <h1>Create a New Movie</h1>

    <!-- Error message -->
    {{ if .error }}
        <p style="color: red;">{{ .error }}</p>
    {{ end }}

    <!-- Success message -->
    {{ if .success }}
        <p style="color: green;">{{ .success }}</p>
    {{ end }}

    <!-- Movie creation form -->
    <form action="/movies" method="post">
        <label>
            Movie ID:
            <input type="text" name="id" required>
        </label>
        <br>
        <label>
            Title:
            <input type="text" name="title" required minlength="2" maxlength="100">
        </label>
        <br>
        <label>
            Genre:
            <input type="text" name="genre" required>
        </label>
        <br>
        <label>
            Year:
            <input type="number" name="year" required min="1888" max="2024">
        </label>
        <br><br>
        <button type="submit">Create Movie</button>
    </form>

    <!-- Display list of movies (optional) -->
    {{ if .movieList }}
        <h2>Current Movies</h2>
        <ul>
            {{ range .movieList }}
                <li>{{ .ID }} - {{ .Title }} ({{ .Year }}) - {{ .Genre }}</li>
            {{ end }}
        </ul>
    {{ end }}
</body>
</html>
```

---

**4. `update_movie.html` (Update Movie Page)**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Update Movie</title>
</head>
<body>
    <h1>Update Movie</h1>

    <!-- Success message -->
    {{ if .Success }}
        <p style="color: green;">{{ .Success }}</p>
    {{ end }}

    <!-- Update form -->
    <form action="/movies/edit/{{ .Movie.ID }}" method="post">
        <label>
            Title:
            <input type="text" name="title" value="{{ .Movie.Title }}" required>
        </label>
        <br>
        <label>
            Genre:
            <input type="text" name="genre" value="{{ .Movie.Genre }}" required>
        </label>
        <br>
        <label>
            Year:
            <input type="number" name="year" value="{{ .Movie.Year }}" required>
        </label>
        <br><br>
        <button type="submit">Update Movie</button>
    </form>

    <br>
    <a href="/">Back to Home</a>
</body>
</html>
```

---

**5. `error.html` (Error Page)**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Error</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <h1>Error: {{ .Message }}</h1>
    <a href="/">Go Back to Home</a>
</body>
</html>
```

---

### Static Assets (Optional)

**CSS: `static/css/style.css`**

```css
body {
    font-family: Arial, sans-serif;
    margin: 20px;
}

a {
    text-decoration: none;
    color: blue;
}

a:hover {
    text-decoration: underline;
}
```
