# OLX Clone - Hackaboom 3.0

A full-stack marketplace application built in just 2 days as part of the Hackaboom 3.0 hackathon. This project demonstrates a functional OLX-like platform where users can create accounts, post announcements, browse listings, make deals, and communicate in real-time.

## Features

- **User Authentication**: Register and login with secure password hashing (bcrypt)
- **Create & Manage Announcements**: Post new marketplace announcements with photos
- **Feed & Discovery**: Browse all announcements in a dynamic feed
- **Live Map View**: Visualize announcements geographically on an interactive map
- **Favorites System**: Save favorite announcements for later
- **Deal Making**: Send and respond to deal requests between users
- **Real-time Messaging**: Chat with other users using Socket.io
- **User Profiles**: View user information and their posted announcements
- **Notifications**: Real-time updates on deal requests and messages

## Tech Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| **Backend** | Node.js + Express | 4.x |
| **Frontend** | Vanilla JavaScript + HTML + CSS | - |
| **Database** | MongoDB | Native Driver |
| **Real-time** | Socket.io | 4.x |
| **Authentication** | bcrypt, JWT | - |
| **File Uploads** | Multer / express-formidable | - |
| **Environment** | dotenv | - |
| **Dev Tools** | nodemon | - |

## Prerequisites

- **Node.js** (v14 or higher)
- **npm** (comes with Node.js)
- **MongoDB** (running locally on `localhost:27017`)

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd OLX_Clone
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Start MongoDB

Make sure MongoDB is running on your machine:

```bash
# On Windows
mongod

# On macOS/Linux
brew services start mongodb-community
# or
mongod
```
### 4. Configure Environment Variables

Create a `.env` file in the root directory (optional, defaults are provided):

```env
PORT=5000
MONGODB_URL=mongodb://localhost:27017/Hackaboom
```

### 5. Run the Server

```bash
# Development mode (with auto-reload via nodemon)
npm run dev

# or
npm start
```

The server will start on `http://localhost:5000`

### 6. Access the Application

Open your browser and navigate to:

```
http://localhost:5000
```

The frontend is served statically from the `/Frontend` folder.

## Frontend Pages Overview

| Page | File | Purpose |
|------|------|---------|
| **Landing Page** | `index.html` | Welcome page and entry point |
| **Sign Up** | `signup.html` | User registration form |
| **Login** | `login.html` | User authentication |
| **Main Feed** | `main.html` | Browse all announcements |
| **Add Announcement** | `addAnnounce.html` | Create a new listing |
| **View Announcement** | `announceView.html` | Detailed view of a single announcement |
| **My Account** | `myAccount.html` | User profile and their announcements |
| **Favorites** | `favorite.html` | Saved favorite announcements |
| **Conversations** | `conversations.html` | Messaging inbox |
| **Live Map** | `map.html` | Geographic view of announcements |
| **Notifications** | `notification.js` | Real-time notification handler |

## API Endpoints

### User Routes (`/user`)

#### Sign Up
- **POST** `/user/signup`
- **Body**:
  ```json
  {
    "username": "string",
    "email": "string",
    "password": "string",
    "location": {
      "city": "string",
      "lat": "number",
      "lng": "number"
    }
  }
  ```

#### Login
- **POST** `/user/login`
- **Body**:
  ```json
  {
    "email": "string",
    "password": "string"
  }
  ```
- **Response**: `{ email: "string" }`

#### Logout
- **POST** `/user/logout`
- **Body**: `{ email: "string" }`

#### Get User Profile
- **GET** `/user/myAccount?email=user@example.com`
- **Response**: User profile + their announcements

---

### Announcements Routes (`/announces`)

#### Create Announcement
- **POST** `/announces/create`
- **Content-Type**: `multipart/form-data`
- **Fields**:
  - `name` (string)
  - `description` (string)
  - `price` (number)
  - `userEmail` (string)
  - `location` (JSON array: `[city, lat, lng]`)
  - `photo` (file upload)

#### Delete Announcement
- **DELETE** `/announces/delete`
- **Body**: `{ announcementId: "string" }`

#### Get Feed (All Announcements)
- **GET** `/announces/feed`
- **Response**: Array of all announcements

