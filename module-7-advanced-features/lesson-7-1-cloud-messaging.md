### Module 7: Advanced Firebase Features

#### Lesson 7.1: Firebase Cloud Messaging (FCM)

In this lesson, we will explore Firebase Cloud Messaging (FCM), a powerful service that enables you to send notifications and messages to users across platforms. FCM allows you to engage users with timely updates and alerts, making it an essential feature for modern applications. By the end of this lesson, you will understand how to set up FCM, send notifications, and integrate it into a React TypeScript frontend and a Python Flask backend.

##### 1. What is Firebase Cloud Messaging (FCM)?

Firebase Cloud Messaging (FCM) is a cross-platform messaging solution that allows you to send messages and notifications to users on Android, iOS, and web applications. It supports both push notifications and data messages, enabling you to engage users with relevant content.

**Key Features of FCM:**

- **Cross-Platform Support**: Send messages to users across different platforms, including mobile and web.
- **Notification Messages**: Display notifications to users even when the app is in the background or closed.
- **Data Messages**: Send data payloads to your application for processing, allowing for more customized user experiences.
- **Topic Messaging**: Send messages to multiple users who have subscribed to specific topics, making it easy to target groups of users.

##### 2. Advantages of Using FCM

- **User Engagement**: Keep users informed and engaged with timely notifications and updates.
- **Scalability**: FCM can handle millions of messages and notifications, making it suitable for applications with large user bases.
- **Easy Integration**: FCM integrates seamlessly with other Firebase services, allowing for a comprehensive solution for app development.

##### 3. Setting Up Firebase Cloud Messaging

To get started with Firebase Cloud Messaging, follow these steps:

**Step 1: Enable Cloud Messaging in Firebase Console**

1. Go to the [Firebase Console](https://console.firebase.google.com/).
2. Select your project and navigate to the **Cloud Messaging** section.
3. Note down your **Server Key** and **Sender ID**, as you will need these for sending messages from your backend.

**Step 2: Configure Your React Application**

1. Install the Firebase SDK in your React project if you haven't already:

   ```bash
   npm install firebase
   ```

2. In your `firebaseConfig.ts` file, import and initialize Firebase Cloud Messaging:

   ```typescript
   // src/firebaseConfig.ts
   import { initializeApp } from "firebase/app";
   import { getMessaging } from "firebase/messaging";

   const firebaseConfig = {
     apiKey: "YOUR_API_KEY",
     authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
     projectId: "YOUR_PROJECT_ID",
     storageBucket: "YOUR_PROJECT_ID.appspot.com",
     messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
     appId: "YOUR_APP_ID",
   };

   const app = initializeApp(firebaseConfig);
   const messaging = getMessaging(app); // Initialize Firebase Cloud Messaging

   export { app, messaging };
   ```

**Step 3: Requesting Permission to Send Notifications**

In your main application component (e.g., `App.tsx`), request permission to send notifications and get the FCM token:

```typescript
// src/App.tsx
import React, { useEffect } from "react";
import { messaging } from "./firebaseConfig";
import { getToken } from "firebase/messaging";

const App: React.FC = () => {
  useEffect(() => {
    const requestPermission = async () => {
      try {
        const token = await getToken(messaging, { vapidKey: "YOUR_VAPID_KEY" });
        if (token) {
          console.log("FCM Token:", token);
          // Send the token to your server or save it for later use
        } else {
          console.log("No registration token available.");
        }
      } catch (error) {
        console.error("Error retrieving FCM token:", error);
      }
    };

    requestPermission();
  }, []);

  return (
    <div>
      <h1>Firebase Cloud Messaging Example</h1>
      {/* Your application components */}
    </div>
  );
};

export default App;
```

**Note**: Replace `'YOUR_VAPID_KEY'` with your actual VAPID key, which you can generate in the Firebase Console under **Project Settings > Cloud Messaging**.

##### 4. Sending Notifications from the Flask Backend

To send notifications from your Python Flask backend, you will need to use the FCM server key.

**Step 1: Install Required Packages**

If you haven't already, install the `requests` package to make HTTP requests:

```bash
pip install requests
```

**Step 2: Create a Function to Send Notifications**

In your Flask application (e.g., `app.py`), create a function to send notifications:

```python
# app.py
import requests
from flask import Flask, request, jsonify

app = Flask(__name__)

FCM_SERVER_KEY = 'YOUR_SERVER_KEY'  # Replace with your FCM server key
FCM_URL = 'https://fcm.googleapis.com/fcm/send'

def send_push_notification(token, message_title, message_body):
    headers = {
        'Content-Type': 'application/json',
        'Authorization': f'key={FCM_SERVER_KEY}'
    }
    payload = {
        'to': token,
        'notification': {
            'title': message_title,
            'body': message_body
        }
    }
    response = requests.post(FCM_URL, headers=headers, json=payload)
    return response.json()

@app.route('/send-notification', methods=['POST'])
def notify_user():
    data = request.get_json()
    token = data.get('token')
    message_title = data.get('title')
    message_body = data.get('body')

    response = send_push_notification(token, message_title, message_body)
    return jsonify(response)

if __name__ == '__main__':
    app.run(debug=True)
```

In this code:

- The `send_push_notification` function constructs the payload and sends a POST request to the FCM API.
- The `/send-notification` endpoint accepts a POST request with the FCM token, title, and body of the notification.

##### 5. Testing Notifications

To test sending notifications:

1. Start your Flask backend:

   ```bash
   python app.py
   ```

2. Use a tool like Postman to send a POST request to `http://127.0.0.1:5000/send-notification` with the following JSON body:

```json
{
  "token": "YOUR_FCM_TOKEN",
  "title": "Test Notification",
  "body": "This is a test notification from Flask!"
}
```

Replace `"YOUR_FCM_TOKEN"` with the actual FCM token you obtained from the frontend.

##### 6. Handling Incoming Notifications

To handle incoming notifications in your React application, you can add an event listener for messages:

```typescript
// src/App.tsx (continued)
import { onMessage } from "firebase/messaging";

useEffect(() => {
  onMessage(messaging, (payload) => {
    console.log("Message received. ", payload);
    // Customize notification handling here
    alert(
      `Notification: ${payload.notification?.title} - ${payload.notification?.body}`
    );
  });
}, []);
```

This code sets up an event listener that triggers whenever a notification is received while the app is in the foreground.

##### Conclusion

In this lesson, we explored Firebase Cloud Messaging (FCM) and how to integrate it into a React TypeScript frontend and Python Flask backend. We covered how to set up FCM, request permissions, send notifications from the backend, and handle incoming notifications in the frontend.

FCM is a powerful tool for engaging users with timely updates and alerts, making it essential for modern applications.

[Next: lesson-7-2-analytics](./lesson-7-2-analytics.md)
