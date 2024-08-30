# Project 6.1: Deploying Your First App

## Overview

The **Deploying Your First App** project demonstrates how to deploy a web application using Firebase Hosting. This project showcases a complete workflow for deploying a React TypeScript frontend application while optionally managing a Python Flask backend. By following this guide, you will learn how to set up Firebase Hosting, prepare your application for deployment, and manage your deployed application effectively.

## Features

- Deploy a React TypeScript frontend application to Firebase Hosting.
- Optionally deploy a Python Flask backend to Google Cloud Run or another cloud provider.
- Set up custom domain support for your application.
- Manage redirects and rewrites for your deployed application.

## Prerequisites

Before you begin, ensure you have the following:

- A Firebase project set up with Firebase Hosting enabled.
- Python installed (preferably Python 3.7 or higher).
- Node.js installed (preferably version 14 or higher).
- Basic knowledge of React, TypeScript, Flask, and Firebase.

## Project Structure

The project consists of two main parts: the React frontend and the optional Flask backend.

```
deploying-your-first-app/
├── backend/                     # Optional Flask backend
│   ├── app.py                   # Main Flask application
│   ├── requirements.txt         # Python dependencies
│   └── serviceAccountKey.json   # Firebase service account key
└── frontend/                    # React frontend
    ├── src/
    │   ├── components/          # React components
    │   │   └── ExampleComponent.tsx # Example component
    │   ├── firebaseConfig.ts      # Firebase configuration
    │   ├── App.tsx                # Main application component
    │   └── index.tsx              # Entry point
    ├── public/                   # Public assets
    ├── package.json              # Node.js dependencies
    └── tsconfig.json             # TypeScript configuration
```

## Step 1: Setting Up Firebase Hosting

### 1.1 Install Firebase CLI

If you haven’t already, install the Firebase CLI globally using npm:

```bash
npm install -g firebase-tools
```

### 1.2 Initialize Firebase Hosting

1. Open your terminal and navigate to your project directory.
2. Run the following command to initialize Firebase Hosting:

   ```bash
   firebase init hosting
   ```

3. Follow the prompts to select your Firebase project and specify the public directory (typically `build` for React applications).

### 1.3 Prepare Your Application for Deployment

For a React application, you need to create a production build.

1. Navigate to your React project directory in the terminal.
2. Run the following command to create a production build:

   ```bash
   npm run build
   ```

This command generates a `build` directory containing the optimized files needed for deployment.

## Step 2: Deploying the React Application

### 2.1 Deploy to Firebase Hosting

To deploy your application to Firebase Hosting, run the following command:

```bash
firebase deploy --only hosting
```

After deployment, you will receive a hosting URL where your application is accessible. You can also view your project in the Firebase Console under the Hosting section.

## Step 3: Deploying the Flask Backend (Optional)

If your application includes a Flask backend, you will typically deploy it separately. Here are a few common options for deploying your Flask application:

### 3.1 Deploying to Google Cloud Run

Google Cloud Run is a fully managed compute platform that automatically scales your containerized applications. To deploy your Flask app to Cloud Run:

1. **Containerize Your Flask Application**:

   - Create a `Dockerfile` in your Flask project directory:

   ```dockerfile
   # Dockerfile
   FROM python:3.9-slim

   WORKDIR /app
   COPY requirements.txt requirements.txt
   RUN pip install -r requirements.txt
   COPY . .

   CMD ["python", "app.py"]
   ```

2. **Build the Docker Image**:

   - Run the following command to build your Docker image:

   ```bash
   docker build -t gcr.io/YOUR_PROJECT_ID/flask-app .
   ```

3. **Push the Docker Image to Google Container Registry**:

   ```bash
   docker push gcr.io/YOUR_PROJECT_ID/flask-app
   ```

4. **Deploy to Cloud Run**:

   - Use the following command to deploy your application to Cloud Run:

   ```bash
   gcloud run deploy --image gcr.io/YOUR_PROJECT_ID/flask-app --platform managed
   ```

5. Follow the prompts to configure your service, including the service name and region.

### 3.2 Deploying to Heroku or Other Platforms

Alternatively, you can deploy your Flask application to platforms like Heroku, AWS, or DigitalOcean. Each platform has its own deployment process, so refer to their respective documentation for specific instructions.

## Step 4: Managing Your Deployments

Once your applications are deployed, you can manage them through the Firebase Console and the Google Cloud Console.

### 4.1 Firebase Console

- **View Hosting Activity**: Check the Hosting section in the Firebase Console to view deployment history and access logs.
- **Set Up Redirects and Rewrites**: Configure any necessary redirects and rewrites in the `firebase.json` file to manage URL routing.

### 4.2 Google Cloud Console

- **Monitor Flask App**: Use the Google Cloud Console to monitor the performance and logs of your Flask application if deployed on Cloud Run.
- **Manage Services**: You can view and manage your deployed services, including scaling options and environment variables.

## Step 5: Troubleshooting Common Issues

During deployment, you may encounter various issues. Here are some common troubleshooting steps:

- **Build Errors**: If you encounter errors during the build process, check the console output for specific error messages and ensure all dependencies are correctly defined in your `package.json` or `requirements.txt`.
- **Deployment Failures**: If deployment fails, review the error messages in the Firebase Console or the logs in the Google Cloud Console to identify the issue.
- **Access Issues**: Ensure that your Firebase Hosting security rules and Cloud Run permissions are correctly configured to allow access to your applications.

## Conclusion

In this project, we built a serverless API using Firebase Cloud Functions and deployed a React TypeScript frontend application to Firebase Hosting. We also explored the deployment process for a Flask backend, demonstrating how to create scalable and efficient web applications using modern cloud technologies.

This project serves as a foundation for building more complex applications that require serverless architecture and cloud hosting solutions.
