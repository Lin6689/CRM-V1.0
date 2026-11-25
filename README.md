# MiniCRM — Billing App (v1.0)

A full-stack billing/CRM web app with user authentication, customer management, product catalog with image upload, and invoice generation with automatic tax and stock deduction.

## Tech Stack

- **Frontend:** React, Vite, React Router, Tailwind CSS
- **Backend:** Node.js, Express
- **Database:** MongoDB (Mongoose)
- **Auth:** JWT + bcryptjs
- **Uploads:** Multer (local `/uploads` storage)

## Features

- User registration & login with JWT authentication
- Passwords hashed with bcryptjs (never stored in plain text)
- Protected routes — only logged-in users can access the app
- Customer CRUD (name, phone, email, address, balance)
- Duplicate phone number validation for customers
- Product CRUD with SKU, price, stock, and image upload
- Negative stock validation
- Invoice generation:
  - Auto line-total, subtotal, tax %, and grand total calculation (server-side)
  - Product stock auto-deducted on invoice creation
  - Invoice status, due date, and payment tracking
- Per-user data isolation — each user sees only their own customers, products, and invoices
- Print-friendly invoice view (`window.print()`)

## Project Structure

```
├── backend/
│   ├── app.js               # Express app, middleware, static uploads
│   ├── bin/www              # Server entry point
│   ├── models/              # User, Customer, Product, Invoice (Mongoose)
│   └── routes/              # auth, customers, products, invoices
└── frontend/
    └── src/
        ├── api.js           # API client (fetch wrapper, JWT header)
        ├── context/         # AuthContext (React Context for auth state)
        └── components/      # Auth, Layout, Customers, Products, Invoices
```

## Setup

### Backend

```bash
cd backend
npm install
# set MONGODB_URI and JWT_SECRET in .env
npm start              # runs on http://localhost:3000
```

### Frontend

```bash
cd frontend
npm install
npm run dev            # runs on http://localhost:5173
```

## Environment Variables (backend/.env)

| Variable | Description |
|---|---|
| `MONGODB_URI` | MongoDB Atlas connection string |
| `JWT_SECRET` | Secret for signing JWT tokens |

## How It Works (key concepts)

- **Auth:** login verifies password with `bcrypt.compare()` → issues signed JWT → stored in `localStorage` → sent as `Authorization: Bearer` header → verified by `authMiddleware` on every protected route.
- **Data isolation:** every record stores an `owner` field (user id); all queries filter by `req.user.id` so users only see their own data.
- **Invoice math:** computed server-side — price is read from the DB (never trusted from frontend), line totals → subtotal → tax% → grandTotal; stock deducted atomically.
