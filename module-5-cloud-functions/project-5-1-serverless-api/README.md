# Project 5.1: Building a Serverless API

## Overview

The Serverless API project demonstrates how to build a scalable and efficient API using Firebase Cloud Functions. This project provides a backend solution for a React TypeScript frontend application, allowing it to perform various operations such as data retrieval, manipulation, and integration with other Firebase services. By leveraging Firebase Cloud Functions, this API can respond to HTTP requests and manage data seamlessly without the need for traditional server infrastructure.

## Features

- Create, Read, Update, and Delete (CRUD) operations for managing resources.
- Integration with Firebase Firestore for data storage.
- Secure access to the API using Firebase Authentication.
- Serverless architecture that automatically scales based on demand.
- Easy deployment and management through Firebase CLI.

## Prerequisites

Before you begin, ensure you have the following:

- A Firebase project set up with Cloud Functions and Firestore enabled.
- Python installed (preferably Python 3.7 or higher).
- Node.js installed (preferably version 14 or higher).
- Basic knowledge of React, TypeScript, Flask, and Firebase.

## Project Structure

The project consists of two main parts: the Flask backend (which is optional for specific use cases) and the Firebase Cloud Functions.

```
serverless-api/
├── backend/                     # Optional Flask backend
│   ├── app.py                   # Main Flask application
│   ├── requirements.txt         # Python dependencies
│   └── serviceAccountKey.json   # Firebase service account key
└── functions/                   # Firebase Cloud Functions
    ├── src/
    │   ├── index.ts             # Cloud Functions entry point
    │   └── utils.ts             # Utility functions (if needed)
    ├── package.json              # Node.js dependencies
    └── tsconfig.json             # TypeScript configuration
```

## Step 1: Setting Up Firebase Cloud Functions

### 1.1 Install Firebase CLI

If you haven’t already, install the Firebase CLI globally using npm:

```bash
npm install -g firebase-tools
```

### 1.2 Initialize Cloud Functions

1. Open your terminal and navigate to your project directory.
2. Run the following command to initialize Cloud Functions:

   ```bash
   firebase init functions
   ```

3. Follow the prompts to set up your functions. Choose TypeScript as your preferred language and whether to use ESLint for code linting.

### 1.3 Set Up Your Functions Directory

After initialization, a `functions` directory will be created in your project folder. This directory will contain your Cloud Functions code.

## Step 2: Writing Cloud Functions

### 2.1 Create Your First HTTP Function

Open the `functions/src/index.ts` file and add the following code to create a simple HTTP function:

```typescript
// functions/src/index.ts
import * as functions from "firebase-functions";

// Create an HTTP function
export const helloWorld = functions.https.onRequest((request, response) => {
  response.send("Hello from Firebase Cloud Functions!");
});
```

### 2.2 Create CRUD Functions

Next, let's create functions to handle CRUD operations for a resource (e.g., "items"). Add the following code to your `index.ts` file:

```typescript
import * as admin from "firebase-admin";
import * as functions from "firebase-functions";

// Initialize Firebase Admin SDK
admin.initializeApp();

// Create a new item
export const createItem = functions.https.onRequest(
  async (request, response) => {
    const data = request.body;
    const docRef = await admin.firestore().collection("items").add(data);
    response.status(201).send({ id: docRef.id });
  }
);

// Read all items
export const getItems = functions.https.onRequest(async (request, response) => {
  const snapshot = await admin.firestore().collection("items").get();
  const items = snapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() }));
  response.status(200).send(items);
});

// Update an item
export const updateItem = functions.https.onRequest(
  async (request, response) => {
    const { id } = request.params;
    const data = request.body;
    await admin.firestore().collection("items").doc(id).update(data);
    response.status(204).send();
  }
);

// Delete an item
export const deleteItem = functions.https.onRequest(
  async (request, response) => {
    const { id } = request.params;
    await admin.firestore().collection("items").doc(id).delete();
    response.status(204).send();
  }
);
```

### 2.3 Deploy Your Functions

To deploy your functions to Firebase, run the following command in your terminal:

```bash
firebase deploy --only functions
```

After deployment, you will receive URLs for your HTTP functions.

## Step 3: Integrating with the React TypeScript Frontend

### 3.1 Create a New React Project

In a separate directory, create a new React application with TypeScript:

```bash
npx create-react-app photo-gallery-frontend --template typescript
cd photo-gallery-frontend
```

### 3.2 Install Required Packages

Install Axios for handling requests:

```bash
npm install axios
```

### 3.3 Create API Service

Create a file named `apiService.ts` in the `src` directory to handle API requests:

```typescript
// src/apiService.ts
import axios from "axios";

const API_URL = "https://<your-project-id>.cloudfunctions.net"; // URL of your deployed functions

export const createItem = async (item: any) => {
  const response = await axios.post(`${API_URL}/createItem`, item);
  return response.data;
};

export const getItems = async () => {
  const response = await axios.get(`${API_URL}/getItems`);
  return response.data;
};

export const updateItem = async (id: string, item: any) => {
  await axios.put(`${API_URL}/updateItem/${id}`, item);
};

export const deleteItem = async (id: string) => {
  await axios.delete(`${API_URL}/deleteItem/${id}`);
};
```

### 3.4 Create Item Management Component

Create a file named `ItemManagement.tsx` in the `src` directory to manage items:

```typescript
// src/ItemManagement.tsx
import React, { useEffect, useState } from "react";
import { createItem, getItems, updateItem, deleteItem } from "./apiService";

const ItemManagement: React.FC = () => {
  const [items, setItems] = useState<any[]>([]);
  const [itemName, setItemName] = useState<string>("");
  const [selectedItemId, setSelectedItemId] = useState<string | null>(null);

  const fetchItems = async () => {
    const itemsList = await getItems();
    setItems(itemsList);
  };

  useEffect(() => {
    fetchItems();
  }, []);

  const handleSubmit = async (event: React.FormEvent) => {
    event.preventDefault();
    if (selectedItemId) {
      await updateItem(selectedItemId, { name: itemName });
    } else {
      await createItem({ name: itemName });
    }
    setItemName("");
    setSelectedItemId(null);
    fetchItems(); // Refresh the item list
  };

  const handleEdit = (item: any) => {
    setSelectedItemId(item.id);
    setItemName(item.name);
  };

  const handleDelete = async (id: string) => {
    await deleteItem(id);
    fetchItems(); // Refresh the item list
  };

  return (
    <div>
      <h2>Item Management</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          placeholder="Item Name"
          value={itemName}
          onChange={(e) => setItemName(e.target.value)}
          required
        />
        <button type="submit">
          {selectedItemId ? "Update Item" : "Add Item"}
        </button>
      </form>
      <ul>
        {items.map((item) => (
          <li key={item.id}>
            {item.name}
            <button onClick={() => handleEdit(item)}>Edit</button>
            <button onClick={() => handleDelete(item.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default ItemManagement;
```

### 3.5 Integrate Item Management Component

Integrate the `ItemManagement` component into your main application file (e.g., `App.tsx`):

```typescript
// src/App.tsx
import React from "react";
import ItemManagement from "./ItemManagement";

const App: React.FC = () => {
  return (
    <div>
      <h1>Serverless API Example</h1>
      <ItemManagement />
    </div>
  );
};

export default App;
```

## Step 4: Running the Application

1. **Start the Flask Backend** (if applicable):
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

3. **Test the API Functionality**:
   - Use the item management form to add, update, and delete items.
   - Verify that the changes are reflected in the list of items.

### Conclusion

In this project, we built a Serverless API using Firebase Cloud Functions to handle CRUD operations. We implemented a React TypeScript frontend that interacts with the API, allowing users to manage resources efficiently. This project demonstrates how to effectively use Firebase Cloud Functions in a serverless architecture.
