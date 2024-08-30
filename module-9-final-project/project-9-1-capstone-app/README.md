# Project 9.1: Capstone Project

## Overview

The **Capstone Project** is the final project for this course, where you will apply the skills and knowledge you have gained throughout the modules. This project involves building a full-stack web application using a React TypeScript frontend and a Python Flask backend, integrated with Firebase services. The goal of this project is to create a functional, user-friendly application that demonstrates your ability to implement best practices, security measures, and advanced Firebase features.

## Features

The Capstone Project will include the following features:

- **User Authentication**: Implement user authentication using Firebase Authentication, allowing users to sign up, log in, and manage their accounts.
- **Data Management**: Use Firestore as the database to store and manage user-generated content, such as posts, comments, or profiles.
- **Real-Time Updates**: Leverage Firestore's real-time capabilities to update the UI automatically when data changes.
- **Push Notifications**: Integrate Firebase Cloud Messaging (FCM) to send push notifications to users for important updates or alerts.
- **Performance Monitoring**: Use Firebase Performance Monitoring to track the performance of your application and identify areas for improvement.
- **Responsive Design**: Ensure the application is responsive and provides a seamless experience across different devices.

## Project Structure

The project consists of two main parts: the React TypeScript frontend and the Python Flask backend.

```
capstone-project/
├── backend/                     # Flask backend
│   ├── app.py                   # Main Flask application
│   ├── requirements.txt         # Python dependencies
│   └── serviceAccountKey.json   # Firebase service account key
└── frontend/                    # React TypeScript frontend
    ├── src/
    │   ├── components/          # React components
    │   │   └── ExampleComponent.tsx # Example component
    │   ├── firebaseConfig.ts      # Firebase configuration
    │   ├── App.tsx                # Main application component
    │   └── index.tsx              # Entry point
    ├── public/                   # Public assets
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

@app.route('/api/data', methods=['GET', 'POST'])
def handle_data():
    if request.method == 'POST':
        data = request.get_json()
        # Save data to Firestore
        db.collection('your_collection').add(data)
        return jsonify({'status': 'Data added'}), 201
    else:
        # Retrieve data from Firestore
        docs = db.collection('your_collection').stream()
        data = [{doc.id: doc.to_dict()} for doc in docs]
        return jsonify(data), 200

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
npx create-react-app capstone-frontend --template typescript
cd capstone-frontend
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
import { getMessaging } from "firebase/messaging";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID",
  measurementId: "YOUR_MEASUREMENT_ID",
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app); // Initialize Firestore
const messaging = getMessaging(app); // Initialize Firebase Cloud Messaging

export { app, db, messaging };
```

### 2.4 Create Components for Your Application

You can create components to handle user interactions, display data, and manage notifications. For example, you might create a `DataComponent.tsx` to display data from Firestore and a `NotificationComponent.tsx` to handle notifications.

### 2.5 Integrate Components in the Main Application

Integrate your components into the main application file (e.g., `App.tsx`):

```typescript
// src/App.tsx
import React from "react";
import DataComponent from "./components/DataComponent";
import NotificationComponent from "./components/NotificationComponent";

const App: React.FC = () => {
  return (
    <div>
      <h1>Capstone Project</h1>
      <NotificationComponent />
      <DataComponent />
    </div>
  );
};

export default App;
```

## Step 3: Running the Application

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

3. **Test the Application**:
   - Use the application to add and retrieve data from Firestore.
   - Test push notifications if implemented.

## Conclusion

In this Capstone Project, you have built a full-stack web application using a React TypeScript frontend and a Python Flask backend, integrated with Firebase services. You have learned how to set up Firebase Authentication, Firestore, and Cloud Messaging, while also implementing best practices for security and user privacy.

This project serves as a comprehensive demonstration of your skills and knowledge gained throughout the course. As you continue to develop your application, consider exploring additional Firebase services and features to enhance its functionality.
