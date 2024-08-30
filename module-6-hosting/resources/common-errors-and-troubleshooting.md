# Common Errors and Troubleshooting in Firebase Hosting

When deploying applications to Firebase Hosting, you may encounter various errors. Understanding these errors can help you troubleshoot issues effectively.

## Common Errors

1. **Deployment Failed**

   - **Description**: The deployment process encountered an error and failed to complete.
   - **Solution**: Check the terminal output for specific error messages. Ensure that your `firebase.json` file is correctly configured and that all required files are present.

2. **404 Not Found**

   - **Description**: The requested resource could not be found.
   - **Solution**: Verify that the URL is correct and that the requested file exists in the specified public directory. Check your redirects and rewrites in `firebase.json`.

3. **Permission Denied**

   - **Description**: The user does not have permission to access the requested resource.
   - **Solution**: Ensure that your Firebase project settings and security rules are configured correctly. Check that the Firebase CLI is authenticated with the appropriate permissions.

4. **SSL Certificate Issues**
   - **Description**: There are issues with the SSL certificate for your custom domain.
   - **Solution**: Ensure that your custom domain is correctly set up in the Firebase Console. It may take some time for the SSL certificate to be provisioned.

## Troubleshooting Steps

- **Check Firebase Console**: Use the Firebase Console to view logs and deployment history. This can help identify where errors are occurring.
- **Review Documentation**: Refer to the [Firebase Hosting Documentation](https://firebase.google.com/docs/hosting) for guidance on common issues and solutions.
- **Test Locally**: Use the Firebase Emulator Suite to test your application locally before deploying.

For more information on error codes, refer to the [Firebase Hosting Error Codes Documentation](https://firebase.google.com/docs/hosting/quickstart#troubleshooting).
