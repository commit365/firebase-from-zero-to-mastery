### Module 2: Firebase Authentication

#### Lesson 2.2: Advanced Authentication Techniques

In this lesson, we will explore advanced techniques for implementing Firebase Authentication in your applications. Specifically, we will cover how to use custom tokens for authentication and how to implement multi-factor authentication (MFA) to enhance security.

##### 1. Using Firebase Authentication with Custom Tokens

Custom tokens allow you to integrate Firebase Authentication with your existing authentication system. This is particularly useful when you need to authenticate users from a different identity provider or when you want to manage user sessions on your backend.

**Step 1: Generate Custom Tokens**

To use custom tokens, you need to generate them on your server. You can do this using the Firebase Admin SDK. First, ensure you have the Firebase Admin SDK installed in your Python environment (or Node.js, depending on your backend). For Python, you can install it using:

```bash
pip install firebase-admin
```

**Step 2: Initialize the Firebase Admin SDK**

Create a file named `firebase_admin_setup.py` and set up the Firebase Admin SDK with your service account credentials:

```python
# firebase_admin_setup.py
import firebase_admin
from firebase_admin import credentials

# Initialize Firebase Admin SDK
cred = credentials.Certificate('path/to/serviceAccountKey.json')
firebase_admin.initialize_app(cred)
```

**Step 3: Create a Function to Generate Custom Tokens**

Next, create a function that generates custom tokens for authenticated users:

```python
# firebase_admin_setup.py (continued)
from firebase_admin import auth

def create_custom_token(uid: str) -> str:
    # Generate a custom token for the user
    custom_token = auth.create_custom_token(uid)
    return custom_token
```

**Step 4: Authenticate Users with Custom Tokens in Your React Application**

Once you have generated a custom token on your backend, you can use it to authenticate users in your React application. In your `auth.ts` file, add a new function to sign in with the custom token:

```typescript
// src/auth.ts (continued)
import { signInWithCustomToken } from "firebase/auth";

// Function to sign in with a custom token
export const signInWithCustomToken = async (token: string): Promise<void> => {
  try {
    await signInWithCustomToken(auth, token);
    console.log("User signed in with custom token successfully");
  } catch (error) {
    console.error("Error signing in with custom token:", error);
  }
};
```

**Step 5: Example Usage in Your React Component**

You can now use the `signInWithCustomToken` function in your React component. For example, after a user is authenticated on your backend, you can retrieve the custom token and pass it to this function:

```typescript
// Example usage in AuthForm.tsx
const handleCustomLogin = async (uid: string) => {
  const customToken = await fetchCustomToken(uid); // Implement this function to get the token from your backend
  await signInWithCustomToken(customToken);
};
```

##### 2. Implementing Multi-Factor Authentication (MFA)

Multi-factor authentication adds an extra layer of security by requiring users to provide two or more verification methods to gain access to their accounts. Firebase Authentication supports MFA using SMS verification.

**Step 1: Enable Multi-Factor Authentication in Firebase Console**

1. Go to the [Firebase Console](https://console.firebase.google.com/) and select your project.
2. Navigate to **Authentication** > **Sign-in method**.
3. Under the **Multi-factor authentication** section, enable the option for phone number verification.

**Step 2: Set Up Phone Number Authentication**

To implement MFA, you first need to set up phone number authentication. Ensure that you have enabled phone authentication in the Firebase Console under the **Sign-in method** tab.

**Step 3: Add Phone Number Verification in Your React Application**

In your `AuthForm.tsx`, you will need to add functionality to handle phone number verification. Here’s how to do it:

1. Update your state to manage phone number input and verification code:

```typescript
const [phoneNumber, setPhoneNumber] = useState<string>("");
const [verificationCode, setVerificationCode] = useState<string>("");
const [verificationId, setVerificationId] = useState<string>("");
```

2. Create a function to send the verification code:

```typescript
import { RecaptchaVerifier, signInWithPhoneNumber } from "firebase/auth";

// Function to send verification code
const sendVerificationCode = async () => {
  const appVerifier = new RecaptchaVerifier("recaptcha-container", {}, auth);
  try {
    const confirmationResult = await signInWithPhoneNumber(
      auth,
      phoneNumber,
      appVerifier
    );
    setVerificationId(confirmationResult.verificationId);
    console.log("Verification code sent to:", phoneNumber);
  } catch (error) {
    console.error("Error sending verification code:", error);
  }
};
```

3. Create a function to verify the code entered by the user:

```typescript
const verifyCode = async () => {
  const credential = PhoneAuthProvider.credential(
    verificationId,
    verificationCode
  );
  try {
    await signInWithCredential(auth, credential);
    console.log("User signed in successfully with phone number");
  } catch (error) {
    console.error("Error verifying code:", error);
  }
};
```

4. Update the render method to include input fields for the phone number and verification code:

```typescript
return (
  <div>
    {/* Existing login/register form */}

    <h2>Phone Number Authentication</h2>
    <input
      type="text"
      placeholder="Phone Number"
      value={phoneNumber}
      onChange={(e) => setPhoneNumber(e.target.value)}
    />
    <button onClick={sendVerificationCode}>Send Verification Code</button>

    <div id="recaptcha-container"></div>

    <input
      type="text"
      placeholder="Verification Code"
      value={verificationCode}
      onChange={(e) => setVerificationCode(e.target.value)}
    />
    <button onClick={verifyCode}>Verify Code</button>
  </div>
);
```

##### Conclusion

In this lesson, you learned how to implement advanced authentication techniques using Firebase Authentication. You explored how to use custom tokens for authentication, allowing you to integrate with existing authentication systems. Additionally, you learned how to implement multi-factor authentication (MFA) using phone number verification to enhance the security of your application.

With these advanced techniques, you can provide a more secure and flexible authentication experience for your users.

[Next: lesson-3-1-introduction-to-firestore](../module-3-firestore/lesson-3-1-introduction-to-firestore.md)
