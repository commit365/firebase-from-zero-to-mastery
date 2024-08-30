### Module 5: Firebase Cloud Functions

#### Lesson 5.2: Writing and Deploying Functions

In this lesson, we will dive deeper into Firebase Cloud Functions by learning how to write, deploy, and manage functions effectively. We will cover various types of functions, including HTTP functions and background functions triggered by Firebase events. Additionally, we will explore best practices for organizing your functions and handling errors. By the end of this lesson, you will be equipped to write and deploy your own Cloud Functions in a React TypeScript frontend and Python Flask backend environment.

##### 1. Types of Cloud Functions

Firebase Cloud Functions can be categorized into two main types:

- **HTTP Functions**: These functions are triggered by HTTP requests. They can be used to create RESTful APIs or serve webhooks.
- **Background Functions**: These functions are triggered by events from Firebase services, such as Firestore, Firebase Authentication, or Firebase Storage. They run in response to changes in your Firebase project.

##### 2. Writing Cloud Functions

Let's start by writing some Cloud Functions. Ensure you have the Firebase CLI installed and your functions directory set up as described in the previous lesson.

**Step 1: Create an HTTP Function**

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

**Step 2: Create a Background Function**

Next, let's create a background function that triggers when a new document is added to a Firestore collection. This function will log the new document's data.

```typescript
import * as admin from "firebase-admin";
import * as functions from "firebase-functions";

// Initialize Firebase Admin SDK
admin.initializeApp();

// Trigger function when a new document is added to the 'messages' collection
export const logNewMessage = functions.firestore
  .document("messages/{messageId}")
  .onCreate((snapshot, context) => {
    const newValue = snapshot.data();
    console.log("New message added:", newValue);
    return null; // Return a promise or null to indicate completion
  });
```

In this code:

- The function listens for new documents added to the `messages` collection.
- When a new document is created, it logs the document's data to the console.

##### 3. Deploying Cloud Functions

Once you have written your functions, you can deploy them to Firebase.

**Step 1: Deploy Functions**

To deploy your functions, run the following command in your terminal:

```bash
firebase deploy --only functions
```

After deployment, you will receive URLs for your HTTP functions and confirmation that your background functions are active.

**Step 2: Testing HTTP Functions**

You can test your HTTP functions using tools like Postman or simply by navigating to the function's URL in your web browser. For example, if your function is deployed at `https://<your-project-id>.cloudfunctions.net/helloWorld`, you can access it directly.

##### 4. Best Practices for Writing Cloud Functions

- **Organize Your Code**: Keep your functions organized by grouping related functions into separate files or modules. This makes your codebase easier to manage and maintain.
- **Use Environment Variables**: Store sensitive information (like API keys) in environment variables instead of hardcoding them in your functions. You can set environment variables using the Firebase CLI:

  ```bash
  firebase functions:config:set someservice.key="THE_API_KEY"
  ```

- **Error Handling**: Implement proper error handling in your functions to manage exceptions gracefully. Use try-catch blocks and return appropriate HTTP status codes for HTTP functions.

```typescript
export const safeFunction = functions.https.onRequest(
  async (request, response) => {
    try {
      // Your function logic here
      response.send("Function executed successfully!");
    } catch (error) {
      console.error("Error executing function:", error);
      response.status(500).send("Internal Server Error");
    }
  }
);
```

- **Logging**: Use `console.log()` for logging information and debugging. You can view logs in the Firebase Console under the Functions section.

##### 5. Managing Cloud Functions

After deploying your functions, you can manage them using the Firebase Console or the Firebase CLI. You can view logs, monitor performance, and update or delete functions as needed.

**Step 1: Viewing Logs**

To view logs for your functions, go to the Firebase Console, select your project, and navigate to the **Functions** section. Here, you can see logs for each function, including any errors that occurred during execution.

**Step 2: Updating Functions**

To update a function, simply modify the code in your `index.ts` file and redeploy using the same command:

```bash
firebase deploy --only functions
```

**Step 3: Deleting Functions**

To delete a function, you can use the Firebase CLI:

```bash
firebase functions:delete functionName
```

##### Conclusion

In this lesson, we explored how to write and deploy Firebase Cloud Functions. We covered both HTTP functions and background functions, demonstrating how to respond to events and handle requests. Additionally, we discussed best practices for writing and managing your functions.

[Next: lesson-6-1-introduction-to-hosting](../module-6-hosting/lesson-6-1-introduction-to-hosting.md)
