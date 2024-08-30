### Project 2.1: Building a User Login System (Frontend)

#### Overview

In this project, we will create a simple React application that integrates with Firebase Authentication to manage user registration and login functionalities. The frontend will communicate with the Flask backend we previously set up to register new users and authenticate existing users.

#### Prerequisites

Before you begin, ensure you have the following:

- Node.js installed (preferably version 14 or higher)
- A Firebase project set up with Authentication enabled (Email/Password method)
- The Flask backend running (as described in the previous document)

#### Step 1: Set Up the React Application

1. **Create a New React Project**:
   Open your terminal and run the following command to create a new React application with TypeScript:

   ```bash
   npx create-react-app user-login-system --template typescript
   ```

   Navigate into your project directory:

   ```bash
   cd user-login-system
   ```

2. **Install Required Packages**:
   Install the Firebase SDK to interact with Firebase services:

   ```bash
   npm install firebase axios
   ```

   - `firebase`: The Firebase SDK for authentication and other services.
   - `axios`: A promise-based HTTP client for making requests to the Flask backend.

#### Step 2: Configure Firebase

1. **Create a Firebase Configuration File**:
   In the `src` directory, create a new file named `firebaseConfig.ts`. This file will contain the Firebase configuration settings for your project.

   ```typescript
   // src/firebaseConfig.ts
   import { initializeApp } from "firebase/app";

   const firebaseConfig = {
     apiKey: "YOUR_API_KEY",
     authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
     projectId: "YOUR_PROJECT_ID",
     storageBucket: "YOUR_PROJECT_ID.appspot.com",
     messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
     appId: "YOUR_APP_ID",
   };

   const app = initializeApp(firebaseConfig);

   export default app;
   ```

   Replace the placeholder values with your actual Firebase project configuration settings, which you can find in the Firebase Console under **Project settings**.

#### Step 3: Create Authentication Functions

Next, we will create functions to handle user registration and login. Create a new file named `auth.ts` in the `src` directory.

```typescript
// src/auth.ts
import axios from "axios";

const API_URL = "http://127.0.0.1:5000"; // URL of your Flask backend

export const registerUser = async (email: string, password: string) => {
  const response = await axios.post(`${API_URL}/register`, {
    email,
    password,
  });
  return response.data;
};

export const loginUser = async (email: string, password: string) => {
  const response = await axios.post(`${API_URL}/login`, {
    email,
    password,
  });
  return response.data;
};

export const logoutUser = async () => {
  const response = await axios.post(`${API_URL}/logout`);
  return response.data;
};
```

In this code, we define three functions: `registerUser`, `loginUser`, and `logoutUser`. Each function sends a POST request to the corresponding endpoint on the Flask backend.

#### Step 4: Create the Authentication Component

Now, let's create a component that allows users to register and log in. Create a new file named `AuthForm.tsx` in the `src` directory.

```typescript
// src/AuthForm.tsx
import React, { useState } from "react";
import { registerUser, loginUser, logoutUser } from "./auth";

const AuthForm: React.FC = () => {
  const [email, setEmail] = useState<string>("");
  const [password, setPassword] = useState<string>("");
  const [isLogin, setIsLogin] = useState<boolean>(true);
  const [message, setMessage] = useState<string>("");

  const handleSubmit = async (event: React.FormEvent) => {
    event.preventDefault();
    setMessage("");

    try {
      if (isLogin) {
        const loginResponse = await loginUser(email, password);
        setMessage(`Login successful: ${loginResponse.uid}`);
      } else {
        const registerResponse = await registerUser(email, password);
        setMessage(`Registration successful: ${registerResponse.uid}`);
      }
      setEmail("");
      setPassword("");
    } catch (error) {
      setMessage(
        `Error: ${error.response?.data?.error || "An error occurred"}`
      );
    }
  };

  return (
    <div>
      <h2>{isLogin ? "Login" : "Register"}</h2>
      <form onSubmit={handleSubmit}>
        <input
          type="email"
          placeholder="Email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
        <input
          type="password"
          placeholder="Password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
        />
        <button type="submit">{isLogin ? "Login" : "Register"}</button>
      </form>
      <button onClick={() => setIsLogin((prev) => !prev)}>
        Switch to {isLogin ? "Register" : "Login"}
      </button>
      {message && <p>{message}</p>}
      <button onClick={logoutUser}>Logout</button>
    </div>
  );
};

export default AuthForm;
```

In this component:

- We manage the state for email, password, and whether the user is logging in or registering.
- The `handleSubmit` function calls the appropriate authentication function based on the current state and displays success or error messages.
- Users can switch between login and registration modes using a button.

#### Step 5: Integrate the Authentication Component

Finally, integrate the `AuthForm` component into your main application file (e.g., `App.tsx`).

```typescript
// src/App.tsx
import React from "react";
import AuthForm from "./AuthForm";

const App: React.FC = () => {
  return (
    <div>
      <h1>User Login System</h1>
      <AuthForm />
    </div>
  );
};

export default App;
```

#### Step 6: Run the React Application

1. **Start the Application**:
   In your terminal, run the following command to start the React application:

   ```bash
   npm start
   ```

   This will start the development server, and you can access your application at `http://localhost:3000`.

2. **Test the Functionality**:
   - Use the registration form to create a new user.
   - Log in with the registered user credentials.
   - Test the logout functionality.

#### Conclusion

In this document, we have implemented the frontend for a user login system using React with TypeScript and Firebase Authentication. The frontend communicates with the Flask backend to handle user registration, login, and logout functionalities.

This setup provides a solid foundation for building secure applications that leverage Firebase for user management. You can further enhance this application by adding features such as password recovery, email verification, and user profile management.
