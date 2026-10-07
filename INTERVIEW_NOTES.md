# ShopLane - Technical Interview Preparation Guide & File Notes

This guide provides a plain-language, line-by-line explanation of every core file in **ShopLane**, followed by **30 targeted placement interview questions with concise answers** tailored specifically to this codebase.

---

# Part 1: File-by-File Plain-Language Codebase Breakdown

## Backend Files (`/server`)

### 1. `server/server.js` (Server Entry Point)
- **Role**: Starts the HTTP server and loads environment variables.
- **Key Concepts**:
  - `dotenv.config()` loads variables from `.env` into `process.env`.
  - Calls `connectDB()` to connect to MongoDB Atlas if `MONGODB_URI` exists.
  - `app.listen(PORT, ...)` opens port 5000 for incoming HTTP requests.
  - `process.on('unhandledRejection')` acts as a safety net for uncaught promise errors.

### 2. `server/src/app.js` (Express App Configuration)
- **Role**: Assembles Express middleware and mounts API routes.
- **Key Concepts**:
  - `express()` initializes the application instance.
  - `cors()` enables cross-origin requests from the React frontend running on port 5173.
  - `express.json()` parses incoming JSON request bodies (`req.body`).
  - `app.use('/api/products', productRoutes)` mounts feature routers.
  - `notFoundHandler` and `errorHandler` handle 404s and runtime errors centrally.

### 3. `server/src/config/db.js` (Database Connection)
- **Role**: Manages the Mongoose connection to MongoDB Atlas.
- **Key Concepts**:
  - `mongoose.connect(process.env.MONGODB_URI)` initiates the asynchronous connection.
  - Uses `try...catch` block to log successful connection host or gracefully catch errors.

### 4. `server/src/models/Product.js`, `User.js`, `Order.js` (Mongoose Schemas)
- **Product.js**: Defines fields (`name`, `price`, `category`, `rating`, `stock`, `imageUrl`). Contains a compound text index `{ name: 'text', description: 'text' }` for fast search.
- **User.js**: Defines user schema and includes a Mongoose `pre('save')` hook that uses `bcrypt.hash()` to encrypt passwords before saving. Includes instance method `matchPassword()` for login validation.
- **Order.js**: Stores a snapshot of ordered items (`product`, `name`, `price`, `quantity`), shipping address, total amount, and status (`Processing`).

### 5. `server/src/routes/productRoutes.js` (Product REST API)
- **Role**: Serves public product queries.
- **Key Concepts**:
  - `GET /api/products`: Reads query parameters (`search`, `category`, `sort`, `page`, `limit`).
  - Regex search: `{ name: { $regex: search, $options: 'i' } }` enables case-insensitive pattern matching.
  - Pagination: `.skip((page - 1) * limit).limit(limit)` retrieves exact data slices.

### 6. `server/src/routes/authRoutes.js` (User Registration & Login)
- **Role**: Handles user onboarding and authentication.
- **Key Concepts**:
  - `POST /api/auth/register`: Validates input fields, checks email uniqueness, creates User document, and returns signed JWT token.
  - `POST /api/auth/login`: Compares passwords with `matchPassword()`, issuing a JWT on success.

### 7. `server/src/middleware/authMiddleware.js` (JWT Route Guard)
- **Role**: Restricts access to cart and order endpoints.
- **Key Concepts**:
  - Checks `req.headers.authorization` for `Bearer <token>`.
  - Verifies token using `jwt.verify(token, secret)`.
  - Attaches authenticated user object to `req.user` for downstream handlers.

### 8. `server/src/middleware/errorMiddleware.js` (Central Error Middleware)
- **Role**: Ensures consistent JSON error output `{ success: false, message: ... }`.
- **Key Concepts**:
  - Formats Mongoose `ValidationError` and `CastError` into user-friendly HTTP status codes (400, 404).

---

## Frontend Files (`/client`)

### 9. `client/src/utils/api.js` (Fetch Helper Wrapper)
- **Role**: Standardizes all network requests to the backend.
- **Key Concepts**:
  - Reads `localStorage.getItem('shoplane_token')` and automatically attaches `Authorization: Bearer <token>` headers.
  - Throws standardized errors if `response.ok` is false.

### 10. `client/src/context/AuthContext.jsx` (Global Authentication State)
- **Role**: Shares user login status across all components.
- **Key Concepts**:
  - Manages `user` and `token` state.
  - Persists JWT token in `localStorage` so user stays logged in across page refreshes.

