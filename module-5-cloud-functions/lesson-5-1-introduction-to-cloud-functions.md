### Module 5: Firebase Cloud Functions

#### Lesson 5.1: Introduction to Cloud Functions

In this lesson, we will explore Firebase Cloud Functions, a serverless framework that allows you to run backend code in response to events triggered by Firebase features and HTTPS requests. Cloud Functions enable you to extend your application’s functionality without managing servers, providing a powerful way to create scalable and responsive applications. By the end of this lesson, you will understand the core concepts of Cloud Functions and how to integrate them with a React TypeScript frontend and a Python Flask backend.

##### 1. What are Firebase Cloud Functions?

Firebase Cloud Functions are event-driven functions that run in a managed environment. They allow you to execute backend code in response to various events, such as:

- Changes in Firestore or Realtime Database (e.g., document creation, updates, or deletions).
- User authentication events (e.g., user sign-ups or deletions).
- HTTP requests (e.g., RESTful APIs).
- Scheduled events (e.g., cron jobs).

**Key Features of Firebase Cloud Functions:**

- **Serverless Architecture**: Automatically scales based on demand, so you don’t need to manage infrastructure.
- **Event-Driven**: Respond to events from Firebase services and external sources.
- **Built-in Security**: Functions run in a secure environment, and you can control access using Firebase Authentication.
- **Integration with Other Firebase Services**: Seamlessly integrates with Firestore, Firebase Authentication, Firebase Storage, and more.

##### 2. Advantages of Using Cloud Functions

- **Cost-Effective**: You only pay for the compute time your functions consume, with no charges for idle time.
- **Easy Deployment**: Deploying functions is straightforward using the Firebase CLI, and you can update your functions without downtime.
- **Automatic Scaling**: Functions automatically scale up or down based on the number of incoming requests or events.

##### 3. Setting Up Firebase Cloud Functions

To get started with Firebase Cloud Functions, you need to set up your Firebase project and install the Firebase CLI.

**Step 1: Install Firebase CLI**

If you haven’t already, install the Firebase CLI globally using npm:

```bash
npm install -g firebase-tools
```

**Step 2: Initialize Cloud Functions in Your Firebase Project**

1. Open your terminal and navigate to your Firebase project directory.
2. Run the following command to initialize Cloud Functions:

   ```bash
   firebase init functions
   ```

3. Follow the prompts to set up your functions. Choose your preferred language (JavaScript or TypeScript) and whether to use ESLint for code linting.

**Step 3: Set Up Your Functions Directory**

After initialization, a `functions` directory will be created in your project folder. This directory will contain your Cloud Functions code.

### Part A: Creating Your First Cloud Function

#### Step 1: Write a Simple HTTP Function

Open the `functions/src/index.ts` file (or `index.js` if you chose JavaScript) and add the following code to create a simple HTTP function:

```typescript
// functions/src/index.ts
import * as functions from "firebase-functions";

// Create an HTTP function
export const helloWorld = functions.https.onRequest((request, response) => {
  response.send("Hello from Firebase Cloud Functions!");
});
```

This function responds to HTTP requests with a simple greeting message.

#### Step 2: Deploy Your Function

To deploy your function to Firebase, run the following command in your terminal:

```bash
firebase deploy --only functions
```

After deployment, you will receive a URL where your function is accessible.

### Part B: Integrating Cloud Functions with Your Application

#### Step 1: Create a Function to Handle File Uploads

You can create a Cloud Function that triggers on file uploads to Firebase Storage. For example, you might want to generate a thumbnail image whenever a user uploads a new photo.

Add the following code to your `index.ts` file:

```typescript
import * as admin from "firebase-admin";
import * as functions from "firebase-functions";
import { Storage } from "@google-cloud/storage";

// Initialize Firebase Admin SDK
admin.initializeApp();
const gcs = new Storage();

// Trigger function when a file is uploaded to Firebase Storage
export const generateThumbnail = functions.storage
  .object()
  .onFinalize(async (object) => {
    const filePath = object.name;
    const bucketName = object.bucket;

    // Your logic to generate a thumbnail goes here
    console.log(`File uploaded: ${filePath} in bucket: ${bucketName}`);
  });
```

This function will trigger every time a file is uploaded to Firebase Storage, allowing you to implement custom logic, such as generating thumbnails.

#### Step 2: Deploy the Function

Deploy the updated functions again:

```bash
firebase deploy --only functions
```

### Part C: Calling Cloud Functions from React TypeScript Frontend

You can call your Cloud Functions from your React application using the Firebase SDK.

#### Step 1: Install Firebase SDK

If you haven’t already, ensure you have the Firebase SDK installed in your React project:

```bash
npm install firebase
```

#### Step 2: Create a Function to Call Your Cloud Function

In your `firebaseConfig.ts`, ensure you import and initialize Firebase Functions:

```typescript
// src/firebaseConfig.ts
import { initializeApp } from "firebase/app";
import { getFunctions, httpsCallable } from "firebase/functions";

const firebaseConfig = {
  // Your Firebase config
};

const app = initializeApp(firebaseConfig);
const functions = getFunctions(app); // Initialize Firebase Functions

export { app, functions };
```

#### Step 3: Call the Cloud Function in Your Component

Create a new component named `FunctionCaller.tsx` to call the `helloWorld` function:

```typescript
// src/FunctionCaller.tsx
import React from "react";
import { functions } from "./firebaseConfig";
import { httpsCallable } from "firebase/functions";

const FunctionCaller: React.FC = () => {
  const callHelloWorld = async () => {
    const helloWorld = httpsCallable(functions, "helloWorld");
    const result = await helloWorld();
    console.log(result.data); // Should log "Hello from Firebase Cloud Functions!"
  };

  return (
    <div>
      <h2>Call Cloud Function</h2>
      <button onClick={callHelloWorld}>Call Hello World Function</button>
    </div>
  );
};

export default FunctionCaller;
```

### Part D: Integrating Function Caller Component

Integrate the `FunctionCaller` component into your main application file (e.g., `App.tsx`):

```typescript
// src/App.tsx
import React from "react";
import FunctionCaller from "./FunctionCaller";

const App: React.FC = () => {
  return (
    <div>
      <h1>Firebase Cloud Functions Example</h1>
      <FunctionCaller />
    </div>
  );
};

export default App;
```

### Step 4: Running the Application

1. **Start the Flask Backend**:
   Ensure your Flask backend is running (if you have any endpoints related to file handling):

   ```bash
   python app.py
   ```

2. **Start the React Application**:
   In your terminal, run the following command to start the React application:

   ```bash
   npm start
   ```

   This will start the development server, and you can access your application at `http://localhost:3000`.

3. **Test the Cloud Function**:
   - Click the button in the `FunctionCaller` component to invoke the Cloud Function.
   - Check the console for the response from the Cloud Function.

### Conclusion

In this lesson, we introduced Firebase Cloud Functions, a powerful serverless framework for executing backend code in response to events. We covered how to set up Cloud Functions, create HTTP functions, and trigger functions on Firebase Storage events. Additionally, we demonstrated how to call Cloud Functions from a React TypeScript frontend.

[Next: lesson-5-2-writing-deploying-functions](./lesson-5-2-writing-deploying-functions.md)
