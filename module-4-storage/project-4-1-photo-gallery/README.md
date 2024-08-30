# Project 4.1: Photo Gallery Application

## Overview

The Photo Gallery Application is a web application that allows users to upload, view, and manage images using Firebase Storage. Built with a React TypeScript frontend and a Python Flask backend, this application demonstrates how to leverage Firebase Storage for storing and serving user-generated content. Users can easily upload photos, which are then stored in the cloud and made accessible for viewing in a gallery format.

## Features

- User authentication using Firebase Authentication.
- Uploading images to Firebase Storage.
- Displaying uploaded images in a gallery format.
- Deleting images from Firebase Storage.
- Responsive design for a seamless user experience.

## Prerequisites

Before you begin, ensure you have the following:

- A Firebase project set up with Firebase Storage and Authentication enabled.
- Python installed (preferably Python 3.7 or higher).
- Node.js installed (preferably version 14 or higher).
- Basic knowledge of React, TypeScript, and Flask.

## Project Structure

The project consists of two main parts: the Flask backend and the React frontend.

```
photo-gallery/
├── backend/                     # Flask backend
│   ├── app.py                   # Main Flask application
│   ├── requirements.txt         # Python dependencies
│   └── serviceAccountKey.json   # Firebase service account key
└── frontend/                    # React frontend
    ├── src/
    │   ├── components/          # React components
    │   │   ├── PhotoUpload.tsx   # Photo upload component
    │   │   └── PhotoGallery.tsx   # Photo gallery component
    │   ├── firebaseConfig.ts      # Firebase configuration
    │   ├── storageService.ts       # Service for storage operations
    │   ├── App.tsx                # Main application component
    │   └── index.tsx              # Entry point
    ├── package.json               # Node.js dependencies
    └── tsconfig.json              # TypeScript configuration
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

Create a file named `app.py` and set up the Flask application with Firebase Storage:

```python
# app.py
from flask import Flask, request, jsonify
import firebase_admin
from firebase_admin import credentials, storage

# Initialize Flask app
app = Flask(__name__)

# Initialize Firebase Admin SDK
cred = credentials.Certificate('path/to/serviceAccountKey.json')
firebase_admin.initialize_app(cred, {
    'storageBucket': 'YOUR_PROJECT_ID.appspot.com'
})

@app.route('/upload', methods=['POST'])
def upload_file():
    """Upload a file to Firebase Storage."""
    if 'file' not in request.files:
        return jsonify({'error': 'No file part'}), 400

    file = request.files['file']
    if file.filename == '':
        return jsonify({'error': 'No selected file'}), 400

    bucket = storage.bucket()
    blob = bucket.blob(file.filename)
    blob.upload_from_file(file)
    return jsonify({'message': 'File uploaded successfully', 'url': blob.public_url}), 201

@app.route('/files', methods=['GET'])
def list_files():
    """List all files in Firebase Storage."""
    bucket = storage.bucket()
    blobs = bucket.list_blobs()
    file_list = [{'name': blob.name, 'url': blob.public_url} for blob in blobs]
    return jsonify(file_list), 200

@app.route('/delete/<filename>', methods=['DELETE'])
def delete_file(filename):
    """Delete a file from Firebase Storage."""
    bucket = storage.bucket()
    blob = bucket.blob(filename)
    blob.delete()
    return jsonify({'message': 'File deleted successfully'}), 204

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
npx create-react-app photo-gallery-frontend --template typescript
cd photo-gallery-frontend
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
import { getStorage } from "firebase/storage";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID",
};

const app = initializeApp(firebaseConfig);
const storage = getStorage(app); // Initialize Firebase Storage

export { app, storage };
```

### 2.4 Create Storage Service

Create a file named `storageService.ts` in the `src` directory to handle file uploads and retrieval:

```typescript
// src/storageService.ts
import axios from "axios";

const API_URL = "http://127.0.0.1:5000"; // URL of your Flask backend

export const uploadFile = async (file: File) => {
  const formData = new FormData();
  formData.append("file", file);

  const response = await axios.post(`${API_URL}/upload`, formData, {
    headers: {
      "Content-Type": "multipart/form-data",
    },
  });
  return response.data; // Return the response from the backend
};

export const getFiles = async () => {
  const response = await axios.get(`${API_URL}/files`);
  return response.data; // Return the list of files
};

export const deleteFile = async (filename: string) => {
  await axios.delete(`${API_URL}/delete/${filename}`);
};
```

### 2.5 Create Photo Upload Component

Create a file named `PhotoUpload.tsx` in the `src/components` directory to manage photo uploads:

```typescript
// src/components/PhotoUpload.tsx
import React, { useState } from "react";
import { uploadFile } from "../storageService";

const PhotoUpload: React.FC = () => {
  const [file, setFile] = useState<File | null>(null);
  const [message, setMessage] = useState<string>("");

  const handleFileChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    if (event.target.files) {
      setFile(event.target.files[0]);
    }
  };

  const handleUpload = async (event: React.FormEvent) => {
    event.preventDefault();
    if (file) {
      try {
        const response = await uploadFile(file);
        setMessage(`File uploaded successfully! URL: ${response.url}`);
        setFile(null); // Reset the file input
      } catch (error) {
        setMessage(`Error uploading file: ${error.message}`);
      }
    }
  };

  return (
    <div>
      <h2>Upload Photo</h2>
      <form onSubmit={handleUpload}>
        <input type="file" onChange={handleFileChange} required />
        <button type="submit">Upload</button>
      </form>
      {message && <p>{message}</p>}
    </div>
  );
};

export default PhotoUpload;
```

### 2.6 Create Photo Gallery Component

Create a file named `PhotoGallery.tsx` in the `src/components` directory to display uploaded photos:

```typescript
// src/components/PhotoGallery.tsx
import React, { useEffect, useState } from "react";
import { getFiles, deleteFile } from "../storageService";

const PhotoGallery: React.FC = () => {
  const [files, setFiles] = useState<any[]>([]);

  const fetchFiles = async () => {
    const filesList = await getFiles();
    setFiles(filesList);
  };

  useEffect(() => {
    fetchFiles();
  }, []);

  const handleDelete = async (filename: string) => {
    await deleteFile(filename);
    fetchFiles(); // Refresh the file list
  };

  return (
    <div>
      <h2>Photo Gallery</h2>
      <div>
        {files.map((file) => (
          <div key={file.name}>
            <img
              src={file.url}
              alt={file.name}
              style={{ width: "200px", height: "auto" }}
            />
            <button onClick={() => handleDelete(file.name)}>Delete</button>
          </div>
        ))}
      </div>
    </div>
  );
};

export default PhotoGallery;
```

### 2.7 Integrate Components in the Main Application

Integrate the `PhotoUpload` and `PhotoGallery` components into your main application file (e.g., `App.tsx`):

```typescript
// src/App.tsx
import React from "react";
import PhotoUpload from "./components/PhotoUpload";
import PhotoGallery from "./components/PhotoGallery";

const App: React.FC = () => {
  return (
    <div>
      <h1>Photo Gallery Application</h1>
      <PhotoUpload />
      <PhotoGallery />
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

3. **Test the Photo Upload and Gallery Functionality**:
   - Use the photo upload form to select and upload images.
   - Verify that the uploaded images appear in the gallery with the option to delete them.

### Conclusion

In this project, we built a Photo Gallery Application using Firebase Storage for storing and serving images. We implemented functionalities for uploading photos, displaying them in a gallery, and deleting them as needed. This project demonstrates how to effectively use Firebase Storage in a React TypeScript frontend and a Python Flask backend.
