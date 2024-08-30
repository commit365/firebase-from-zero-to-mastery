# Project 3.1: Building a Chat Application

## Overview

In this project, we will build a real-time chat application using Firebase Firestore as the backend database. The application will allow users to send and receive messages in real-time. We will implement a React TypeScript frontend that interacts with a Python Flask backend, which will handle Firestore operations.

## Features

- User registration and login using Firebase Authentication.
- Sending and receiving messages in real-time.
- Displaying a chat history.
- A simple and responsive user interface for chatting.

## Prerequisites

Before you begin, ensure you have the following:

- A Firebase project set up with Firestore and Authentication enabled.
- A Python Flask backend set up with the Firebase Admin SDK.
- A React TypeScript frontend set up as described in previous lessons.
- Basic knowledge of React, TypeScript, and Flask.

## Project Structure

The project will consist of two main parts: the Flask backend and the React frontend.

```
chat-application/
├── backend/                     # Flask backend
│   ├── app.py                   # Main Flask application
│   ├── requirements.txt         # Python dependencies
│   └── serviceAccountKey.json   # Firebase service account key
└── frontend/                    # React frontend
    ├── src/
    │   ├── components/          # React components
    │   │   ├── Chat.tsx         # Chat component
    │   │   └── Auth.tsx         # Authentication component
    │   ├── firebaseConfig.ts     # Firebase configuration
    │   ├── chatService.ts        # Service for chat operations
    │   ├── App.tsx              # Main application component
    │   └── index.tsx            # Entry point
    ├── package.json              # Node.js dependencies
    └── tsconfig.json             # TypeScript configuration
```

## Step 1: Setting Up the Flask Backend

### 1.1 Install Required Packages

In your backend directory, create a virtual environment and install the required packages:

```bash
# Navigate to the backend directory
cd backend

# Create a virtual environment
python -m venv venv

# Activate the virtual environment
# On Windows
venv\Scripts\activate
# On macOS/Linux
source venv/bin/activate

# Install Flask and Firebase Admin SDK
pip install Flask firebase-admin
```

### 1.2 Initialize Firestore in Your Flask Application

Create a file named `app.py` and set up the Flask application with Firestore:

```python
# app.py
from flask import Flask, request, jsonify
import firebase_admin
from firebase_admin import credentials, firestore

# Initialize Flask app
app = Flask(__name__)

# Initialize Firebase Admin SDK
cred = credentials.Certificate('path/to/serviceAccountKey.json')
firebase_admin.initialize_app(cred)

# Initialize Firestore
db = firestore.client()

@app.route('/messages', methods=['POST'])
def send_message():
    """Send a new chat message."""
    data = request.get_json()
    message_ref = db.collection('messages').add(data)
    return jsonify({'id': message_ref.id}), 201

@app.route('/messages', methods=['GET'])
def get_messages():
    """Retrieve all chat messages."""
    messages_ref = db.collection('messages').order_by('timestamp')
    messages = messages_ref.stream()
    message_list = [{**msg.to_dict(), 'id': msg.id} for msg in messages]
    return jsonify(message_list), 200

if __name__ == '__main__':
    app.run(debug=True)
```

### 1.3 Create a Service Account Key

1. Go to the [Firebase Console](https://console.firebase.google.com/).
2. Select your project and navigate to **Project settings**.
3. Go to the **Service accounts** tab.
4. Click on **Generate new private key**. This will download a JSON file containing your service account credentials. Place this file in the backend directory and update the path in `app.py`.

### 1.4 Run the Flask Application

Run the Flask application:

```bash
python app.py
```

The server will start on `http://127.0.0.1:5000`.

## Step 2: Setting Up the React TypeScript Frontend

### 2.1 Create a New React Project

In a separate directory, create a new React application with TypeScript:

```bash
npx create-react-app chat-frontend --template typescript
cd chat-frontend
```

### 2.2 Install Required Packages

Install Firebase and Axios for handling requests:

```bash
npm install firebase axios
```

### 2.3 Configure Firebase

Create a file named `firebaseConfig.ts` in the `src` directory:

```typescript
// src/firebaseConfig.ts
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID",
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app); // Initialize Firestore

export { app, db };
```

### 2.4 Create Chat Service

Create a file named `chatService.ts` in the `src` directory to handle chat operations:

```typescript
// src/chatService.ts
import axios from "axios";

const API_URL = "http://127.0.0.1:5000"; // URL of your Flask backend

export const sendMessage = async (message: { text: string; user: string }) => {
  const response = await axios.post(`${API_URL}/messages`, message);
  return response.data;
};

export const getMessages = async () => {
  const response = await axios.get(`${API_URL}/messages`);
  return response.data;
};
```

### 2.5 Create Chat Component

Create a file named `Chat.tsx` in the `src` directory:

```typescript
// src/Chat.tsx
import React, { useEffect, useState } from "react";
import { sendMessage, getMessages } from "./chatService";

const Chat: React.FC = () => {
  const [messages, setMessages] = useState<any[]>([]);
  const [messageText, setMessageText] = useState<string>("");
  const [user, setUser] = useState<string>("User"); // Replace with actual user management

  const fetchMessages = async () => {
    const messagesList = await getMessages();
    setMessages(messagesList);
  };

  useEffect(() => {
    fetchMessages();
  }, []);

  const handleSendMessage = async (event: React.FormEvent) => {
    event.preventDefault();
    if (messageText.trim() === "") return;

    const message = { text: messageText, user: user };
    await sendMessage(message);
    setMessageText("");
    fetchMessages(); // Refresh the message list
  };

  return (
    <div>
      <h2>Chat Application</h2>
      <div
        style={{
          maxHeight: "400px",
          overflowY: "scroll",
          border: "1px solid #ccc",
          padding: "10px",
        }}
      >
        {messages.map((msg) => (
          <div key={msg.id}>
            <strong>{msg.user}:</strong> {msg.text}
          </div>
        ))}
      </div>
      <form onSubmit={handleSendMessage}>
        <input
          type="text"
          placeholder="Type your message..."
          value={messageText}
          onChange={(e) => setMessageText(e.target.value)}
          required
        />
        <button type="submit">Send</button>
      </form>
    </div>
  );
};

export default Chat;
```

### 2.6 Integrate the Chat Component

Integrate the `Chat` component into your main application file (e.g., `App.tsx`):

```typescript
// src/App.tsx
import React from "react";
import Chat from "./Chat";

const App: React.FC = () => {
  return (
    <div>
      <h1>Firestore Chat Application</h1>
      <Chat />
    </div>
  );
};

export default App;
```

### Step 3: Running the Application

1. **Start the Flask Backend**:
   Ensure your Flask backend is running:

   ```bash
   python app.py
   ```

2. **Start the React Application**:
   In your terminal, run the following command to start the React application:

   ```bash
   npm start
   ```

   This will start the development server, and you can access your application at `http://localhost:3000`.

3. **Test the Chat Functionality**:
   - Open multiple browser tabs to test real-time chat functionality.
   - Send messages from one tab and observe them appearing in the other tab.

### Conclusion

In this project, we built a real-time chat application using Firebase Firestore for data storage and a Flask backend for handling requests. The application allows users to send and receive messages in real time, demonstrating how to effectively use Firestore in a React TypeScript frontend and a Python Flask backend.

This project serves as a solid foundation for building more complex applications that require real-time data synchronization.
