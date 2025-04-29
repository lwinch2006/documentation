### Example: Web App in Echo

```go
package main

import (
	"html/template"
	"io"
	"net/http"

	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
)

type Movie struct {
	ID    string
	Title string
	Year  int
	Genre string
}

type TemplateRenderer struct {
	templates *template.Template
}

func (t *TemplateRenderer) Render(w io.Writer, name string, data interface{}, c echo.Context) error {
	return t.templates.ExecuteTemplate(w, name, data)
}

movies := []Movie{
   {ID: "1", Title: "Inception", Year: 2010, Genre: "Sci-Fi"},
   {ID: "2", Title: "The Dark Knight", Year: 2008, Genre: "Action"},
   {ID: "3", Title: "Interstellar", Year: 2014, Genre: "Adventure"},
}

func main() {
	e := echo.New()

	e.Use(middleware.Logger())
	e.Use(middleware.Recover())

	e.Static("/static", "public")

	e.Renderer = &TemplateRenderer{
		templates: template.Must(template.ParseGlob("views/*.html")),
	}

	e.GET("/", func(c echo.Context) error {
		// Render the index page
		return c.Render(http.StatusOK, "index.html", map[string]interface{}{
			"title":  "Movies List",
			"movies": movies,
		})
	})

	e.GET("/movie/:id", func(c echo.Context) error {
		// Find movie by ID
		id := c.Param("id")
		var movie *Movie
		for _, m := range movies {
			if m.ID == id {
				movie = &m
				break
			}
		}

		if movie == nil {
			return echo.NewHTTPError(http.StatusNotFound, "Movie not found")
		}

		// Render a page for the selected movie
		return c.Render(http.StatusOK, "movie.html", map[string]interface{}{
			"title": "Movie Details",
			"movie": movie,
		})
	})

	e.Logger.Fatal(e.Start(":8080"))
}
```

---

#### HTML Templates

1. **`layout.html`** (Base Layout):
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{{.title}}</title>
        <link rel="stylesheet" href="/static/styles.css">
    </head>
    <body>
        <header>
            <h1>Go Movie App</h1>
        </header>
        <main>
            {{block "content" .}}{{end}}
        </main>
        <footer>
            <p>&copy; 2023 MovieApp. All rights reserved.</p>
        </footer>
    </body>
    </html>
    ```

2. **`index.html`** (Movies Page):
    ```html
    {{template "layout.html" .}}

    {{define "content"}}
    <h2>Movies List</h2>
    <ul>
        {{range .movies}}
        <li>
            <a href="/movie/{{.ID}}">{{.Title}} ({{.Year}})</a> - {{.Genre}}
        </li>
        {{else}}
        <p>No movies available.</p>
        {{end}}
    </ul>
    {{end}}
    ```

3. **`movie.html`** (Movie Details Page):
    ```html
    {{template "layout.html" .}}

    {{define "content"}}
    <h2>{{.movie.Title}}</h2>
    <p><strong>Year:</strong> {{.movie.Year}}</p>
    <p><strong>Genre:</strong> {{.movie.Genre}}</p>
    <a href="/">Back to Movies List</a>
    {{end}}
    ```

---

#### Static Files

Place your CSS, JavaScript, and other static assets in the `public/` directory. For example:

- **`public/styles.css`**:
    ```css
    body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 0;
    }

    header {
        background: #333;
        color: #fff;
        padding: 1rem;
        text-align: center;
    }

    main {
        padding: 2rem;
    }

    footer {
        background: #f4f4f4;
        text-align: center;
        padding: 1rem;
        margin-top: 2rem;
    }

    a {
        color: #007BFF;
        text-decoration: none;
    }

    a:hover {
        text-decoration: underline;
    }
    ```

---

#### Directory Structure
Here’s a sample directory structure for the app:

```
app/
├── main.go            # Entry point of the application
├── views/             # Directory containing HTML templates
│   ├── index.html
│   ├── movie.html
│   └── layout.html
└── public/            # Static files (CSS, JS, images)
    ├── styles.css
    └── script.js
```
