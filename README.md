# Camp Feedback API Documentation

## Overview
This is a comprehensive camp feedback management system with authentication, role management, and feedback collection features.

## Environment Setup
1. Create a `.env` file in the root directory with the following variables:
```
MONGODB_URI=mongodb://localhost:27017/camp_feedback_db
JWT_SECRET=your_super_secure_jwt_secret_key_here
PORT=3000
NODE_ENV=development
```

2. Install dependencies: `npm install`
3. Start server: `npm run dev`

## Base URL
```
http://localhost:3000/api/v1
```

## Authentication
All protected routes require a Bearer token in the Authorization header:
```
Authorization: Bearer <your_jwt_token>
```

---

## 🔐 Authentication Endpoints

### 1. Register User
**POST** `/auth/register`

**Body:**
```json
{
    "fullName": "John Doe",
    "email": "john@example.com",
    "password": "password123",
    "role": "User"
}
```

**Response (201):**
```json
{
    "status": 201,
    "data": {
        "id": "user_id",
        "fullName": "John Doe",
        "email": "john@example.com",
        "role": "User"
    },
    "message": "User created successfully"
}
```

### 2. Login User
**POST** `/auth/login`

**Body:**
```json
{
    "email": "john@example.com",
    "password": "password123"
}
```

**Response (200):**
```json
{
    "status": 200,
    "data": {
        "user": {
            "id": "user_id",
            "fullName": "John Doe",
            "email": "john@example.com",
            "role": "User"
        },
        "token": "jwt_token_here"
    },
    "message": "Login successful"
}
```

### 3. Get User Profile
**GET** `/profile`
- **Auth Required:** Yes

**Response (200):**
```json
{
    "status": 200,
    "data": {
        "id": "user_id",
        "fullName": "John Doe",
        "email": "john@example.com",
        "role": "User",
        "isActive": true,
        "createdAt": "2024-01-01T00:00:00.000Z",
        "updatedAt": "2024-01-01T00:00:00.000Z"
    },
    "message": "User profile retrieved successfully"
}
```

### 4. Get All Users (Admin Only)
**GET** `/users`
- **Auth Required:** Yes (Admin only)

**Response (200):**
```json
{
    "status": 200,
    "data": {
        "users": [...],
        "total": 10
    },
    "message": "Users retrieved successfully"
}
```

---

## 👥 Role Management Endpoints

### 1. Create Role (Admin Only)
**POST** `/roles`
- **Auth Required:** Yes (Admin only)

**Body:**
```json
{
    "name": "Moderator",
    "description": "Camp moderator with limited admin access",
    "permissions": ["read", "write", "manage_feedback"]
}
```

### 2. Get All Roles
**GET** `/roles`
- **Auth Required:** Yes

### 3. Get Role by ID
**GET** `/roles/:id`
- **Auth Required:** Yes

### 4. Update Role (Admin Only)
**PUT** `/roles/:id`
- **Auth Required:** Yes (Admin only)

### 5. Delete Role (Admin Only)
**DELETE** `/roles/:id`
- **Auth Required:** Yes (Admin only)

### 6. Assign Role to User (Admin Only)
**POST** `/roles/assign`
- **Auth Required:** Yes (Admin only)

**Body:**
```json
{
    "userId": "user_id_here",
    "roleName": "Admin"
}
```

---

## 💬 Feedback Endpoints

### 1. Create Feedback
**POST** `/feedback`
- **Auth Required:** Yes

**Body:**
```json
{
    "message": "Great camp experience! Learned a lot about coding.",
    "suggestion": "More hands-on workshops would be helpful.",
    "rating": 5,
    "campName": "Summer Coding Bootcamp 2024",
    "anonymous": false
}
```

**Response (201):**
```json
{
    "status": 201,
    "data": {
        "_id": "feedback_id",
        "message": "Great camp experience!",
        "suggestion": "More hands-on workshops would be helpful.",
        "rating": 5,
        "campName": "Summer Coding Bootcamp 2024",
        "anonymous": false,
        "userId": {...},
        "createdAt": "2024-01-01T00:00:00.000Z"
    },
    "message": "Feedback submitted successfully"
}
```

### 2. Get My Feedback
**GET** `/feedback/my-feedback?page=1&limit=10`
- **Auth Required:** Yes

### 3. Get All Feedback (Admin Only)
**GET** `/feedback/all?page=1&limit=10&campName=Summer`
- **Auth Required:** Yes (Admin only)

### 4. Get Feedback Statistics (Admin Only)
**GET** `/feedback/stats`
- **Auth Required:** Yes (Admin only)

**Response (200):**
```json
{
    "status": 200,
    "data": {
        "totalFeedback": 150,
        "averageRating": 4.2,
        "ratingDistribution": [
            {"_id": 1, "count": 5},
            {"_id": 2, "count": 10},
            {"_id": 3, "count": 25},
            {"_id": 4, "count": 60},
            {"_id": 5, "count": 50}
        ],
        "feedbackByCamp": [
            {"_id": "Summer Coding Bootcamp 2024", "count": 75, "avgRating": 4.5},
            {"_id": "Winter Tech Camp 2024", "count": 75, "avgRating": 3.9}
        ]
    },
    "message": "Feedback statistics retrieved successfully"
}
```

### 5. Get Feedback by ID
**GET** `/feedback/:id`
- **Auth Required:** Yes

### 6. Update Feedback
**PUT** `/feedback/:id`
- **Auth Required:** Yes (Own feedback only)

### 7. Delete Feedback
**DELETE** `/feedback/:id`
- **Auth Required:** Yes (Own feedback or Admin)

---

## 🩺 Health Check

### Health Check
**GET** `/health`

**Response (200):**
```json
{
    "status": 200,
    "message": "Camp Feedback API is running successfully",
    "timestamp": "2024-01-01T00:00:00.000Z"
}
```

---

## 🔒 Authorization Levels

- **Public:** Registration, Login, Health Check
- **Authenticated Users:** Profile, Create Feedback, View Own Feedback, Update Own Feedback
- **Admin Only:** User Management, Role Management, View All Feedback, Feedback Statistics

---

## 📝 Testing with Postman

### Step 1: Register a User
1. POST to `/auth/register` with user details
2. Note the response contains user info

### Step 2: Login
1. POST to `/auth/login` with email and password
2. Copy the `token` from the response

### Step 3: Set Authorization
1. In Postman, go to Authorization tab
2. Select "Bearer Token"
3. Paste the JWT token

### Step 4: Test Protected Routes
1. Try accessing `/profile` to verify authentication
2. Create feedback using `/feedback`
3. View your feedback with `/feedback/my-feedback`

### Step 5: Test Admin Features (Optional)
1. Register another user with role "Admin"
2. Login with admin credentials
3. Test admin-only endpoints like `/users` and `/feedback/stats`

---

## 🚨 Error Responses

All error responses follow this format:
```json
{
    "status": 400,
    "data": null,
    "message": "Error description here"
}
```

Common HTTP status codes:
- `400` - Bad Request
- `401` - Unauthorized (missing/invalid token)
- `403` - Forbidden (insufficient permissions)
- `404` - Not Found
- `500` - Internal Server Error