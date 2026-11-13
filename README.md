# Lab4 - Secure Connect Platform

A secure REST API with real-time chat built using Node.js, Express, MongoDB, JWT authentication, and Socket.IO.

## Features

- User registration and login with JWT authentication
- Password hashing using bcrypt
- Role-based access control (user / admin)
- Full CRUD for personal notes (each user sees only their own)
- Admin endpoint to view all notes
- Real-time chat rooms via Socket.IO (JWT-protected)
- Event-driven note lifecycle logging using Node.js EventEmitter

## Tech Stack

| Layer | Technology |
|---|---|
| Runtime | Node.js |
| Framework | Express v5 |
| Database | MongoDB (Mongoose) |
| Auth | JSON Web Tokens (JWT) |
| Password | bcrypt |
| Realtime | Socket.IO |
| Config | dotenv |

## Project Structure

```
├── middleware/
│   └── authMiddleware.js   # JWT auth + admin role guard
├── models/
│   ├── User.js             # User schema (username, email, password, role)
│   └── Note.js             # Note schema (title, content, owner)
├── routes/
│   ├── authRoutes.js       # POST /api/auth/signup, /login
│   └── noteRoutes.js       # CRUD /api/notes
├── server.js               # Entry point, Socket.IO, EventEmitter, DB connect
├── package.json
└── .env                    # Environment variables (not committed)
```

## Setup

**1. Clone and install dependencies**

```bash
npm install
```

**2. Create a `.env` file in the root:**

```env
PORT=5000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret_key
```

**3. Start the server**

```bash
# Production
npm start

# Development (with nodemon)
npm run dev
```

## API Endpoints

### Auth

| Method | Endpoint | Description | Auth Required |
|---|---|---|---|
| POST | `/api/auth/signup` | Register a new user | No |
| POST | `/api/auth/login` | Login and get JWT token | No |

#### Signup Request Body
```json
{
  "username": "john",
  "email": "john@example.com",
  "password": "secret123"
}
```

#### Login Request Body
```json
{
  "email": "john@example.com",
  "password": "secret123"
}
```

#### Login Response
```json
{
  "message": "Login successful",
  "token": "<jwt_token>"
}
```

---

### Notes

All note routes require the `Authorization: Bearer <token>` header.

| Method | Endpoint | Description | Role |
|---|---|---|---|
| POST | `/api/notes` | Create a note | User |
| GET | `/api/notes` | Get all your notes | User |
| GET | `/api/notes/:id` | Get a single note | User |
| PUT | `/api/notes/:id` | Update a note | User |
| DELETE | `/api/notes/:id` | Delete a note | User |
| GET | `/api/notes/admin/all` | Get all notes (all users) | Admin |

---

## Real-Time Chat (Socket.IO)

Connect with a valid JWT token:

```js
const socket = io("http://localhost:5000", {
  auth: { token: "<jwt_token>" }
});

// Join a room
socket.emit("joinRoom", "general");

// Send a message
socket.emit("chatMessage", "Hello everyone!");

// Listen for messages
socket.on("chatMessage", (data) => {
  console.log(`[${data.room}] ${data.username}: ${data.message}`);
});
```

## Environment Variables

| Variable | Description |
|---|---|
| `PORT` | Port the server runs on |
| `MONGO_URI` | MongoDB connection string |
| `JWT_SECRET` | Secret key for signing JWTs |