### 11. `client/src/context/CartContext.jsx` (Global Shopping Cart State)
- **Role**: Manages cart items using `useReducer`.
- **Key Concepts**:
  - Reducer functions handle actions (`ADD_ITEM`, `REMOVE_ITEM`, `UPDATE_QTY`, `CLEAR_CART`).
  - Computes `itemCount` and `totalAmount` dynamically.
  - Syncs state changes with the backend `/api/cart` endpoint when logged in.

### 12. `client/src/pages/HomePage.jsx` (Product Catalog Page)
- **Role**: Primary landing page for browsing products.
- **Key Concepts**:
  - **Debounced Search**: Uses `setTimeout` inside `useEffect` (300ms delay) to prevent sending API requests on every keystroke.
  - Manages category pill state, sort dropdown state, and pagination numbers.

### 13. `client/src/pages/CartPage.jsx` & `OrderHistoryPage.jsx`
- **CartPage.jsx**: Displays interactive cart items, quantity modifiers, total calculation, and triggers `POST /api/orders` checkout.
- **OrderHistoryPage.jsx**: Fetches user past orders from `GET /api/orders` and renders order status cards.

---

# Part 2: 30 Likely Interview Questions & Concise Answers

## Section A: JavaScript & Asynchronous Programming (10 Questions)

#### Q1: What is the difference between `null` and `undefined` in JavaScript?
**Answer**: `undefined` means a variable has been declared but has not yet been assigned a value. `null` is an explicit assignment representing the intentional absence of an object value.

#### Q2: Explain Promises and how `async/await` works under the hood.
**Answer**: A Promise represents the eventual completion or failure of an asynchronous operation. `async/await` is syntactic sugar built on top of Promises that allows asynchronous code to be written sequentially using `try...catch` blocks.

#### Q3: What is Event Delegation and why is it useful?
**Answer**: Event Delegation is a pattern where a single event listener is attached to a parent element to manage events triggered by its child elements, utilizing event bubbling. It reduces memory usage and simplifies dynamic DOM handling.

#### Q4: How does debouncing work in our search bar (`HomePage.jsx`)?
**Answer**: Debouncing delays function execution until a specified period (e.g., 300ms) has elapsed since the last event. In `HomePage.jsx`, `setTimeout` waits for the user to stop typing before updating `debouncedSearch`, preventing unnecessary fetch API calls on every keystroke.

#### Q5: What is the difference between `==` and `===`?
**Answer**: `==` (loose equality) performs type coercion before comparing values, whereas `===` (strict equality) checks both value and data type without coercion.

#### Q6: How does `Array.prototype.reduce()` work in our cart total calculation?
**Answer**: `reduce()` executes a reducer function on each element of an array, accumulating a single result value. We use it in `CartContext.jsx` to sum `(item.price * item.quantity)` across all cart items.

#### Q7: What are Closures in JavaScript?
**Answer**: A closure is a function that retains access to its lexical scope even when executed outside that scope. In React, hooks like `useState` and `useEffect` rely on closures to retain state across renders.

#### Q8: What is the difference between `localStorage` and `sessionStorage`?
**Answer**: `localStorage` persists data across browser sessions until explicitly cleared. `sessionStorage` clears data automatically when the browser tab or window is closed. We use `localStorage` for JWT tokens.

#### Q9: What is the Event Loop in JavaScript?
**Answer**: The Event Loop is the single-threaded mechanism in JavaScript that handles asynchronous callbacks by pushing completed tasks from the Callback Queue onto the Call Stack when it is empty.

#### Q10: How do arrow functions differ from traditional functions regarding the `this` keyword?
**Answer**: Arrow functions do not bind their own `this`; instead, they lexically inherit `this` from their enclosing scope.

---

## Section B: React 18 & State Management (10 Questions)

#### Q11: What is the Virtual DOM and how does React update the UI?
**Answer**: The Virtual DOM is a lightweight in-memory representation of the real DOM. When state changes, React creates a new Virtual DOM tree, compares it with the previous tree using a diffing algorithm ("reconciliation"), and updates only the changed elements in the real DOM.

#### Q12: Why do we use `useReducer` instead of `useState` in `CartContext.jsx`?
**Answer**: `useReducer` is preferred for complex state logic that involves multiple sub-values (`items`, `totalAmount`, `itemCount`) or when the next state depends on the previous state. It centralizes state mutations in a pure reducer function.

#### Q13: What is the purpose of the `useEffect` cleanup function?
**Answer**: The cleanup function returned by `useEffect` runs before the component unmounts or before the effect re-runs. We use it to clear `setTimeout` timers in our debounced search to prevent memory leaks and stale state updates.

