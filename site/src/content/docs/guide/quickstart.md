---
title: Quickstart
description: Build a production-ready Echo API in under five minutes.
sidebar:
  order: 1
---

Echo is a high performance, minimalist Go web framework. This guide gets a server
running in under five minutes.

## Requirements

Echo requires **Go 1.25 or newer**. Check your version:

```bash
go version
```

## Install

Create a module and add Echo:

```bash
go mod init myapp
go get github.com/labstack/echo/v5
```

## Hello, World

Create `main.go`:

```go
package main

import (
	"net/http"

	"github.com/labstack/echo/v5"
	"github.com/labstack/echo/v5/middleware"
)

func main() {
	e := echo.New()

	e.Use(middleware.RequestLogger())
	e.Use(middleware.Recover())

	e.GET("/", func(c *echo.Context) error {
		return c.JSON(http.StatusOK, map[string]string{"message": "Hello, World!"})
	})

	if err := e.Start(":1323"); err != nil {
		e.Logger.Error("failed to start server", "error", err)
	}
}
```

Run it:

```bash
go run main.go
```

Your server is live at `http://localhost:1323`. Echo's router dispatches requests
with **zero dynamic memory allocation** per route.

:::tip[Ask Echo]
Stuck? Press the **Ask Echo** button (bottom-right) and ask
*"How do I add JWT auth?"* — answers come straight from these docs.
:::

## Next steps

- [Routing](/guide/routing/) — static, parameterized, and wildcard routes.
- [Context](/guide/context/) — the per-request request/response object.
- [Binding](/guide/binding/) — parse request data into typed structs.
go mod init research-api

go get github.com/labstack/echo/v4
go get github.com/jackc/pgx/v5
go get github.com/jackc/pgx/v5/pgxpool
go get github.com/golang-jwt/jwt/v5
go get github.com/go-playground/validator/v10
go get github.com/joho/godotenv
go get github.com/google/uuid

package main

import (
	"log"
	"os"

	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
)

func main() {
	e := echo.New()

	e.Use(middleware.Recover())
	e.Use(middleware.Logger())
	e.Use(middleware.CORS())

	api := e.Group("/api/v1")

	api.GET("/health", func(c echo.Context) error {
		return c.JSON(200, map[string]interface{}{
			"success": true,
			"data": map[string]string{
				"status": "ok",
			},
		})
	})

	registerRoutes(api)

	port := os.Getenv("PORT")
	if port == "" {
		port = "8080"
	}

	log.Fatal(e.Start(":" + port))
}

func registerRoutes(api *echo.Group) {
	api.GET("/projects", listProjects)
	api.POST("/projects", createProject)
	api.GET("/projects/:id", getProject)
	api.PATCH("/projects/:id", updateProject)

	api.GET("/strategies", listStrategies)
	api.GET("/research-plans", listResearchPlans)
	api.GET("/funding-sources", listFundingSources)
	api.GET("/dashboard/overview", dashboardOverview)
}
