# ShopLane 🛍️ - Full-Stack E-Commerce Web Application

![React](https://img.shields.io/badge/React-18.2-61DAFB?logo=react)
![Node.js](https://img.shields.io/badge/Node.js-20.x-339933?logo=node.js)
![Express](https://img.shields.io/badge/Express-4.19-000000?logo=express)
![MongoDB](https://img.shields.io/badge/MongoDB-Atlas-47A248?logo=mongodb)
![Vite](https://img.shields.io/badge/Vite-5.2-646CFF?logo=vite)
![Tests](https://img.shields.io/badge/Tests-12%20Passing-brightgreen)

**ShopLane** is a production-ready, full-stack e-commerce web application engineered for campus placement preparation at **Arshith Group**. Built with a clean, modular architecture, the codebase emphasizes clarity, strict coding standards, accessible UI design, and zero unnecessary external dependencies.

---

## 🌟 Live Demo & Repository Links
- **GitHub Repository**: [https://github.com/rajeshmadiki/shopLane---e-commers-.git](https://github.com/rajeshmadiki/shopLane---e-commers-.git)
- **Live Frontend Application (Vercel)**: `https://shoplane-client.vercel.app` *(Deployable link)*
- **Live REST API Backend (Render)**: `https://shoplane-api.onrender.com` *(Deployable link)*

---

## 🛠️ Tech Stack & Tooling

| Domain | Technology | Purpose & Details |
| :--- | :--- | :--- |
| **Frontend** | React 18 + Vite | Modular UI component rendering & ultra-fast development server |
| **Routing** | React Router DOM v6 | Single Page Application (SPA) client-side navigation |
| **State Management** | Context API + `useReducer` | Global authentication state & synchronized shopping cart reducer |
| **Styling** | Plain CSS3 | CSS Grid, Flexbox, media queries, `:focus-visible` accessibility |
| **Backend** | Node.js + Express | RESTful API server with modular routes and middleware |
| **Database** | MongoDB Atlas + Mongoose | Cloud NoSQL database with structured schemas & text search index |
| **Authentication** | JWT + `bcryptjs` | JSON Web Tokens & salted password hashing |
| **Testing** | Jest + Supertest (Backend)<br>Vitest + RTL (Frontend) | 12 automated unit and integration test suites |
| **Deployment** | Render (Server)<br>Vercel (Client) | Automated cloud hosting & environment configuration |

---

## 📋 Features Overview

- 🔍 **Product Catalog & Filtering**: Browse 30+ realistic products across 5 categories (*Electronics, Clothing, Home & Kitchen, Books, Fitness*).
- ⚡ **Debounced Search**: 300ms debounced text search querying name and description without overloading the server.
- 🔀 **Sorting & Pagination**: Sort by *Newest*, *Price: Low to High*, *Price: High to Low*, and *Highest Rated*, with paginated page navigation.
- 🔐 **User Authentication**: Secure user registration and login with encrypted password hashing (`bcryptjs`) and JWT session persistence.
- 🛒 **Shopping Cart (`useReducer`)**: Synchronized cart state supporting item additions, quantity updates, item deletions, and subtotal calculations.
- 📦 **Order Placement & History**: Checkout mechanism converting cart items into structured orders stored in MongoDB, with order status tracking.
- ♿ **Responsive & Accessible**: Mobile/Tablet/Desktop support, semantic HTML tags (`<header>`, `<nav>`, `<main>`, `<article>`, `<footer>`), `aria-label` tags, and visible focus rings.
- 🧪 **Comprehensive Automated Testing**: 12 automated test cases covering endpoints, authentication guards, component renders, and state reducers.

---

## 🚀 REST API Endpoint Table

| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/health` | Public | System status and API health check |
| `GET` | `/api/products` | Public | List products (supports `?search=`, `?category=`, `?sort=`, `?page=`, `?limit=`) |
| `GET` | `/api/products/:id` | Public | Get detailed product by ID |
| `GET` | `/api/categories` | Public | Fetch distinct category names |
| `POST` | `/api/auth/register` | Public | Register new user account & return JWT |
| `POST` | `/api/auth/login` | Public | Authenticate user & return JWT token |
| `GET` | `/api/cart` | Protected | Fetch user's synchronized cart items |
| `POST` | `/api/cart` | Protected | Add product to cart or increment quantity |
| `PATCH` | `/api/cart/:productId` | Protected | Update item quantity in cart |
| `DELETE` | `/api/cart/:productId` | Protected | Remove item from cart |
| `POST` | `/api/orders` | Protected | Checkout cart items and create new order |
| `GET` | `/api/orders` | Protected | Get authenticated user's order history |

---

## 💻 Local Running & Development Steps

### Prerequisites
- Node.js (v18+ or v20+)
- Git

### 1. Clone Repository & Setup Environment
```bash
git clone https://github.com/rajeshmadiki/shopLane---e-commers-.git
cd shoplane
```

### 2. Configure Environment Files
- Copy `server/.env.example` to `server/.env`:
  ```env
  PORT=5000
  NODE_ENV=development
  MONGODB_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/shoplane?retryWrites=true&w=majority
  JWT_SECRET=your_super_secret_jwt_key_here
  CLIENT_URL=http://localhost:5173
  ```
- Copy `client/.env.example` to `client/.env`:
  ```env
  VITE_API_URL=http://localhost:5000/api
  ```

### 3. Run Backend REST API Server
```bash
cd server
npm install
npm run seed     # Seeds 30 realistic products into MongoDB Atlas
npm start        # Launches server on http://localhost:5000
```

### 4. Run Frontend Client
```bash
cd ../client
npm install
npm run dev      # Launches Vite dev server on http://localhost:5173
```

### 5. Execute Automated Tests
```bash
# Backend Jest Tests (Server)
cd server
npm test

# Frontend Vitest Tests (Client)
cd client
npm test
```

---

## 🌐 Click-by-Click Cloud Deployment Steps

### Backend Deployment on Render (Node.js REST API)
1. Log in to [Render Dashboard](https://dashboard.render.com/).
2. Click **New +** -> Select **Web Service**.
3. Connect your GitHub repository `rajeshmadiki/shopLane---e-commers-`.
4. Set the **Root Directory** to `server`.
5. Set the **Build Command** to `npm install`.
6. Set the **Start Command** to `node server.js`.
7. In the **Environment Variables** section, add:
   - `PORT` = `5000`
   - `NODE_ENV` = `production`
   - `MONGODB_URI` = *(Your MongoDB Atlas connection string)*
   - `JWT_SECRET` = *(Your production JWT secret key)*
   - `CLIENT_URL` = `https://shoplane-client.vercel.app`
8. Click **Create Web Service**. Once deployed, copy your live backend URL (e.g. `https://shoplane-api.onrender.com`).

### Frontend Deployment on Vercel (React + Vite)
1. Log in to [Vercel Dashboard](https://vercel.com/).
2. Click **Add New...** -> Select **Project**.
3. Import your GitHub repository `rajeshmadiki/shopLane---e-commers-`.
4. Set the **Root Directory** to `client`.
5. Framework Preset will automatically detect **Vite**.
6. In **Environment Variables**, add:
   - `VITE_API_URL` = `https://shoplane-api.onrender.com/api`
7. Click **Deploy**. Vercel will build and deploy your live site (e.g. `https://shoplane-client.vercel.app`).

---

## 🔮 Future Improvements & Scalability Roadmap
1. **Payment Gateway Integration**: Integrating Razorpay or Stripe webhooks for live payment processing.
2. **Admin Dashboard**: Adding protected admin routes for real-time inventory management and order status updates.
3. **Redis Caching Layer**: Caching product query results and category listings in Redis to reduce database read latency.


