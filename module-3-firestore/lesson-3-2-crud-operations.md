### Module 3: Firestore Database

#### Lesson 3.2: CRUD Operations

In this lesson, we will explore how to perform Create, Read, Update, and Delete (CRUD) operations using Firestore in both a React TypeScript frontend and a Python Flask backend. Understanding these operations is essential for managing data effectively in your applications. By the end of this lesson, you will have hands-on experience with Firestore CRUD operations.

##### 1. Overview of CRUD Operations

CRUD operations are the basic functions of persistent storage. They allow you to manage data in your application effectively:

- **Create**: Add new documents to a collection.
- **Read**: Retrieve documents from a collection.
- **Update**: Modify existing documents in a collection.
- **Delete**: Remove documents from a collection.

Firestore provides simple APIs to perform these operations, making it easy to manage your data.

##### 2. Setting Up Firestore in Your Application

Before diving into CRUD operations, ensure that you have set up Firestore in your React and Flask applications as outlined in the previous lesson. You should have the Firestore SDK configured in your React frontend and the Firebase Admin SDK set up in your Flask backend.

### Part A: CRUD Operations in React TypeScript Frontend

#### Step 1: Create Firestore Functions

In your `firestore.ts` file, we will implement the CRUD functions for interacting with Firestore.

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
    return docRef.id; // Return the ID of the newly created document
  } catch (error) {
    console.error("Error adding user:", error);
    throw error; // Propagate the error for handling in the component
  }
};

// Function to retrieve all users
export const getUsers = async () => {
  try {
    const usersCollection = collection(db, "users");
    const userSnapshot = await getDocs(usersCollection);
    const userList = userSnapshot.docs.map((doc) => ({
      id: doc.id,
      ...doc.data(),
    }));
    return userList; // Return the list of users
  } catch (error) {
    console.error("Error retrieving users:", error);
    throw error; // Propagate the error for handling in the component
  }
};

// Function to update a user
export const updateUser = async (
  userId: string,
  updatedData: Partial<{ name: string; email: string; age: number }>
) => {
  try {
    const userDoc = doc(db, "users", userId);
    await updateDoc(userDoc, updatedData);
    console.log("User updated:", userId);
  } catch (error) {
    console.error("Error updating user:", error);
    throw error; // Propagate the error for handling in the component
  }
};

// Function to delete a user
export const deleteUser = async (userId: string) => {
  try {
    const userDoc = doc(db, "users", userId);
    await deleteDoc(userDoc);
    console.log("User deleted:", userId);
  } catch (error) {
    console.error("Error deleting user:", error);
    throw error; // Propagate the error for handling in the component
  }
};
```

In this code, we define functions to add a new user, retrieve all users, update a user's information, and delete a user from the Firestore database.

#### Step 2: Create a User Management Component

Create a new component named `UserManagement.tsx` in the `src` directory to manage users using the CRUD functions we just defined.

```typescript
// src/UserManagement.tsx
import React, { useEffect, useState } from "react";
import { addUser, getUsers, updateUser, deleteUser } from "./firestore";

const UserManagement: React.FC = () => {
  const [users, setUsers] = useState<any[]>([]);
  const [name, setName] = useState<string>("");
  const [email, setEmail] = useState<string>("");
  const [age, setAge] = useState<number | "">("");
  const [selectedUserId, setSelectedUserId] = useState<string | null>(null);

  const fetchUsers = async () => {
    const usersList = await getUsers();
    setUsers(usersList);
  };

  useEffect(() => {
    fetchUsers();
  }, []);

  const handleSubmit = async (event: React.FormEvent) => {
    event.preventDefault();
    const userData = { name, email, age: Number(age) };

    if (selectedUserId) {
      await updateUser(selectedUserId, userData);
      setSelectedUserId(null); // Reset the selected user
    } else {
      await addUser(userData);
    }

    setName("");
    setEmail("");
    setAge("");
    fetchUsers(); // Refresh the user list
  };

  const handleEdit = (user: any) => {
    setSelectedUserId(user.id);
    setName(user.name);
    setEmail(user.email);
    setAge(user.age);
  };

  const handleDelete = async (userId: string) => {
    await deleteUser(userId);
    fetchUsers(); // Refresh the user list
  };

  return (
    <div>
      <h2>User Management</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          placeholder="Name"
          value={name}
          onChange={(e) => setName(e.target.value)}
          required
        />
        <input
          type="email"
          placeholder="Email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
        <input
          type="number"
          placeholder="Age"
          value={age}
          onChange={(e) => setAge(e.target.value)}
          required
        />
        <button type="submit">
          {selectedUserId ? "Update User" : "Add User"}
        </button>
      </form>

      <h3>User List</h3>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            {user.name} ({user.email}, Age: {user.age})
            <button onClick={() => handleEdit(user)}>Edit</button>
            <button onClick={() => handleDelete(user.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default UserManagement;
```

In this component:

- We manage the state for users, user details (name, email, age), and the selected user for editing.
- The `fetchUsers` function retrieves the list of users from Firestore.
- The `handleSubmit` function adds a new user or updates an existing user based on whether a user is selected for editing.
- The `handleEdit` function populates the form with the selected user's data for editing.
- The `handleDelete` function deletes a user from Firestore.

#### Step 3: Integrate the User Management Component

Finally, integrate the `UserManagement` component into your main application file (e.g., `App.tsx`).

```typescript
// src/App.tsx
import React from "react";
import UserManagement from "./UserManagement";

const App: React.FC = () => {
  return (
    <div>
      <h1>Firestore User Management</h1>
      <UserManagement />
    </div>
  );
};

export default App;
```

### Part B: CRUD Operations in Python Flask Backend

Now we will implement the corresponding CRUD operations in your Flask backend to manage users in Firestore.

#### Step 1: Update Flask Application

In your existing `app.py` file, ensure you have the necessary imports and Firestore setup:

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

#### Step 2: Create CRUD Endpoints

Add the following endpoints to your Flask application to handle user management:

```python
@app.route('/users', methods=['POST'])
def add_user():
    """Add a new user."""
    data = request.get_json()
    user_ref = db.collection('users').add(data)
    return jsonify({'id': user_ref.id}), 201

@app.route('/users', methods=['GET'])
def get_users():
    """Retrieve all users."""
    users_ref = db.collection('users')
    users = users_ref.stream()
    user_list = [{**user.to_dict(), 'id': user.id} for user in users]
    return jsonify(user_list), 200

@app.route('/users/<user_id>', methods=['PUT'])
def update_user(user_id):
    """Update an existing user."""
    data = request.get_json()
    db.collection('users').document(user_id).update(data)
    return jsonify({'id': user_id}), 200

@app.route('/users/<user_id>', methods=['DELETE'])
def delete_user(user_id):
    """Delete a user."""
    db.collection('users').document(user_id).delete()
    return jsonify({'id': user_id}), 204
```

In this code:

- The `/users` endpoint with the `POST` method adds a new user to Firestore.
- The `/users` endpoint with the `GET` method retrieves all users from Firestore.
- The `/users/<user_id>` endpoint with the `PUT` method updates an existing user.
- The `/users/<user_id>` endpoint with the `DELETE` method removes a user from Firestore.

### Conclusion

In this lesson, we explored how to perform CRUD operations using Firestore in both a React TypeScript frontend and a Python Flask backend. We implemented functions to create, read, update, and delete user data, allowing you to manage your application's data effectively.

[Next: lesson-3-3-real-time-data-sync](./lesson-3-3-real-time-data-sync.md)
