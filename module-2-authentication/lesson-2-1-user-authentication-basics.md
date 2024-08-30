### Module 2: Firebase Authentication

#### Lesson 2.1: User Authentication Basics

In this lesson, we will explore how to implement Firebase Authentication in a React application. We will cover the process of setting up user authentication, including registration and login functionalities. Additionally, we will demonstrate how to use Firebase Authentication with TypeScript to ensure type safety and enhance the development experience.

##### 1. Implementing Firebase Authentication in a React Application

To get started with Firebase Authentication, follow these steps:

**Step 1: Set Up Firebase Authentication in the Firebase Console**

1. Go to the [Firebase Console](https://console.firebase.google.com/) and select your project.
2. In the left sidebar, navigate to **Authentication** and click on the **Get Started** button if you haven’t already set it up.
3. Under the **Sign-in method** tab, you will see various authentication providers. Enable the desired sign-in methods (e.g., Email/Password, Google, Facebook) by clicking the toggle switch next to each provider and clicking **Save**.

**Step 2: Install Required Packages**

If you haven’t already done so, ensure that you have the Firebase SDK installed in your React project. You can do this by running:

```bash
npm install firebase
```

**Step 3: Create Authentication Functions**

Next, we will create functions for user registration and login. In your `src` directory, create a new file named `auth.ts`. This file will contain our authentication logic.

```typescript
// src/auth.ts
import {
  getAuth,
  createUserWithEmailAndPassword,
  signInWithEmailAndPassword,
  signOut,
} from "firebase/auth";
import app from "./firebaseConfig";

const auth = getAuth(app);

export const registerUser = async (
  email: string,
  password: string
): Promise<void> => {
  try {
    await createUserWithEmailAndPassword(auth, email, password);
    console.log("User registered successfully");
  } catch (error) {
    console.error("Error registering user:", error);
  }
};

export const loginUser = async (
  email: string,
  password: string
): Promise<void> => {
  try {
    await signInWithEmailAndPassword(auth, email, password);
    console.log("User logged in successfully");
  } catch (error) {
    console.error("Error logging in:", error);
  }
};

export const logoutUser = async (): Promise<void> => {
  try {
    await signOut(auth);
    console.log("User logged out successfully");
  } catch (error) {
    console.error("Error logging out:", error);
  }
};
```

In this code, we import the necessary functions from the Firebase Authentication SDK and create three functions: `registerUser`, `loginUser`, and `logoutUser`. Each function handles the respective authentication process and logs success or error messages to the console.

**Step 4: Create a Simple Authentication Component**

Now, let's create a simple React component that allows users to register and log in. Create a new file named `AuthForm.tsx` in the `src` directory.

```typescript
// src/AuthForm.tsx
import React, { useState } from "react";
import { registerUser, loginUser, logoutUser } from "./auth";

const AuthForm: React.FC = () => {
  const [email, setEmail] = useState<string>("");
  const [password, setPassword] = useState<string>("");
  const [isLogin, setIsLogin] = useState<boolean>(true);

  const handleSubmit = async (event: React.FormEvent) => {
    event.preventDefault();
    if (isLogin) {
      await loginUser(email, password);
    } else {
      await registerUser(email, password);
    }
    setEmail("");
    setPassword("");
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
      <button onClick={logoutUser}>Logout</button>
    </div>
  );
};

export default AuthForm;
```

In this component, we manage the state for email, password, and whether the user is logging in or registering. The `handleSubmit` function calls the appropriate authentication function based on the current state. The form allows users to switch between login and registration modes.

**Step 5: Integrate the Authentication Component**

Finally, integrate the `AuthForm` component into your main application file (e.g., `App.tsx`).

```typescript
// src/App.tsx
import React from "react";
import AuthForm from "./AuthForm";

const App: React.FC = () => {
  return (
    <div>
      <h1>Firebase Authentication Example</h1>
      <AuthForm />
    </div>
  );
};

export default App;
```

Now, when you run your React application, you should see a simple authentication form that allows users to register, log in, and log out.

##### 2. Using Firebase Authentication with TypeScript

TypeScript provides static type checking, which helps catch errors during development. In the example above, we have already utilized TypeScript features such as type annotations for state variables and function parameters.

Here are some best practices for using Firebase Authentication with TypeScript:

- **Define Types for User Data**: Create interfaces to define the structure of user data. For example:

  ```typescript
  interface User {
    uid: string;
    email: string;
    displayName?: string;
  }
  ```

- **Use Type Assertions**: When working with Firebase user objects, you can use type assertions to ensure TypeScript understands the structure of the returned data:

  ```typescript
  import { User as FirebaseUser } from "firebase/auth";

  const user: FirebaseUser | null = auth.currentUser;
  if (user) {
    const userData: User = {
      uid: user.uid,
      email: user.email!,
      displayName: user.displayName || undefined,
    };
  }
  ```

- **Error Handling**: Use TypeScript’s union types to handle different error scenarios effectively. For example:

  ```typescript
  try {
    await loginUser(email, password);
  } catch (error) {
    if (error instanceof FirebaseError) {
      console.error("Firebase error:", error.message);
    } else {
      console.error("Unexpected error:", error);
    }
  }
  ```

By following these practices, you can ensure that your code remains type-safe and maintainable while leveraging the power of Firebase Authentication.

##### Conclusion

In this lesson, you learned how to implement Firebase Authentication in a React application, including user registration and login functionalities. You also explored how to use Firebase Authentication with TypeScript to enhance type safety and code quality.

[Next: lesson-2-2-advanced-authentication](./lesson-2-2-advanced-authentication.md)
