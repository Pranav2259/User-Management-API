# FastAPI User Authentication API

## Overview

This project is a simple user authentication system built with **FastAPI** and **PostgreSQL**. It provides endpoints for user registration, login (with JWT authentication), profile retrieval, and profile update. Passwords are securely hashed using bcrypt. The API is fully documented with automatic Swagger docs.

---

## Tech Stack

- **Backend Framework:** FastAPI
- **Database:** PostgreSQL
- **ORM:** SQLAlchemy
- **Authentication:** JWT (JSON Web Tokens)
- **Password Hashing:** bcrypt (via passlib)
- **Environment Variables:** python-dotenv
- **Validation:** Pydantic

---

## How to Run

### 1. Clone the Repository

```bash
git clone <your-repo-url>
cd python_API
```

### 2. Set Up the Environment

Create a virtual environment and activate it:

```bash
python -m venv .venv
# On Windows:
.venv\Scripts\activate
# On Unix/Mac:
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables

Edit the `.env` file with your PostgreSQL credentials and secret key:

```
DATABASE_URL=postgresql://<db_user>:<db_password>@localhost/<db_name>
SECRET_KEY=<your_secret_key>
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

## PostgreSQL Database Setup

Before running the API, you need to set up your PostgreSQL database and user table.

### 1. Create Database and User

1. **Login to PostgreSQL:**

   ```bash
   psql -U postgres
   ```

2. **Create a new database user (e.g., `auth_user`) and set a password:**

   ```sql
   CREATE USER auth_user WITH PASSWORD 'your_password';
   ```

3. **Create a new database (e.g., `auth_db`):**

   ```sql
   CREATE DATABASE auth_db OWNER auth_user;
   ```

4. **Grant all privileges on the database to your user:**
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE auth_db TO auth_user;
   ```

### 2. Create the `users` Table

Connect to your new database:

```bash
\c auth_db
```

Then run the following SQL to create the `users` table:

```sql
CREATE SEQUENCE users_user_id_seq;

CREATE TABLE users (
    user_id integer NOT NULL DEFAULT nextval('users_user_id_seq'::regclass),
    name character varying(100) NOT NULL,
    email character varying(255) NOT NULL,
    password text NOT NULL,
    CONSTRAINT users_pkey PRIMARY KEY (user_id),
    CONSTRAINT users_email_key UNIQUE (email)
);
```

### 3. Update Your `.env` File

Make sure your `.env` file matches your database setup:

```
DATABASE_URL=postgresql://auth_user:your_password@localhost/auth_db
SECRET_KEY=your_secret_key
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

---

**Table Details:**

- **Table name:** `users`
- **Primary key:** `user_id` (auto-incremented integer)
- **Columns:** `user_id`, `name`, `email` (unique), `password`

---

Now your database is ready for the FastAPI application!

### 6. Start the API Server

```bash
uvicorn main:app --reload
```

### 7. Access API Docs

Visit [http://localhost:8000/docs](http://localhost:8000/docs) for interactive Swagger documentation.

---

## API Endpoints

### **POST /register**

Register a new user.

- **Request Body (JSON):**
  ```json
  {
    "name": "John Doe",
    "email": "john@example.com",
    "password": "yourpassword"
  }
  ```
- **Response:** User data (without password).

---

### **POST /login**

Authenticate user and receive a JWT token.

- **Request Body:** `x-www-form-urlencoded`

  - `username`: user email
  - `password`: user password

- **Response:**
  ```json
  {
    "access_token": "<JWT_TOKEN>",
    "token_type": "bearer"
  }
  ```

---

### **GET /profile/{id}**

Get user profile by user ID.

- **Headers:**  
  `Authorization: Bearer <JWT_TOKEN>`
- **Response:** User data.

---

### **PUT /profile/{id}**

Update user profile.

- **Headers:**  
  `Authorization: Bearer <JWT_TOKEN>`
- **Request Body (JSON):**
  ```json
  {
    "name": "New Name",
    "email": "newemail@example.com",
    "password": "newpassword"
  }
  ```
- **Response:** Updated user data.

---

## Features

- **JWT Authentication:** Secure endpoints with token-based authentication.
- **Password Hashing:** User passwords are hashed using bcrypt before storage.
- **Input Validation:** All input is validated using Pydantic models.
- **Error Handling:** Returns appropriate HTTP status codes and error messages.
- **Automatic API Docs:** Swagger UI available at `/docs`.

---

## Notes

- Make sure PostgreSQL is running and accessible with the credentials provided in `.env`.
- The API will create tables automatically if they do not exist.
- Use the `/login` endpoint to obtain a JWT token for accessing protected routes.

---

## License

This project is for educational and demonstration purposes.