#### Q14: What is the Context API and what problem does it solve?
**Answer**: The Context API provides a way to pass data through the component tree without manually passing props down at every level ("prop drilling"). We use it in `AuthContext` and `CartContext`.

#### Q15: Why are `key` props important in React lists (`product-grid`)?
**Answer**: `key` props help React identify which items in a list have changed, been added, or been removed. Passing unique IDs (`product._id`) ensures efficient reconciliation and prevents DOM state rendering bugs.

#### Q16: What is the difference between controlled and uncontrolled components?
**Answer**: A controlled component has its form input value bound to React state via `value` and `onChange`. An uncontrolled component maintains its own internal DOM state accessed via React `refs`. Our forms use controlled components.

#### Q17: What is `<React.StrictMode>`?
**Answer**: `<React.StrictMode>` is a developer tool that highlights potential problems in an application by invoking lifecycle methods and hooks twice in development to catch side effects and deprecated APIs.

#### Q18: What is the role of `react-router-dom`'s `<Navigate />` and `useNavigate()`?
**Answer**: `useNavigate()` is a hook providing programmatic navigation (e.g. redirecting after login). `<Navigate />` is a component wrapper used in `ProtectedRoute.jsx` to declaratively redirect unauthenticated users.

#### Q19: How do you handle loading and error states in React data fetching?
**Answer**: By maintaining explicit `loading` (boolean) and `error` (string/null) state variables. Before fetching, `loading` is set to `true`. In `try...catch`, data is saved on success, or `error` is populated on failure, conditionally rendering `LoadingSpinner` or `ErrorMessage`.

#### Q20: What is component hydration?
**Answer**: Hydration is the process where client-side React attaches event listeners to HTML markup that was server-side rendered, turning static HTML into an interactive Single Page Application.

---

## Section C: Node.js, Express, MongoDB & Auth (10 Questions)

#### Q21: What is Express middleware and how does `next()` work?
**Answer**: Middleware functions are functions that have access to the request (`req`), response (`res`), and `next` function in the application HTTP cycle. Calling `next()` passes control to the next middleware in the pipeline.

#### Q22: How does JWT authentication work in ShopLane?
**Answer**: During login, the backend generates a signed JWT token containing user ID payload signed with `JWT_SECRET`. The client stores this token in `localStorage` and sends it in the `Authorization: Bearer <token>` header for protected API calls. `authMiddleware.js` verifies the token using `jwt.verify()`.

#### Q23: Why do we hash passwords using `bcryptjs` instead of storing plain text?
**Answer**: Storing plain text passwords is a severe security vulnerability. `bcryptjs` uses a cryptographic hashing algorithm with salting (random bits added before hashing) to make passwords resistant to rainbow table and brute-force attacks.

#### Q24: What is CORS and why did we configure it in `app.js`?
**Answer**: Cross-Origin Resource Sharing (CORS) is a browser security mechanism that restricts HTTP requests made from a different domain, port, or protocol. We enabled `cors()` in Express to allow requests from the React frontend running on `http://localhost:5173`.

#### Q25: Explain MongoDB indexing and why we created a text index on `Product`.
**Answer**: Indexes are specialized data structures that speed up query execution by avoiding full collection scans. We created a compound text index `{ name: 'text', description: 'text' }` on the `Product` schema to enable fast text searches.

#### Q26: What is Mongoose and what advantages does it provide over the raw MongoDB driver?
**Answer**: Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node.js. It provides schema validation, type casting, middleware hooks (`pre/post`), and query building.

#### Q27: How does pagination work in MongoDB with Mongoose?
**Answer**: Pagination uses `.skip((page - 1) * limit)` to skip preceding records and `.limit(limit)` to restrict the number of returned documents per page.

#### Q28: What is the purpose of `process.env` and `dotenv`?
**Answer**: `dotenv` loads configuration variables from a `.env` file into Node.js `process.env` object. This keeps sensitive credentials (database passwords, API keys, JWT secrets) out of source control.

#### Q29: How does centralized error middleware work in Express?
**Answer**: An Express error handler is defined with four arguments `(err, req, res, next)`. When an error is passed to `next(error)` from any route, Express bypasses remaining handlers and invokes this middleware, returning a structured JSON response `{ success: false, message }`.

#### Q30: What is the difference between SQL (relational) and NoSQL (document) databases?
**Answer**: SQL databases (PostgreSQL, MySQL) store data in fixed tables with rigid schemas and foreign key relationships. NoSQL databases (MongoDB) store data in flexible, JSON-like document structures, making them easy to scale and adapt to evolving application requirements.
