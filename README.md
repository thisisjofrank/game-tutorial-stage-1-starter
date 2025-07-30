# 🦕 Dino Runner Game - Stage 1 Starter

Welcome to the Dino Runner Game tutorial! This is your **starting point** for
Stage 1, where you'll build the foundation of the game from scratch.

## What you'll build in this stage

By the end of Stage 1, you'll have:

- ✅ A web server using Deno and Oak framework
- ✅ Static file serving for HTML, CSS, and JavaScript
- ✅ Basic API endpoints with health checks
- ✅ A simple landing page for your game
- ✅ Project structure and configuration

## Getting started

### Prerequisites

- [Deno](https://deno.com/) installed on your system
- A code editor (VS Code recommended)
- Basic knowledge of TypeScript/JavaScript

### Setup

Deploy this starter kit and explore the code.

[![Deploy on Deno](https://deno.com/button)](https://app.deno.com/new?clone=https://github.com/thisisjofrank/game-tutorial-stage-1-starter.git&install=deno+install&mode=dynamic&entrypoint=src/main.ts)

This button will create a new GitHub repository with the code from this starter
kit and deploy it to Deno Deploy. Clone the created project to your local
machine to work through the code, every time you commit and push changes to the
github repository.

The project will initially fail to deploy to Deno Deploy, because we don't yet have any code in there, but as we build it out you'll be able to commit and push your work to the repository and see the changes live.

### Project configuration

First, let's set up the project configuration. Edit the `deno.json` file in the
root directory and set up some scripts to run your project:

<details>
<summary>📁 deno.json (click to expand)</summary>

```json
{
  "tasks": {
    "dev": "deno run --allow-net --allow-read --allow-env --watch src/main.ts",
    "start": "deno run --allow-net --allow-read --allow-env src/main.ts"
  }
}
```

Tasks are scripts you can run in your terminal, for example `deno task dev` to
start the development server.

Next we'll install the dependencies we need for this stage, we'll be using the
[Oak framework](https://jsr.io/@oak/oak) for building our web server:

```bash
deno add jsr:@oak/oak
```

This will add Oak to your project, and list it in your `deno.json` file under
`imports`.

</details>

### Environment configuration

Next, update your `.env` file to set the server port and host:

<details>
<summary>📁 .env (click to expand)</summary>

```env
# Server Configuration
PORT=8000

# Environment
ENVIRONMENT=development
```

</details>

At later stages we will add more environment variables for database connections
and feature flags.

### Create the server entry point

We're going to make a simple server that serves static files and handles API
routes. Servers are the backbone of web applications, allowing us to serve
content and handle requests, the entrypoint is where the server starts running.

Make a new directory called `src` and create the main server file at
`src/main.ts`.

We'll use the Oak framework to set up a simple HTTP server that serves static
files and handles API routes:

<details>
<summary>📁 src/main.ts (click to expand)</summary>

```typescript
// Import the Application class from Oak framework - this is our web server
import { Application } from "jsr:@oak/oak/application";
// Import our API routes that we'll create in a separate file
import { apiRouter } from "./routes/api.routes.ts";

// Get the port number from environment variables, or use 8000 as default
// parseInt() converts the string to a number since env vars are always strings
const PORT = parseInt(Deno.env.get("PORT") || "8000");
// Get the host from environment variables, or use localhost as default
const HOST = Deno.env.get("HOST") || "localhost";

// Create a new Oak application instance - this is our web server
const app = new Application();

// MIDDLEWARE 1: Serve static files from the 'public' directory
// This lets us serve HTML, CSS, JavaScript, and image files to browsers
app.use(async (context, next) => {
  try {
    // Try to send a file from the public directory
    await context.send({
      root: `${Deno.cwd()}/public`, // Look in the 'public' folder
      index: "index.html", // Default to index.html if no file specified
    });
  } catch {
    // If no file found, continue to the next middleware
    await next();
  }
});

// MIDDLEWARE 2: Add our API routes (like /api/health)
app.use(apiRouter.routes());
// Allow HTTP methods like GET, POST, PUT, DELETE
app.use(apiRouter.allowedMethods());

// Start the server and listen for incoming requests
app.listen({
  port: PORT,
});

// Print helpful messages to the console when the server starts
console.log(`🦕 Server is running on http://${HOST}:${PORT}`);
console.log(`🎯 Visit http://${HOST}:${PORT} to see the game`);
console.log(`🔧 API health check at http://${HOST}:${PORT}/api/health`);
```

</details>

### Create API routes

API routes are endpoints that allow clients (like browsers) to interact with our
server. They can be used for things like health checks, data retrieval, and
more.

Wwe will set up one simple API endpoint that checks if the server is running
correctly. This is useful for monitoring and debugging.

Make a new directory called `routes` inside `src`, and create the API routes
file at `src/routes/api.routes.ts`:

<details>
<summary>📁 src/routes/api.routes.ts (click to expand)</summary>

```typescript
import { Router } from "jsr:@oak/oak/router";

export const apiRouter = new Router({ prefix: "/api" });

// Health check endpoint
apiRouter.get("/health", (ctx) => {
  ctx.response.body = {
    status: "ok",
    message: "🦕 Stage 1 - Dino server is healthy!",
  };
});
```

</details>

### Create the HTML landing page

Our game will eventually run in a web browser, so we need a landing page that
serves as the entry point for players.

Create a new directory called `public`, and add the main HTML file at
`public/index.html`.

We'll make a simple HTML page with some basic styles, a status indicator and a
script to check server status:

<details>
<summary>📁 public/index.html (click to expand)</summary>

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="https://demo-styles.deno.deno.net/styles.css">
    <link rel="stylesheet" href="css/styles.css">
    <link rel="icon" href="favicon.ico" type="image/x-icon">
    <title>Dino Runner - Stage 1</title>
  </head>
  <body>
    <header>
      <h1>🦕 Dino Runner</h1>
    </header>

    <main class="container">
      <section class="hero">
        <h2>Welcome to Dino Runner!</h2>
        <p>
          You've successfully set up the foundation for your game. In the next
          stages, we'll add the game canvas, controls, and gameplay mechanics.
        </p>
      </section>

      <section class="container status">
        <h3>Server Status</h3>
        <p>
          Check that everything is working: <a
            href="/api/health"
            target="_blank"
          >Health Check Endpoint</a>
        </p>
        <p>
          <small>Should return:
            <code>{"status": "ok", "message": "🦕 Stage 1 - Dino server is
              healthy!"}</code></small>
        </p>
      </section>

      <section class="features">
        <h3>✅ Stage 1 Accomplishments</h3>
        <ul>
          <li>Set up Deno web server with Oak framework</li>
          <li>Configured static file serving</li>
          <li>Created basic API endpoints</li>
          <li>Established project structure</li>
          <li>Added environment configuration</li>
        </ul>
      </section>
    </main>

    <script src="js/game.js"></script>
  </body>
</html>
```

</details>

### Style the landing page

We've written some CSS for you to copy to style the landing page.

Create a new CSS file at `public/css/styles.css` and copy the contents from
[this CSS file](https://raw.githubusercontent.com/thisisjofrank/game-tutorial-stage-1/refs/heads/main/public/css/styles.css)

### Client-side JavaScript

The game will run in a web browser, so we need some client-side JavaScript to
handle interactions and game logic, for now we'll set up a simple script that
checks the server status.

Create the JavaScript file at `public/js/game.js`:

<details>
<summary>📁 public/js/game.js (click to expand)</summary>

```javascript
// Function to check if our server's health endpoint is working
// 'async' means this function can wait for network requests to complete
async function checkHealth() {
  try {
    // Make a GET request to our health endpoint
    // fetch() is a built-in browser function for making HTTP requests
    const response = await fetch("/api/health");

    // Convert the response from JSON text into a JavaScript object
    // await means "wait for this to finish before continuing"
    const data = await response.json();

    // Print success message with the server response to browser console
    console.log("🎉 Server health check:", data);
  } catch (error) {
    // If anything goes wrong (server down, network error, etc.)
    // catch the error and print a helpful message
    console.error("❌ Health check failed:", error);
  }
}

// Call the health check function as soon as this script loads
// This helps us verify the server is working correctly
checkHealth();

// Print some helpful status messages to the browser's developer console
// These help developers understand what stage of the tutorial they're on
console.log("🦕 Stage 1: Basic Deno + Oak setup loaded successfully!");
console.log("📋 This is the foundation for our 24-stage tutorial series");
console.log("🎯 Next: Stage 2 will add HTML5 Canvas and basic game structure");
```

</details>

### Verify your directory structure

Your finished project structure should look like this:

```text
stage-1-starter/
├── src/
│   ├── main.ts
│   └── routes/
│       └── api.routes.ts
├── public/
│   ├── index.html
│   ├── css/
│   │   └── styles.css
│   └── js/
│       └── game.js
├── deno.json
├── .env.example
├── .env
└── README.md
```

## Running your project

1. Install dependencies:
   ```bash
   deno install
   ```

2. Start the development server:
   ```bash
   deno task dev
   ```

3. Open your browser: Navigate to [http://localhost:8000](http://localhost:8000)

You should see your Dino Runner landing page a link to the server status check.

## Deploy your project to see your updates on Deno Deploy

Commit your changes and push to your GitHub repository. This will trigger
automatic deployment to Deno Deploy. If you open up the [Deno Deploy dashboard](https://app.deno.com), you will be able see your app build and deploy successfully.

## Learning objectives completed

After completing Stage 1, you should understand:

- [x] **Deno Basics:** How to set up a Deno project with a `deno.json`
- [x] **Oak Framework:** Setting up a web server with routing
- [x] **Static Files:** Serving HTML, CSS, and JavaScript files
- [x] **API Development:** Creating RESTful endpoints
- [x] **Environment Config:** Using environment variables
- [x] **Project Structure:** Organizing code for maintainability

## Ready for Stage 2?

Great job! You've built the foundation of your Dino Runner game. In Stage 2,
you'll add:

- HTML5 Canvas for game rendering
- Basic game loop and animation
- Keyboard and mouse input handling
- A jumping dino character with physics

**Continue to:**
[Stage 2 Starter](https://github.com/thisisjofrank/game-tutorial-stage-2-starter)

Happy coding! 🦕✨
