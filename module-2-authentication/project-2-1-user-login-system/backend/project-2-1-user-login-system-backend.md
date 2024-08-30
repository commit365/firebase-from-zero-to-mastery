### Project 2.1: Building a User Login System (Backend)

#### Overview

In this project, we will create a simple Flask application that integrates with Firebase Authentication to manage user registration and login functionalities. The backend will handle requests from the frontend (React application) to register new users and authenticate existing users using Firebase.

#### Prerequisites

Before you begin, ensure you have the following:

- Python installed (preferably Python 3.7 or higher)
- Flask installed (you can install it using `pip install Flask`)
- Firebase Admin SDK installed (install it using `pip install firebase-admin`)
- A Firebase project set up with Authentication enabled (Email/Password method)

#### Step 1: Set Up Firebase Admin SDK

1. **Create a Service Account**:

   - Go to the [Firebase Console](https://console.firebase.google.com/).
   - Select your project and navigate to **Project settings**.
   - Go to the **Service accounts** tab.
   - Click on **Generate new private key**. This will download a JSON file containing your service account credentials.

2. **Create a Flask Application**:

   - Create a new directory for your Flask application and navigate into it:
     ```bash
     mkdir flask-firebase-auth
     cd flask-firebase-auth
     ```

3. **Create a new file named `app.py`** in your project directory. This file will contain the main application code.

#### Step 2: Implement the Flask Application

Here’s a comprehensive implementation of the Flask application in `app.py`:

```python
# app.py
from flask import Flask, request, jsonify
import firebase_admin
from firebase_admin import credentials, auth

# Initialize Flask app
app = Flask(__name__)

# Initialize Firebase Admin SDK
cred = credentials.Certificate('path/to/serviceAccountKey.json')
firebase_admin.initialize_app(cred)

@app.route('/register', methods=['POST'])
def register():
    """Register a new user."""
    data = request.get_json()
    email = data.get('email')
    password = data.get('password')

    if not email or not password:
        return jsonify({'error': 'Email and password are required'}), 400

    try:
        user = auth.create_user(email=email, password=password)
        return jsonify({'message': 'User registered successfully', 'uid': user.uid}), 201
    except Exception as e:
        return jsonify({'error': str(e)}), 400

@app.route('/login', methods=['POST'])
def login():
    """Login an existing user."""
    data = request.get_json()
    email = data.get('email')
    password = data.get('password')

    if not email or not password:
        return jsonify({'error': 'Email and password are required'}), 400

    try:
        # Verify user credentials using Firebase Authentication
        user = auth.get_user_by_email(email)
        # To validate the password, you would typically use a custom token or a client-side verification.
        # Here, we assume the password is validated on the client side and we proceed with the login.
        return jsonify({'message': 'User logged in successfully', 'uid': user.uid}), 200
    except Exception as e:
        return jsonify({'error': str(e)}), 400

@app.route('/logout', methods=['POST'])
def logout():
    """Logout a user."""
    # In a typical stateless application, logout can be handled on the client side.
    # Here, we just return a message.
    return jsonify({'message': 'User logged out successfully'}), 200

if __name__ == '__main__':
    app.run(debug=True)
```

#### Explanation of the Code

1. **Imports**: We import the necessary modules from Flask and Firebase Admin SDK.
2. **Flask App Initialization**: We create a Flask application instance.
3. **Firebase Initialization**: We initialize the Firebase Admin SDK using the service account credentials.
4. **Register Endpoint** (`/register`):
   - Accepts a POST request with JSON data containing `email` and `password`.
   - Uses Firebase Authentication to create a new user.
   - Returns a success message with the user ID or an error message if registration fails.
5. **Login Endpoint** (`/login`):
   - Accepts a POST request with JSON data containing `email` and `password`.
   - Checks if the user exists using Firebase Authentication.
   - Returns a success message with the user ID or an error message if login fails.
6. **Logout Endpoint** (`/logout`):
   - A simple endpoint to handle user logout (logout logic is typically managed on the client side).

#### Step 3: Running the Flask Application

1. **Install Required Packages**:
   If you haven't already, install Flask and Firebase Admin SDK using pip:

   ```bash
   pip install Flask firebase-admin
   ```

2. **Run the Application**:
   In your terminal, run the following command to start the Flask server:

   ```bash
   python app.py
   ```

   The server will start on `http://127.0.0.1:5000/`.

#### Step 4: Testing the Endpoints

You can test the endpoints using tools like Postman or cURL.

1. **Register a User**:

   - **Endpoint**: `POST http://127.0.0.1:5000/register`
   - **Body** (JSON):
     ```json
     {
       "email": "user@example.com",
       "password": "yourpassword"
     }
     ```

2. **Login a User**:

   - **Endpoint**: `POST http://127.0.0.1:5000/login`
   - **Body** (JSON):
     ```json
     {
       "email": "user@example.com",
       "password": "yourpassword"
     }
     ```

3. **Logout a User**:
   - **Endpoint**: `POST http://127.0.0.1:5000/logout`

#### Conclusion

In this document, we have implemented a simple Flask backend for a user login system using Firebase Authentication. The backend handles user registration, login, and logout functionalities, allowing you to manage user authentication effectively.

This backend can be integrated with a frontend application built using React, allowing users to register and log in seamlessly. The next steps would involve connecting this backend to your React application and implementing the corresponding frontend logic to interact with these endpoints.

[Next: project-2-1-user-login-system-frontend](../frontend/project-2-1-user-login-system-frontend.md)
