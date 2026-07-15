# 📚 Book Store — MERN Stack Project

A full-stack **Book Store Management System** built using the **MERN Stack** (MongoDB, Express.js, React.js, Node.js). The application allows users to browse, search, and purchase books, while administrators can manage inventory, orders, and users through a dedicated admin panel.

**Repository:** [book-store-smartbridge](https://github.com/sravaniyagavakota0001/book-store-smartbridge)

---

**DEMO:** [https://drive.google.com/file/d/1ZIyFXEjsNLY5g5g5JG6jIDqDeUcF1Bqz/view?pli=1]
---

## 📑 Table of Contents

1. [Project Architecture](#1-project-architecture)
2. [Technical Architecture](#2-technical-architecture)
3. [Entity Relation Diagram](#3-entity-relation-diagram)
4. [Features](#4-features)
5. [Roles and Responsibilities](#5-roles-and-responsibilities)
6. [User Flow](#6-user-flow)
7. [MVC Pattern, Project Setup and Configuration](#7-mvc-pattern-project-setup-and-configuration)
8. [Backend Development](#8-backend-development)
9. [Database Development](#9-database-development)
10. [Frontend Development](#10-frontend-development)
11. [Project Execution](#11-project-execution)

---

## 1. Project Architecture

The Book Store application follows a **three-tier client-server architecture**:

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT TIER                             │
│   React.js (SPA)                                                │
│   - Components, Pages, Routes                                   │
│   - Axios for API calls                                         │
│   - Context API / Redux for state management                   │
└───────────────────────────┬───────────────────────────────────┘
                             │ HTTP / REST API (JSON)
┌───────────────────────────▼───────────────────────────────────┐
│                       APPLICATION TIER                           │
│   Node.js + Express.js (Server)                                  │
│   - Routes → Controllers → Services                              │
│   - Middleware (Auth, Validation, Error Handling)                │
│   - JWT based authentication                                     │
└───────────────────────────┬───────────────────────────────────┘
                             │ Mongoose ODM
┌───────────────────────────▼───────────────────────────────────┐
│                         DATABASE TIER                            │
│   MongoDB (NoSQL Document Database)                               │
│   - Collections: Users, Books, Orders, Cart, Categories           │
└─────────────────────────────────────────────────────────────────┘
```

**Key architectural principles:**
- Separation of concerns between client, server, and database
- RESTful API communication between client and server
- Stateless authentication using JWT tokens
- Modular folder structure for scalability and maintainability

---

## 2. Technical Architecture

| Layer | Technology Used | Purpose |
|---|---|---|
| Frontend | React.js, React Router, Axios, Bootstrap / Tailwind CSS | UI rendering, routing, API consumption |
| Backend | Node.js, Express.js | REST API, business logic, request handling |
| Database | MongoDB, Mongoose | Data persistence and schema modeling |
| Authentication | JWT (JSON Web Token), bcrypt.js | Secure login, password hashing, role-based access |
| State Management | Context API / Redux | Global state (cart, user session) |
| Version Control | Git & GitHub | Source code management |
| API Testing | Postman | Endpoint testing during development |
| Deployment | Render / Vercel / Netlify / MongoDB Atlas | Hosting frontend, backend, and database |

**High-Level Technical Flow:**

```
Browser → React App → Axios (HTTP Request) → Express Router
        → Middleware (Auth/Validation) → Controller → Mongoose Model
        → MongoDB → Response (JSON) → React State Update → UI Render
```

---

## 3. Entity Relation Diagram

```
┌────────────────────┐          ┌────────────────────┐
│        User        │          │        Book        │
├────────────────────┤          ├────────────────────┤
│ _id                │          │ _id                │
│ name               │          │ title              │
│ email              │          │ author             │
│ password           │          │ description        │
│ role (user/admin)  │          │ price              │
│ createdAt          │          │ category           │
│ updatedAt          │          │ stock              │
└─────────┬──────────┘          │ imageUrl           │
          │ 1                  │ createdAt           │
          │                    │ updatedAt           │
          │                    └─────────┬───────────┘
          │ N                            │ N
┌─────────▼──────────┐          ┌────────▼───────────┐
│        Order        │◄────────┤     OrderItem       │
├────────────────────┤   N   1  ├────────────────────┤
│ _id                │          │ _id                │
│ userId (FK)        │          │ orderId (FK)        │
│ items[]            │          │ bookId (FK)          │
│ totalAmount        │          │ quantity             │
│ status              │          │ price               │
│ shippingAddress     │          └────────────────────┘
│ paymentStatus       │
│ createdAt           │
└────────────────────┘

┌────────────────────┐          ┌────────────────────┐
│        Cart         │          │      Category       │
├────────────────────┤          ├────────────────────┤
│ _id                │          │ _id                │
│ userId (FK)         │          │ name                │
│ items[]             │          │ description         │
│ (bookId, quantity)  │          └────────────────────┘
└────────────────────┘
```

**Relationships:**
- One **User** can place many **Orders** (1:N)
- One **User** has one **Cart** (1:1)
- One **Order** contains many **OrderItems** (1:N)
- One **Book** belongs to one **Category** (N:1)
- One **Book** can appear in many **OrderItems** and **Cart items** (N:N via join)

---

## 4. Features

### 👤 User Features
- User registration and login (JWT authentication)
- Browse all books with search and category filters
- View detailed book information (author, price, description, stock)
- Add / remove books from cart
- Update quantity of items in cart
- Place orders and view order history
- Update profile information
- Logout and session management

### 🛠️ Admin Features
- Admin login with role-based access control
- Add, update, and delete books
- Manage book categories
- Manage stock/inventory levels
- View and manage all customer orders
- Update order status (Pending, Shipped, Delivered, Cancelled)
- View and manage registered users
- Dashboard with sales/order overview

### ⚙️ System Features
- RESTful API architecture
- Secure password hashing with bcrypt
- Token-based authentication with JWT
- Protected routes on both client and server
- Responsive UI across devices
- Centralized error handling middleware
- Input validation on client and server

---

## 5. Roles and Responsibilities

| Role | Responsibilities |
|---|---|
| **Guest (Unauthenticated User)** | Browse books, view details, search/filter catalog, must register/login to purchase |
| **Registered User (Customer)** | Everything a guest can do + manage cart, place orders, view order history, manage own profile |
| **Admin** | Full CRUD on books and categories, manage all orders and update statuses, manage users, access admin dashboard |
| **System/Server** | Validates requests, authenticates and authorizes users, persists data, sends responses, handles errors |

**Access Control Matrix:**

| Action | Guest | User | Admin |
|---|:---:|:---:|:---:|
| View books | ✅ | ✅ | ✅ |
| Search/filter books | ✅ | ✅ | ✅ |
| Add to cart | ❌ | ✅ | ✅ |
| Place order | ❌ | ✅ | ✅ |
| Add/Edit/Delete book | ❌ | ❌ | ✅ |
| Manage all orders | ❌ | ❌ | ✅ |
| Manage users | ❌ | ❌ | ✅ |

---

## 6. User Flow

```
┌──────────┐     ┌──────────────┐     ┌─────────────────┐     ┌──────────────┐
│  Landing  │ ──► │ Register /    │ ──► │  Browse Books /   │ ──► │  Book Details  │
│  Page     │     │ Login         │     │  Search & Filter  │     │  Page          │
└──────────┘     └──────────────┘     └─────────────────┘     └───────┬──────┘
                                                                        │
                                                                        ▼
┌──────────────┐     ┌─────────────────┐     ┌──────────────┐     ┌──────────────┐
│  Order        │ ◄── │  Checkout /      │ ◄── │  View Cart    │ ◄── │  Add to Cart  │
│  Confirmation │     │  Place Order     │     │               │     │               │
└──────┬───────┘     └─────────────────┘     └──────────────┘     └──────────────┘
       │
       ▼
┌──────────────┐
│  Order         │
│  History /     │
│  Track Status  │
└──────────────┘
```

**Admin User Flow:**

```
Admin Login ──► Admin Dashboard ──► Manage Books (Add/Edit/Delete)
                       │
                       ├──► Manage Categories
                       ├──► Manage Orders (Update Status)
                       └──► Manage Users
```

---

## 7. MVC Pattern, Project Setup and Configuration

### MVC Pattern in this Project

| MVC Component | Responsibility | Location |
|---|---|---|
| **Model** | Defines schema/structure of data using Mongoose | `server/models/` |
| **View** | React components rendering UI (acts as the "View" in this decoupled architecture) | `client/src/` |
| **Controller** | Handles business logic, processes requests, returns responses | `server/controllers/` |

Request lifecycle: `Route → Middleware → Controller → Model → Database → Response`

### Creating Project Folder

```bash
mkdir book-store-smartbridge
cd book-store-smartbridge
mkdir client server
git init
```

### Client Setup (Installing React App)

```bash
cd client
npx create-react-app .
npm install axios react-router-dom bootstrap react-bootstrap
npm install @reduxjs/toolkit react-redux   # if using Redux
npm start
```

Folder created:
```
client/
├── public/
├── src/
├── package.json
└── ...
```

### Server Setup (npm init)

```bash
cd ../server
npm init -y
npm install express mongoose cors dotenv bcryptjs jsonwebtoken
npm install --save-dev nodemon
```

Add to `server/package.json`:
```json
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js"
}
```

Create `.env` file in `server/`:
```
PORT=5000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret_key
```

---

## 8. Backend Development

### Backend Structure

```
server/
├── config/
│   └── db.js                # MongoDB connection setup
├── models/
│   ├── User.js
│   ├── Book.js
│   ├── Order.js
│   ├── Cart.js
│   └── Category.js
├── controllers/
│   ├── authController.js
│   ├── bookController.js
│   ├── orderController.js
│   ├── cartController.js
│   └── userController.js
├── routes/
│   ├── authRoutes.js
│   ├── bookRoutes.js
│   ├── orderRoutes.js
│   ├── cartRoutes.js
│   └── userRoutes.js
├── middleware/
│   ├── authMiddleware.js     # JWT verification
│   ├── adminMiddleware.js    # Role-based access
│   └── errorHandler.js
├── .env
├── index.js                  # Entry point
└── package.json
```

### Development and Explanation

**1. Entry Point — `index.js`**
```javascript
const express = require('express');
const cors = require('cors');
const dotenv = require('dotenv');
const connectDB = require('./config/db');

dotenv.config();
connectDB();

const app = express();
app.use(cors());
app.use(express.json());

app.use('/api/auth', require('./routes/authRoutes'));
app.use('/api/books', require('./routes/bookRoutes'));
app.use('/api/orders', require('./routes/orderRoutes'));
app.use('/api/cart', require('./routes/cartRoutes'));
app.use('/api/users', require('./routes/userRoutes'));

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

**2. Authentication Middleware — `authMiddleware.js`**
```javascript
const jwt = require('jsonwebtoken');

const protect = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ message: 'Not authorized' });

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (err) {
    res.status(401).json({ message: 'Invalid token' });
  }
};

module.exports = { protect };
```

**3. Sample Controller — `bookController.js`**
```javascript
const Book = require('../models/Book');

exports.getAllBooks = async (req, res) => {
  try {
    const books = await Book.find();
    res.status(200).json(books);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
};

exports.createBook = async (req, res) => {
  try {
    const book = new Book(req.body);
    await book.save();
    res.status(201).json(book);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};
```

**4. Sample Routes — `bookRoutes.js`**
```javascript
const express = require('express');
const router = express.Router();
const { getAllBooks, createBook } = require('../controllers/bookController');
const { protect } = require('../middleware/authMiddleware');
const { isAdmin } = require('../middleware/adminMiddleware');

router.get('/', getAllBooks);
router.post('/', protect, isAdmin, createBook);

module.exports = router;
```

### REST API Endpoints

| Method | Endpoint | Description | Access |
|---|---|---|---|
| POST | `/api/auth/register` | Register a new user | Public |
| POST | `/api/auth/login` | Login and receive JWT | Public |
| GET | `/api/books` | Get all books | Public |
| GET | `/api/books/:id` | Get book by ID | Public |
| POST | `/api/books` | Add a new book | Admin |
| PUT | `/api/books/:id` | Update a book | Admin |
| DELETE | `/api/books/:id` | Delete a book | Admin |
| GET | `/api/cart` | Get user's cart | User |
| POST | `/api/cart` | Add item to cart | User |
| POST | `/api/orders` | Place an order | User |
| GET | `/api/orders` | Get all orders | Admin |
| GET | `/api/orders/my-orders` | Get logged-in user's orders | User |
| PUT | `/api/orders/:id` | Update order status | Admin |

---

## 9. Database Development

### Configure MongoDB

- **Local:** Install MongoDB Community Server and run `mongod`
- **Cloud (recommended):** Create a free cluster on [MongoDB Atlas](https://www.mongodb.com/atlas), whitelist IP, and get the connection string.

### Create Database Connection

`server/config/db.js`
```javascript
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    console.log('MongoDB Connected');
  } catch (err) {
    console.error(err.message);
    process.exit(1);
  }
};

module.exports = connectDB;
```

### Create Schema and Models

**User Model — `models/User.js`**
```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  role: { type: String, enum: ['user', 'admin'], default: 'user' },
}, { timestamps: true });

module.exports = mongoose.model('User', userSchema);
```

**Book Model — `models/Book.js`**
```javascript
const mongoose = require('mongoose');

const bookSchema = new mongoose.Schema({
  title: { type: String, required: true },
  author: { type: String, required: true },
  description: { type: String },
  price: { type: Number, required: true },
  category: { type: mongoose.Schema.Types.ObjectId, ref: 'Category' },
  stock: { type: Number, default: 0 },
  imageUrl: { type: String },
}, { timestamps: true });

module.exports = mongoose.model('Book', bookSchema);
```

**Order Model — `models/Order.js`**
```javascript
const mongoose = require('mongoose');

const orderSchema = new mongoose.Schema({
  userId: { type: mongoose.Schema.Types.ObjectId, ref: 'User', required: true },
  items: [{
    bookId: { type: mongoose.Schema.Types.ObjectId, ref: 'Book' },
    quantity: Number,
    price: Number,
  }],
  totalAmount: { type: Number, required: true },
  status: { type: String, enum: ['Pending', 'Shipped', 'Delivered', 'Cancelled'], default: 'Pending' },
  shippingAddress: { type: String },
  paymentStatus: { type: String, enum: ['Pending', 'Paid'], default: 'Pending' },
}, { timestamps: true });

module.exports = mongoose.model('Order', orderSchema);
```

**Cart Model — `models/Cart.js`**
```javascript
const mongoose = require('mongoose');

const cartSchema = new mongoose.Schema({
  userId: { type: mongoose.Schema.Types.ObjectId, ref: 'User', required: true },
  items: [{
    bookId: { type: mongoose.Schema.Types.ObjectId, ref: 'Book' },
    quantity: { type: Number, default: 1 },
  }],
}, { timestamps: true });

module.exports = mongoose.model('Cart', cartSchema);
```

**Category Model — `models/Category.js`**
```javascript
const mongoose = require('mongoose');

const categorySchema = new mongoose.Schema({
  name: { type: String, required: true, unique: true },
  description: { type: String },
});

module.exports = mongoose.model('Category', categorySchema);
```

---

## 10. Frontend Development

### Frontend Structure

```
client/
├── public/
├── src/
│   ├── components/
│   │   ├── Navbar.jsx
│   │   ├── Footer.jsx
│   │   ├── BookCard.jsx
│   │   ├── CartItem.jsx
│   │   └── PrivateRoute.jsx
│   ├── pages/
│   │   ├── Home.jsx
│   │   ├── Login.jsx
│   │   ├── Register.jsx
│   │   ├── BookDetails.jsx
│   │   ├── Cart.jsx
│   │   ├── Checkout.jsx
│   │   ├── OrderHistory.jsx
│   │   └── admin/
│   │       ├── Dashboard.jsx
│   │       ├── ManageBooks.jsx
│   │       └── ManageOrders.jsx
│   ├── context/
│   │   └── AuthContext.jsx
│   ├── services/
│   │   └── api.js            # Axios instance
│   ├── App.js
│   └── index.js
└── package.json
```

### Development and Explanation

**1. Axios Instance — `services/api.js`**
```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:5000/api',
});

api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

export default api;
```

**2. App Routing — `App.js`**
```jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import Login from './pages/Login';
import Register from './pages/Register';
import BookDetails from './pages/BookDetails';
import Cart from './pages/Cart';
import Checkout from './pages/Checkout';
import OrderHistory from './pages/OrderHistory';
import PrivateRoute from './components/PrivateRoute';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
        <Route path="/books/:id" element={<BookDetails />} />
        <Route path="/cart" element={<PrivateRoute><Cart /></PrivateRoute>} />
        <Route path="/checkout" element={<PrivateRoute><Checkout /></PrivateRoute>} />
        <Route path="/orders" element={<PrivateRoute><OrderHistory /></PrivateRoute>} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

**3. Sample Component — `BookCard.jsx`**
```jsx
function BookCard({ book }) {
  return (
    <div className="card book-card">
      <img src={book.imageUrl} alt={book.title} />
      <h5>{book.title}</h5>
      <p>{book.author}</p>
      <p>₹{book.price}</p>
      <a href={`/books/${book._id}`} className="btn btn-primary">View Details</a>
    </div>
  );
}

export default BookCard;
```

**4. Auth Context — `context/AuthContext.jsx`**
```jsx
import { createContext, useState, useContext } from 'react';

const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [user, setUser] = useState(JSON.parse(localStorage.getItem('user')) || null);

  const login = (userData, token) => {
    localStorage.setItem('token', token);
    localStorage.setItem('user', JSON.stringify(userData));
    setUser(userData);
  };

  const logout = () => {
    localStorage.clear();
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuth = () => useContext(AuthContext);
```

---

## 11. Project Execution

### Steps for Execution

**1. Clone the repository**
```bash
git clone https://github.com/sravaniyagavakota0001/book-store-smartbridge.git
cd book-store-smartbridge
```

**2. Setup and run the backend**
```bash
cd server
npm install
# create a .env file with MONGO_URI, JWT_SECRET, PORT
npm run dev
```
Server runs at: `http://localhost:5000`

**3. Setup and run the frontend**
```bash
cd ../client
npm install
npm start
```
Client runs at: `http://localhost:3000`

**4. Verify the application**
- Register a new user account
- Login and browse the book catalog
- Add books to cart and place an order
- Login as admin to manage books, categories, and orders

### Demo Screenshots

> Add screenshots of the application here once available, e.g.:
```
📷 Home Page              → screenshots/home.png
📷 Login Page             → screenshots/login.png
📷 Book Listing           → screenshots/books.png
📷 Book Details Page      → screenshots/book-details.png
📷 Cart Page              → screenshots/cart.png
📷 Checkout Page          → screenshots/checkout.png
📷 Admin Dashboard        → screenshots/admin-dashboard.png
📷 Manage Books (Admin)   → screenshots/manage-books.png
```
```markdown
![Home Page](screenshots/home.png)
![Book Details](screenshots/book-details.png)
![Admin Dashboard](screenshots/admin-dashboard.png)
```

### Drive Link

> 🔗 **Project Demo Video / Drive Link:** [Add your Google Drive link here]

---

## 🧰 Tech Stack Summary

`MongoDB` `Express.js` `React.js` `Node.js` `JWT` `Mongoose` `Bootstrap`

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes (`git commit -m 'Add some feature'`)
4. Push to the branch (`git push origin feature/your-feature`)
5. Open a Pull Request

## 📄 License

This project is developed for educational purposes as part of the SmartBridge program.

---
