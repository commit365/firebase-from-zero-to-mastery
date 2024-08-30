### Module 1: Introduction to Firebase

#### Lesson 1.3: Firebase SDKs

In this lesson, we will cover how to install and configure Firebase SDKs for your React application using TypeScript. Additionally, we will provide an overview of the Firebase Admin SDK for Python, which is useful for server-side operations in your applications.

##### 1. Installing and Configuring Firebase SDKs for React with TypeScript

To use Firebase services in your React application, you need to install the Firebase JavaScript SDK. This SDK provides all the necessary tools to integrate Firebase features into your frontend application.

**Step 1: Create a New React Project**

If you haven't already created a React project, you can do so using Create React App with TypeScript. Open your terminal and run the following command:

```bash
npx create-react-app my-firebase-app --template typescript
```

Replace `my-firebase-app` with your desired project name. This command will create a new React project with TypeScript support.

**Step 2: Navigate to Your Project Directory**

Change your working directory to the newly created project:

```bash
cd my-firebase-app
```

**Step 3: Install Firebase SDK**

To install the Firebase SDK, run the following command in your terminal:

```bash
npm install firebase
```

This command will add the Firebase SDK to your project, allowing you to access Firebase services.

**Step 4: Configure Firebase in Your Application**

1. **Create a Firebase Configuration File**: In your `src` directory, create a new file named `firebaseConfig.ts`. This file will contain the configuration settings for your Firebase project.

2. **Get Firebase Configuration Settings**: Go back to the Firebase Console, select your project, and navigate to **Project settings** (gear icon). In the **General** tab, scroll down to the **Your apps** section and select the **Web** icon (</>). If you haven't added a web app yet, click on **Add app** and follow the prompts. Once done, you will receive your Firebase configuration settings.

3. **Add Configuration to `firebaseConfig.ts`**: Copy the Firebase configuration settings and paste them into your `firebaseConfig.ts` file. It should look something like this:

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

   Make sure to replace the placeholder values with the actual configuration values from your Firebase project.

**Step 5: Using Firebase Services in Your Application**

Now that you have configured Firebase, you can start using its services in your React components. For example, to use Firebase Authentication, you can import the necessary functions from the Firebase SDK:

```typescript
import { getAuth, signInWithEmailAndPassword } from "firebase/auth";
import app from "./firebaseConfig";

const auth = getAuth(app);

// Example function to sign in a user
const signInUser = async (email: string, password: string) => {
  try {
    const userCredential = await signInWithEmailAndPassword(
      auth,
      email,
      password
    );
    const user = userCredential.user;
    console.log("User signed in:", user);
  } catch (error) {
    console.error("Error signing in:", error);
  }
};
```

This code snippet demonstrates how to set up Firebase Authentication in your React application. You can similarly import and use other Firebase services, such as Firestore or Storage, by following the corresponding documentation.

##### 2. Overview of Firebase Admin SDK for Python

While the Firebase JavaScript SDK is designed for client-side applications, the Firebase Admin SDK is intended for server-side operations. It allows you to perform administrative tasks such as managing users, accessing Firestore, and sending messages via Firebase Cloud Messaging.

**Key Features of the Firebase Admin SDK for Python:**

- **User Management**: Create, delete, and update user accounts programmatically. You can also verify ID tokens and manage user sessions.
- **Access to Firestore**: Perform CRUD operations on Firestore collections and documents, enabling you to manage your database from the server side.

- **Cloud Messaging**: Send push notifications and messages to users' devices using Firebase Cloud Messaging.

**Step 1: Installing the Firebase Admin SDK**

To use the Firebase Admin SDK in your Python application, you need to install it using pip. Run the following command in your terminal:

```bash
pip install firebase-admin
```

**Step 2: Initializing the Admin SDK**

To initialize the Firebase Admin SDK, you need to provide your service account credentials. You can generate a service account key from the Firebase Console:

1. Go to **Project settings** in the Firebase Console.
2. Navigate to the **Service accounts** tab.
3. Click on **Generate new private key**. This will download a JSON file containing your service account credentials.

Use the following code to initialize the Admin SDK in your Python application:

```python
import firebase_admin
from firebase_admin import credentials

# Path to your service account key file
cred = credentials.Certificate('path/to/serviceAccountKey.json')

# Initialize the Firebase Admin SDK
firebase_admin.initialize_app(cred)
```

**Step 3: Using the Admin SDK**

Once initialized, you can use the Admin SDK to perform various operations. For example, to create a new user, you can use the following code:

```python
from firebase_admin import auth

# Create a new user
user = auth.create_user(
    email='user@example.com',
    password='password123',
)

print('Successfully created new user:', user.uid)
```

This snippet demonstrates how to create a new user account using the Firebase Admin SDK.

##### Conclusion

In this lesson, you learned how to install and configure the Firebase SDK for your React application using TypeScript. You also gained an overview of the Firebase Admin SDK for Python, which allows you to perform server-side operations with Firebase services.

With these tools in place, you are now ready to start building applications that leverage Firebase's powerful capabilities.

[Next: lesson-2-1-user-authentication-basics](../module-2-authentication/lesson-2-1-user-authentication-basics.md)