#### Get Map Data
- **GET** `/announces/map`
- **Response**: Announcements with geo coordinates

#### Get User's Announcements
- **GET** `/announces/user?email=user@example.com`
- **Response**: Announcements posted by the user

#### Get User's Favorites
- **GET** `/announces/favorite?email=user@example.com`
- **Response**: User's favorited announcements

#### Add to Favorites
- **POST** `/announces/addFavorite`
- **Body**: `{ email: "string", name: "string" }`

#### Make a Deal Request
- **POST** `/announces/makedeal`
- **Body**:
  ```json
  {
    "from": "string (sender email)",
    "to": "string (receiver email)",
    "announcementId": "string"
  }
  ```

#### Check Deal Requests
- **GET** `/announces/checkdeals?email=user@example.com`
- **Response**: Pending deal requests for the user

#### Respond to Deal Request
- **PATCH** `/announces/dealresponse`
- **Body**: `{ dealId: "string", newStatus: "confirmed|refused" }`

---

### Messages Routes (`/messages`)

#### Send Message
- **POST** `/messages/send`
- **Body**:
  ```json
  {
    "senderEmail": "string",
    "receiverEmail": "string",
    "message": "string"
  }
  ```
- **Note**: Also emitted via Socket.io in real-time

#### Get Chat History
- **GET** `/messages/chat?senderEmail=user1@example.com&receiverEmail=user2@example.com`
- **Response**: Message history between two users

#### Get All Conversations
- **GET** `/messages/conversationsUsers?email=user@example.com`
- **Response**: List of all conversations for a user

#### Create Conversation
- **POST** `/messages/create/conversation`
- **Body**:
  ```json
  {
    "senderEmail": "string",
    "receiverEmail": "string"
  }
  ```

---

## Real-time Features

This application uses **Socket.io 4.x** for real-time communication:

- **Rooms**: Chat rooms are dynamically created and named based on email pairs:
  - Format: `[email1, email2].sort().join('-')`
  - Example: `alice@example.com-bob@example.com`

- **Events**:
  - **`joinRoom`**: Client joins a chat room
  - **`newMessage`**: Server broadcasts incoming messages to both participants

- **Flow**:
  1. User joins a conversation
  2. Client emits `joinRoom` with room identifier
  3. Messages are sent via Socket.io and HTTP
  4. Server broadcasts `newMessage` events to all participants in the room

## MongoDB Collections

| Collection | Purpose | Key Fields |
|-----------|---------|-----------|
| **users** | User accounts and profiles | email, username, password (hashed), location (city, lat, lng) |
| **announcements** | Posted marketplace listings | name, description, price, userEmail, location, photo, createdAt |
| **favorites** | User's favorite announcements | email, announcementIds |
| **messages** | Direct messages between users | senderEmail, receiverEmail, message, timestamp |
| **conversations** | Conversation metadata | participants (emails), lastMessage, updatedAt |
| **dealsSealed** | Deal requests between users | from, to, announcementId, status (pending/confirmed/refused) |

## Project Structure

```
OLX_Clone/
├── server.js                          # Main Express + Socket.io server
├── package.json                       # Project dependencies
├── .env                               # Environment variables (optional)
├── config/
│   └── db.js                          # MongoDB connection setup
├── controllers/
│   ├── userController.js              # User-related endpoints
│   ├── announcesController.js         # Announcement-related endpoints
│   └── messagesController.js          # Messaging-related endpoints
└── Frontend/
    ├── index.html                     # Landing page
    ├── login.html                     # Login form
    ├── signup.html                    # Registration form
    ├── main.html                      # Main feed
    ├── addAnnounce.html               # Create announcement
    ├── announceView.html              # Announcement details
    ├── myAccount.html                 # User profile
    ├── favorite.html                  # Favorites list
    ├── conversations.html             # Messaging inbox
    ├── map.html                       # Live map view
    └── notification.js                # Notification system
```

## Notes

- **Session Management**: Uses email-based session tracking via tokens
- **Photo Uploads**: Handled through multipart/form-data; photos are stored on the server
- **Geolocation**: Announcements include latitude/longitude for map visualization
- **Password Security**: All passwords are hashed using bcrypt before storage
- **Database**: MongoDB instance must be running before starting the server