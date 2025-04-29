### Example: Web API in Fiber

```go
package main

import (
	"github.com/gofiber/fiber/v2"
)

func main() {
	app := fiber.New()

	movies := app.Group("/Movies")

	// GET /Movies
	movies.Get("/", func(c *fiber.Ctx) error {
		// Simulate returning a list of movies
		return c.JSON(fiber.Map{
			"movies": []fiber.Map{
				{"id": 1, "title": "Movie 1"},
				{"id": 2, "title": "Movie 2"},
			},
		})
	})
	
	// GET /Movies/:id
	movies.Get("/:id", func(c *fiber.Ctx) error {
		id := c.Params("id")
		// Simulate returning a single movie
		return c.JSON(fiber.Map{
			"movie": fiber.Map{"id": id, "title": "Movie Title"},
		})
	})


	movies.Post("/", func(c *fiber.Ctx) error {
		// Simulate request movie input
		// Normally, you would validate and parse a Movie from the request body
		return c.Status(fiber.StatusCreated).JSON(fiber.Map{
			"message": "Movie created successfully",
		})
	})

	// PUT /Movies/:id
	movies.Put("/:id", func(c *fiber.Ctx) error {
		id := c.Params("id")
		// Simulate updating a movie
		return c.JSON(fiber.Map{
			"message": "Movie updated successfully",
			"id":      id,
		})
	})

	// DELETE /Movies/:id
	movies.Delete("/:id", func(c *fiber.Ctx) error {
		id := c.Params("id")
		// Simulate deleting a movie
		return c.JSON(fiber.Map{
			"message": "Movie deleted successfully",
			"id":      id,
		})
	})

	app.Listen(":3000")
}
```
