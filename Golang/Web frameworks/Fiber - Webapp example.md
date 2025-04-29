### Example: Webapp in Fiber

```go
package main

import (
	"github.com/gofiber/fiber/v2"
	"github.com/gofiber/template/html"
	"strconv"
)

type Movie struct {
	ID    int    `json:"id"`
	Title string `json:"title"`
	Genre string `json:"genre"`
	Year  int    `json:"year"`
}

var movies = []Movie{
	{ID: 1, Title: "Inception", Genre: "Sci-Fi", Year: 2010},
	{ID: 2, Title: "The Godfather", Genre: "Crime", Year: 1972},
	{ID: 3, Title: "The Dark Knight", Genre: "Action", Year: 2008},
}

func main() {
	engine := html.New("./templates", ".html")
	app := fiber.New(fiber.Config{
		Views: engine,
	})

	// GET /Movies
	app.Get("/Movies", func(c *fiber.Ctx) error {
		return c.Render("index", fiber.Map{
			"Title":  "Movie List",
			"Movies": movies,
		})
	})

	// GET /Movies/new
	app.Get("/Movies/new", func(c *fiber.Ctx) error {
		return c.Render("movie_create", fiber.Map{
			"Title": "Add New Movie",
		})
	})

	// POST /Movies/new
	app.Post("/Movies/new", func(c *fiber.Ctx) error {
		newMovie := new(Movie)

		// Parse form data into the Movie struct
		if err := c.BodyParser(newMovie); err != nil {
			return c.Status(fiber.StatusBadRequest).SendString("Failed to parse form data")
		}

		// Assign a unique ID
		if len(movies) > 0 {
			newMovie.ID = movies[len(movies)-1].ID + 1
		} else {
			newMovie.ID = 1
		}

		movies = append(movies, *newMovie)

		return c.Redirect("/Movies")
	})

	// GET /Movies/edit/:id
	app.Get("/Movies/edit/:id", func(c *fiber.Ctx) error {
		id, err := strconv.Atoi(c.Params("id"))
		if err != nil {
			return c.Status(fiber.StatusBadRequest).SendString("Invalid movie ID")
		}

		// Find the movie by its ID
		var movie Movie
		for _, m := range movies {
			if m.ID == id {
				movie = m
				break
			}
		}

		// If no movie is found
		if movie.ID == 0 {
			return c.Status(fiber.StatusNotFound).SendString("Movie not found")
		}

		// Render the edit page
		return c.Render("movie_update", fiber.Map{
			"Title": "Edit Movie",
			"Movie": movie,
		})
	})

	// POST /Movies/update/:id
	app.Post("/Movies/update/:id", func(c *fiber.Ctx) error {
		id, err := strconv.Atoi(c.Params("id"))
		if err != nil {
			return c.Status(fiber.StatusBadRequest).SendString("Invalid movie ID")
		}

		// Find the existing movie
		for i, movie := range movies {
			if movie.ID == id {
				// Update the movie details from the form
				if err := c.BodyParser(&movies[i]); err != nil {
					return c.Status(fiber.StatusBadRequest).SendString("Failed to parse form data")
				}

				// Ensure the ID stays the same
				movies[i].ID = id

				// Redirect back to the movie list page
				return c.Redirect("/Movies")
			}
		}

		// If movie not found
		return c.Status(fiber.StatusNotFound).SendString("Movie not found")
	})

	// POST /Movies/delete/:id
	app.Post("/Movies/delete/:id", func(c *fiber.Ctx) error {
		id, err := strconv.Atoi(c.Params("id"))
		if err != nil {
			return c.Status(fiber.StatusBadRequest).SendString("Invalid movie ID")
		}

		// Filter out the movie to be deleted
		for i, movie := range movies {
			if movie.ID == id {
				movies = append(movies[:i], movies[i+1:]...)
				break
			}
		}

		// Redirect back to the movie list page
		return c.Redirect("/Movies")
	})

	app.Listen(":3000")
}
```

---

### Templates

#### 1. Movie List Page (`templates/index.html`)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ .Title }}</title>
</head>
<body>
    <h1>{{ .Title }}</h1>
    <a href="/Movies/new">Add New Movie</a>
    <ul>
        {{ range .Movies }}
        <li>
            <strong>{{ .Title }}</strong> ({{ .Year }}) - {{ .Genre }}
            <a href="/Movies/edit/{{ .ID }}">Edit</a> |
            <a href="/Movies/delete/{{ .ID }}">Delete</a>
        </li>
        {{ end }}
    </ul>
</body>
</html>
```

#### 2. Create Movie Page (`templates/movie_create.html`)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ .Title }}</title>
</head>
<body>
    <h1>{{ .Title }}</h1>
    <form action="/Movies" method="POST">
        <label for="title">Title:</label>
        <input type="text" id="title" name="title" required><br><br>
        <label for="genre">Genre:</label>
        <input type="text" id="genre" name="genre" required><br><br>
        <label for="year">Year:</label>
        <input type="number" id="year" name="year" required><br><br>
        <button type="submit">Add Movie</button>
    </form>
    <a href="/Movies">Back to Movie List</a>
</body>
</html>
```

#### 3. Update Movie Page (`templates/movie_update.html`)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ .Title }}</title>
</head>
<body>
    <h1>{{ .Title }}</h1>
    <form action="/Movies/update/{{ .Movie.ID }}" method="POST">
        <label for="title">Title:</label>
        <input type="text" id="title" name="title" value="{{ .Movie.Title }}" required><br><br>
        <label for="genre">Genre:</label>
        <input type="text" id="genre" name="genre" value="{{ .Movie.Genre }}" required><br><br>
        <label for="year">Year:</label>
        <input type="number" id="year" name="year" value="{{ .Movie.Year }}" required><br><br>
        <button type="submit">Update Movie</button>
    </form>
    <a href="/Movies">Back to Movie List</a>
</body>
</html>
```

---

### Project Structure
```
/project
    /templates
        index.html        # Home page listing movies
        movie_create.html # Form to create a new movie
        movie_update.html # Form to update a movie
    main.go               # Application entry point
```
