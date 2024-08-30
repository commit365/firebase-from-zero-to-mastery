### Module 3: Firestore Database

#### Project 3.1: Building a Chat Application

In this project, we will create a real-time chat application using Firebase Firestore as the backend database. The application will allow users to send and receive messages in real time. We will implement a React TypeScript frontend that interacts with a Python Flask backend, which will handle Firestore operations.

### Project Overview

The chat application will have the following features:

- User registration and login using Firebase Authentication.
- Sending and receiving messages in real time.
- Displaying a chat history.
- A simple user interface for chatting.

### Prerequisites

Before you begin, ensure you have the following:

- A Firebase project set up with Firestore and Authentication enabled.
- A Python Flask backend set up with the Firebase Admin SDK.
- A React TypeScript frontend set up as described in previous lessons.

### Step 1: Setting Up the Flask Backend

#### 1.1 Install Required Packages

If you haven't already, ensure you have the required packages installed in your Flask environment:

```bash
pip install flask firebase-admin
```

#### 1.2 Initialize Firestore in Your Flask Application

In your existing `app.py` file, ensure you have the following setup:

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
```

#### 1.3 Create Firestore Endpoints for Chat Messages

Add the following endpoints to your Flask application to handle chat messages:

```python
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
```

In this code:

- The `/messages` endpoint with the `POST` method allows users to send new messages.
- The `/messages` endpoint with the `GET` method retrieves all messages from the Firestore collection, ordered by timestamp.

### Step 2: Setting Up the React TypeScript Frontend

#### 2.1 Install Required Packages

If you haven't already, ensure you have the required packages installed in your React project:

```bash
npm install firebase axios
```

#### 2.2 Configure Firebase in Your React Application

In your `firebaseConfig.ts` file, ensure Firestore is initialized:

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

#### 2.3 Create Chat Functions

Create a new file named `chatService.ts` in the `src` directory to handle chat operations:

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

In this code, we define functions to send a new message and retrieve all messages from the Flask backend.

#### 2.4 Create the Chat Component

Create a new component named `Chat.tsx` in the `src` directory to manage the chat interface:

```typescript
// src/Chat.tsx
import React, { useEffect, useState } from "react";
import { sendMessage, getMessages } from "./chatService";

const Chat: React.FC = () => {
  const [messages, setMessages] = useState<any[]>([]);
  const [messageText, setMessageText] = useState<string>("");
  const [user, setUser] = useState<string>("User"); // You can replace this with actual user management

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

In this component:

- We manage the state for messages, the message text, and the user.
- The `fetchMessages` function retrieves messages from the backend.
- The `handleSendMessage` function sends a new message to the backend and refreshes the message list.

#### 2.5 Integrate the Chat Component

Finally, integrate the `Chat` component into your main application file (e.g., `App.tsx`).

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

In this project, we built a real-time chat application using Firebase Firestore for data storage. We implemented the necessary CRUD operations to send and retrieve messages, allowing users to communicate in real time. This project demonstrates how to effectively use Firestore in a React TypeScript frontend and a Python Flask backend.

[Next: lesson-4-1-introduction-to-storage](../module-4-storage/lesson-4-1-introduction-to-storage.md)
