### Module 4: Firebase Storage

#### Lesson 4.2: Uploading and Retrieving Files

In this lesson, we will delve deeper into Firebase Storage by focusing on how to upload files from a React TypeScript frontend and retrieve them for display or download. We will also cover best practices for managing files in Firebase Storage and how to integrate these functionalities with a Python Flask backend. By the end of this lesson, you will be able to implement file upload and retrieval features in your applications.

##### 1. Overview of File Uploading and Retrieval

Firebase Storage provides a simple and efficient way to store and serve user-generated content such as images, videos, and documents. The process involves:

- **Uploading Files**: Sending files from the client (frontend) to Firebase Storage.
- **Retrieving Files**: Accessing files stored in Firebase Storage and displaying them in the application or providing download links.

##### 2. Setting Up Firebase Storage

Before proceeding, ensure you have set up Firebase Storage in your Firebase project as described in the previous lesson. You should have the Firebase SDK configured in your React frontend and the Firebase Admin SDK set up in your Flask backend.

### Part A: Uploading Files in React TypeScript Frontend

#### Step 1: Create a File Upload Component

In your React application, create a file upload component that allows users to select and upload files. Create a new file named `FileUpload.tsx` in the `src` directory:

```typescript
// src/FileUpload.tsx
import React, { useState } from "react";
import { uploadFile } from "./storageService";

const FileUpload: React.FC = () => {
  const [file, setFile] = useState<File | null>(null);
  const [uploadMessage, setUploadMessage] = useState<string>("");

  const handleFileChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    if (event.target.files) {
      setFile(event.target.files[0]);
    }
  };

  const handleUpload = async (event: React.FormEvent) => {
    event.preventDefault();
    if (file) {
      try {
        const url = await uploadFile(file);
        setUploadMessage(`File uploaded successfully! Download URL: ${url}`);
        setFile(null); // Reset the file input
      } catch (error) {
        setUploadMessage(`Error uploading file: ${error.message}`);
      }
    }
  };

  return (
    <div>
      <h2>File Upload</h2>
      <form onSubmit={handleUpload}>
        <input type="file" onChange={handleFileChange} required />
        <button type="submit">Upload</button>
      </form>
      {uploadMessage && <p>{uploadMessage}</p>}
    </div>
  );
};

export default FileUpload;
```

In this component:

- We manage the state for the selected file and the upload message.
- The `handleFileChange` function updates the state when a file is selected.
- The `handleUpload` function uploads the file to Firebase Storage and displays the download URL upon success.

#### Step 2: Update the Storage Service

Ensure your `storageService.ts` file includes the function to upload files:

```typescript
// src/storageService.ts
import { storage } from "./firebaseConfig";
import { ref, uploadBytes, getDownloadURL } from "firebase/storage";

// Function to upload a file
export const uploadFile = async (file: File): Promise<string> => {
  const storageRef = ref(storage, `uploads/${file.name}`);
  await uploadBytes(storageRef, file);
  const url = await getDownloadURL(storageRef);
  return url; // Return the download URL of the uploaded file
};
```

### Part B: Retrieving Files from Firebase Storage

#### Step 1: Create a File Retrieval Component

Create a new file named `FileList.tsx` in the `src` directory to display the uploaded files:

```typescript
// src/FileList.tsx
import React, { useEffect, useState } from "react";
import { getFileUrl } from "./storageService";

const FileList: React.FC = () => {
  const [files, setFiles] = useState<string[]>([]); // Array to store file URLs
  const [fileNames, setFileNames] = useState<string[]>([]); // Array to store file names

  useEffect(() => {
    // Fetch the list of files from your backend or a predefined list
    const fetchFiles = async () => {
      // Example file names; in a real app, you would retrieve these from your backend
      const exampleFiles = ["uploads/file1.jpg", "uploads/file2.png"];
      setFileNames(exampleFiles);

      const urls = await Promise.all(
        exampleFiles.map((fileName) => getFileUrl(fileName))
      );
      setFiles(urls);
    };

    fetchFiles();
  }, []);

  return (
    <div>
      <h2>Uploaded Files</h2>
      <ul>
        {files.map((url, index) => (
          <li key={index}>
            <a href={url} target="_blank" rel="noopener noreferrer">
              {fileNames[index]}
            </a>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default FileList;
```

In this component:

- We manage the state for file URLs and file names.
- The `fetchFiles` function retrieves a list of file names (you can retrieve this from your backend) and gets their download URLs using the `getFileUrl` function.

#### Step 2: Update the Storage Service for File Retrieval

Ensure your `storageService.ts` file includes the function to retrieve file URLs:

```typescript
// src/storageService.ts
import { storage } from "./firebaseConfig";
import { ref, getDownloadURL } from "firebase/storage";

// Function to get a download URL for a file
export const getFileUrl = async (filePath: string): Promise<string> => {
  const fileRef = ref(storage, filePath);
  const url = await getDownloadURL(fileRef);
  return url; // Return the download URL of the file
};
```

### Part C: Integrating Components in the Main Application

Integrate the `FileUpload` and `FileList` components into your main application file (e.g., `App.tsx`):

```typescript
// src/App.tsx
import React from "react";
import FileUpload from "./FileUpload";
import FileList from "./FileList";

const App: React.FC = () => {
  return (
    <div>
      <h1>Firebase Storage Example</h1>
      <FileUpload />
      <FileList />
    </div>
  );
};

export default App;
```

### Step 3: Running the Application

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

3. **Test the File Upload and Retrieval Functionality**:
   - Use the file upload form to select and upload files.
   - Verify that the uploaded files appear in the list with download links.

### Conclusion

In this lesson, we explored how to upload and retrieve files using Firebase Storage in a React TypeScript frontend. We implemented components for file uploading and displaying uploaded files, allowing users to manage their files easily.

[Next: lesson-5-1-introduction-to-cloud-functions](../module-5-cloud-functions/lesson-5-1-introduction-to-cloud-functions.md)
