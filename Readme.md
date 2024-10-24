# Express Middleware Guide

Express middleware functions are essential building blocks in Express.js applications. They have access to the request object (`req`), response object (`res`), and the next middleware function (`next`) in the application's request-response cycle.

## What is Middleware?

Middleware functions can:

- Execute any code
- Modify request and response objects
- End the request-response cycle
- Call the next middleware in the stack

## Types of Middleware

### 1. Application-level Middleware

Application-level middleware is bound to the `app` object using `app.use()`. These functions are executed for every request to the application.

```javascript
const express = require("express");
const app = express();

// Logger middleware
app.use((req, res, next) => {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
  next();
});

// Authorization middleware example
app.use((req, res, next) => {
  const apiKey = req.headers["x-api-key"];
  if (!apiKey) {
    return res.status(401).json({ error: "API key required" });
  }
  next();
});
```

### 2. Router-level Middleware

Router-level middleware works similarly to application middleware but is bound to an instance of `express.Router()`.

```javascript
const express = require("express");
const router = express.Router();

// Router-specific middleware
router.use((req, res, next) => {
  console.log("Time:", Date.now());
  next();
});

// Route handler
router.get("/user/:id", (req, res) => {
  res.send("User Info");
});

module.exports = router;
```

### 3. Error-handling Middleware

Error-handling middleware functions take four parameters (`err`, `req`, `res`, `next`). They are used to handle errors that occur during request processing.

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({
    status: "error",
    message: "Internal server error",
    error: process.env.NODE_ENV === "development" ? err.message : undefined,
  });
});
```

### 4. Built-in Middleware

Express comes with several built-in middleware functions:

```javascript
// Serve static files from 'public' directory
app.use(express.static("public"));

// Parse JSON payloads
app.use(express.json());

// Parse URL-encoded bodies
app.use(express.urlencoded({ extended: true }));
```

### 5. Third-party Middleware

Example using popular third-party middleware packages:

```javascript
const express = require("express");
const cookieParser = require("cookie-parser");
const morgan = require("morgan");
const helmet = require("helmet");
const app = express();

// Parse cookies
app.use(cookieParser());

// HTTP request logger
app.use(morgan("dev"));

// Security headers
app.use(helmet());
```

## Complete Example

Here's a complete example combining different types of middleware:

```javascript
const express = require("express");
const cookieParser = require("cookie-parser");
const app = express();

// Built-in middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Third-party middleware
app.use(cookieParser());

// Custom logging middleware
app.use((req, res, next) => {
  console.log(`${req.method} ${req.path}`);
  next();
});

// Route with middleware
app.get(
  "/api/data",
  // Authentication middleware
  (req, res, next) => {
    const isAuthenticated = checkAuth(req); // Implementation depends on your auth strategy
    if (!isAuthenticated) {
      return res.status(401).json({ error: "Unauthorized" });
    }
    next();
  },
  // Route handler
  (req, res) => {
    res.json({ message: "Success" });
  }
);

// Error handling middleware
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send("Something broke!");
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

## Best Practices

1. Always call `next()` unless you're ending the request-response cycle
2. Place error-handling middleware last
3. Keep middleware functions focused and modular
4. Use appropriate error handling
5. Be mindful of middleware order

## Installation

```bash
npm install express
# Additional middleware packages as needed
npm install cookie-parser morgan helmet
```

## Contributing

Feel free to submit issues and enhancement requests!

## License

MIT
