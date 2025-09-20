# Enhanced Daily Workflow Planner

## Overview

Ever feel like your day just slips away, or you have a million things to do but no clear plan? This "Daily Workflow Planner" is a simple web application built to help with that. It lets you break down your day hour-by-hour, jot down what you *plan* to do, and then track what you *actually* get done. It uses Firebase for cloud storage, so your schedule and progress are saved online and always there when you need them, whether you're on your laptop or phone.

## Features

*   **Hour-by-Hour Planning Table:** Visually organize your entire day in 24 one-hour blocks.
*   **User Authentication:** Securely log in or sign up with email and password. All your data is tied to your account.
*   **Cloud-Based Data Storage:** Powered by Firebase Firestore, your daily plans and tasks are saved securely in the cloud, accessible from anywhere.
*   **Progress Tracking:** Mark tasks as "Completed" or "Lost" and see your progress for the day.
*   **Visual Progress Reports:** A real-time pie chart displays your daily, weekly, or monthly completion rate, giving you an instant overview of your productivity.
*   **Customizable Task Priorities:** Assign priorities (None, Low, Medium, High) to tasks, with visual indicators in the planning table.
*   **Quick Add Task Modal:** A fast way to add a task to a specific hour without navigating the main table.
*   **Reusable Task Templates:** Create and manage a list of frequently used tasks, then easily add them to your daily plan using a dropdown.
*   **Bullet Point Support:** Press 'Enter' in task and "What Has Been Done" textareas to automatically create new bullet points.
*   **Date Navigation:** Easily select any date to plan for the future or review past days.
*   **Dark Mode Toggle:** Switch between light and dark themes for comfortable viewing.
*   **Responsive Design:** Works well across various screen sizes, from desktop to mobile.

## Setup Instructions

To get this application running locally and connected to your own Firebase project, follow these steps carefully.

### Prerequisites

Before you start, make sure you have:

*   **Node.js:** Installed on your machine (includes `npm`). This is useful for managing Firebase tools.
*   **A Google Account:** Required to create and manage a Firebase project.
*   **VS Code (Recommended):** A great code editor for web development.
*   **Live Server VS Code Extension:** This is crucial for local development with ES Modules (`import`/`export` statements). Install it from the VS Code Extensions Marketplace.

### Firebase Project Setup

1.  **Create a Firebase Project:**
    *   Go to the [Firebase Console](https://console.firebase.google.com/).
    *   Click "Add project" or "Create a project."
    *   Follow the on-screen steps to name your project. You can choose whether to enable Google Analytics; it's not strictly necessary for this app's core functionality.
    *   Once your project is created, click the "Web" icon (`</>`) on the project overview page to add a web app.
    *   Register your app and you will be provided with a `firebaseConfig` JavaScript object. **Copy this entire object; you will need it soon.**

2.  **Enable Firebase Authentication:**
    *   In your Firebase project console, navigate to "Build" > "Authentication" in the left-hand menu.
    *   Click "Get started" and then go to the "Sign-in method" tab.
    *   Enable the "Email/Password" provider. This is how users will log in to your app.

3.  **Enable Firestore Database:**
    *   In your Firebase project console, navigate to "Build" > "Firestore Database" in the left-hand menu.
    *   Click "Create database".
    *   Choose "Start in production mode" when prompted (we'll set security rules next).
    *   Select a location for your Firestore data (choose one close to you or your users for best performance).

4.  **Set up Firestore Security Rules:**
    *   This is very important for data security! In the Firestore Database section, go to the "Rules" tab.
    *   Replace the existing rules with the following. These rules ensure that each user can only read and write their own data, and cannot access other users' information.

    ```firestore
    rules_version = '2';
    service cloud.firestore {
      match /databases/{database}/documents {
        // Allow read/write for user's own day data
        match /users/{userId}/days/{document=**} {
          allow read, write: if request.auth != null && request.auth.uid == userId;
        }

        // Allow read/write for user's own task templates
        match /users/{userId}/userTasks/{document=**} {
          allow read, write: if request.auth != null && request.auth.uid == userId;
        }

        // Optional: Deny direct access to the top-level 'users' collection itself
        // Individual user documents and subcollections are handled above.
        match /users/{userId} {
          allow read, write: if false;
        }
      }
    }
    ```
    *After pasting, click "Publish". These rules are a good starting point for user-specific data.*

### Local Application Setup

1.  **Get the Code:**
    *   Download the `index.html` and `firebase-config.js` files into a single folder on your computer.

2.  **Create `firebase-config.js`:**
    *   Ensure there is a file named `firebase-config.js` in the **same directory** as your `index.html`.
    *   Paste the `firebaseConfig` object you copied earlier from the Firebase Console into this `firebase-config.js` file, like this:

    ```javascript
    // firebase-config.js
    // This file contains your Firebase project configuration.
    // Remember: For client-side applications, these keys are publicly accessible.
    // Protect your backend data with Firebase Security Rules.
    export const firebaseConfig = {
      apiKey: "YOUR_API_KEY_HERE",
      authDomain: "YOUR_AUTH_DOMAIN_HERE",
      projectId: "YOUR_PROJECT_ID_HERE",
      storageBucket: "YOUR_STORAGE_BUCKET_HERE",
      messagingSenderId: "YOUR_MESSAGING_SENDER_ID_HERE",
      appId: "YOUR_APP_ID_HERE",
      measurementId: "YOUR_MEASUREMENT_ID_HERE" // Only if you enabled Google Analytics
    };
    ```
    **IMPORTANT:** Replace `"YOUR_API_KEY_HERE"` and all other placeholder values with the actual values you copied from your Firebase project.

### Running the Application

1.  **Open in VS Code:** Open the folder containing your `index.html` and `firebase-config.js` files in VS Code.
2.  **Start Live Server:** Right-click on `index.html` in VS Code's file explorer and select "Open with Live Server".
3.  Your browser should automatically open the application at a local address (e.g., `http://127.0.0.1:5500/index.html`). The application should now be fully functional and connected to your Firebase project.

## Usage

*   **Login/Signup:** When you first open the app, you'll be prompted to log in or create an account. This keeps your data safe and personal.
*   **Plan Your Day:** Select a date using the date picker. Then, simply type your tasks into the "Task To Be Done" text areas for each hour.
*   **Add Tasks Quickly:** Use the purple "Add Task" button in the header to quickly add a task description, select an hour, and set a priority.
*   **Use Task Templates:** The "My Tasks" button lets you add common tasks as templates. Once added, you can select them from the dropdown next to any hourly task field to quickly populate it.
*   **Set Priority:** Choose a priority (None, Low, Medium, High) from the dropdown for each task. The task field will get a colored left border to visually indicate priority.
*   **Track What's Done:** After an hour, update the "What Has Been Done" field to record your accomplishments. You can hit 'Enter' for new bullet points.
*   **Mark Status:** Use the green checkmark (Completed) or red cross (Lost) buttons to mark the status of each hour.
*   **Monitor Progress:** The pie chart and "Day Status" section on the right will update in real-time. You can change the "period selector" to see daily, weekly, or monthly progress.
*   **Reset a Day:** The "Reset Day" button will clear all tasks and statuses for the currently selected day.
*   **Dark Mode:** Click the "Dark Mode" button to toggle between light and dark themes.

## License

This project is licensed under the MIT License. See the LICENSE file (if you create one, otherwise mention here) or the terms below:
