### Module 3: Firestore Database

#### Lesson 3.1: Introduction to Firestore

In this lesson, we will introduce Firestore, a flexible, scalable NoSQL database provided by Firebase. We will cover its core concepts, advantages, and how to integrate Firestore with a React TypeScript frontend and a Python Flask backend. By the end of this lesson, you will have a solid understanding of Firestore and how to use it effectively in your applications.

##### 1. What is Firestore?

Firestore is a cloud-hosted NoSQL database that allows you to store and sync data for client- and server-side development. It is part of the Firebase platform and is designed for building real-time applications that can scale effortlessly.

**Key Features of Firestore:**

- **Real-time Synchronization**: Data is automatically synchronized across all connected clients in real-time, making it ideal for collaborative applications.
- **Offline Capabilities**: Firestore supports offline data persistence, allowing users to access and modify data even when they are not connected to the internet.
- **Rich Querying**: Firestore provides powerful querying capabilities, enabling you to retrieve data based on various criteria.
- **Scalability**: Firestore can handle large amounts of data and high traffic without requiring complex scaling strategies.
- **Security Rules**: Firestore allows you to define security rules to control access to your data, ensuring that users only have access to the data they are authorized to view or modify.

##### 2. Firestore Data Model

Firestore organizes data into collections and documents:

- **Collections**: A collection is a group of documents. Each collection can contain multiple documents, and collections can be nested within other collections.
- **Documents**: A document is a set of key-value pairs. Each document is identified by a unique ID within its collection and can contain various data types, including strings, numbers, booleans, arrays, and even nested objects.

**Example Structure:**

```
users (collection)
  ├── userId1 (document)
  │     ├── name: "John Doe"
  │     ├── email: "john@example.com"
  │     └── age: 30
  ├── userId2 (document)
  │     ├── name: "Jane Smith"
  │     ├── email: "jane@example.com"
  │     └── age: 25
```

In this example, `users` is a collection containing two documents, each representing a user with fields for name, email, and age.

##### 3. Setting Up Firestore

To use Firestore in your application, you need to set it up in the Firebase Console:

1. **Go to the Firebase Console**: Navigate to your Firebase project.
2. **Select Firestore Database**: In the left sidebar, click on **Firestore Database**.
3. **Create Database**: Click on the **Create Database** button. You will be prompted to choose between starting in test mode (which allows read/write access to all users) or production mode (which requires setting up security rules). For development purposes, you can start in test mode.
4. **Choose Location**: Select the location for your Firestore database and click **Done**.

##### 4. Integrating Firestore with React TypeScript Frontend

Now that you have set up Firestore, let’s integrate it into your React TypeScript frontend.

**Step 1: Install Firestore SDK**

If you haven’t already, ensure that you have the Firebase SDK installed in your React project. You can do this by running:

```bash
npm install firebase
```

**Step 2: Configure Firestore in Your Application**

In your existing `firebaseConfig.ts` file, import and initialize Firestore:

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

**Step 3: Create Firestore Functions**

Next, create functions to interact with Firestore. For example, you can create functions to add, retrieve, update, and delete documents in a Firestore collection. Create a new file named `firestore.ts` in the `src` directory.

```typescript
// src/firestore.ts
import { db } from "./firebaseConfig";
import {
  collection,
  addDoc,
  getDocs,
  updateDoc,
  deleteDoc,
  doc,
} from "firebase/firestore";

// Function to add a new user
export const addUser = async (user: {
  name: string;
  email: string;
  age: number;
}) => {
  try {
    const docRef = await addDoc(collection(db, "users"), user);
    console.log("User added with ID:", docRef.id);
  } catch (error) {
    console.error("Error adding user:", error);
  }
};

// Function to retrieve all users
export const getUsers = async () => {
  const usersCollection = collection(db, "users");
  const userSnapshot = await getDocs(usersCollection);
  const userList = userSnapshot.docs.map((doc) => ({
    id: doc.id,
    ...doc.data(),
  }));
  return userList;
};

// Function to update a user
export const updateUser = async (
  userId: string,
  updatedData: Partial<{ name: string; email: string; age: number }>
) => {
  const userDoc = doc(db, "users", userId);
  await updateDoc(userDoc, updatedData);
  console.log("User updated:", userId);
};

// Function to delete a user
export const deleteUser = async (userId: string) => {
  const userDoc = doc(db, "users", userId);
  await deleteDoc(userDoc);
  console.log("User deleted:", userId);
};
```

In this code, we define functions to add a new user, retrieve all users, update a user's information, and delete a user from the Firestore database.

##### 5. Integrating Firestore with Python Flask Backend

To integrate Firestore with your Python Flask backend, you will need to use the Firebase Admin SDK. This allows you to perform operations on Firestore from your Flask application.

**Step 1: Install Firebase Admin SDK**

If you haven’t already, install the Firebase Admin SDK in your Flask environment:

```bash
pip install firebase-admin
```

**Step 2: Initialize Firestore in Your Flask Application**

In your Flask application (e.g., `app.py`), initialize Firestore as follows:

```python
# app.py
import firebase_admin
from firebase_admin import credentials, firestore

# Initialize Firebase Admin SDK
cred = credentials.Certificate('path/to/serviceAccountKey.json')
firebase_admin.initialize_app(cred)

# Initialize Firestore
db = firestore.client()
```

**Step 3: Create Firestore Endpoints**

You can create endpoints in your Flask application to interact with Firestore. For example, you might create endpoints to add, retrieve, update, and delete users.

```python
@app.route('/users', methods=['POST'])
def add_user():
    data = request.get_json()
    user_ref = db.collection('users').add(data)
    return jsonify({'id': user_ref.id}), 201

@app.route('/users', methods=['GET'])
def get_users():
    users_ref = db.collection('users')
    users = users_ref.stream()
    user_list = [{**user.to_dict(), 'id': user.id} for user in users]
    return jsonify(user_list), 200

@app.route('/users/<user_id>', methods=['PUT'])
def update_user(user_id):
    data = request.get_json()
    db.collection('users').document(user_id).update(data)
    return jsonify({'id': user_id}), 200

@app.route('/users/<user_id>', methods=['DELETE'])
def delete_user(user_id):
    db.collection('users').document(user_id).delete()
    return jsonify({'id': user_id}), 204
```

In this code, we define endpoints to add a new user, retrieve all users, update a user's information, and delete a user from Firestore.

##### Conclusion

In this lesson, we introduced Firestore, a powerful NoSQL database provided by Firebase. We covered its core concepts, advantages, and how to set it up in both a React TypeScript frontend and a Python Flask backend. With Firestore, you can build real-time applications that scale effortlessly while managing your data effectively.

[Next: lesson-3-2-crud-operations](./lesson-3-2-crud-operations.md)
