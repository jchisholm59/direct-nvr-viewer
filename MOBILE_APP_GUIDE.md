# 📱 Direct NVR - Mobile App & GitHub Guide

Welcome to your mobile-enabled Direct NVR project! This guide is written specifically to help you upload your code to GitHub (even if you're completely new to Git) and get your native iOS and Android apps running on your devices.

---

## 🚀 Part 1: How to Upload Your Code to GitHub (For Beginners)

Since your local files have been modified and committed locally, we need to push them to your online GitHub repository (`your-github-username/direct-nvr-viewer`).

Because GitHub no longer accepts normal account passwords for security, you must authenticate. Here are the two easiest ways to push your changes:

### Option A: Push via the Terminal (Using a Personal Access Token)
If you have a working Personal Access Token (PAT) with **Repo / Write** access, follow these steps:

1. Open your Mac **Terminal** app.
2. Navigate to your project folder:
   ```bash
   cd /path/to/your/direct-nvr-viewer
   ```
3. Run the push command:
   ```bash
   git push -u origin main
   ```
4. When the Terminal asks for your **Username**, type: `your-github-username`
5. When the Terminal asks for your **Password**, **DO NOT paste your normal password**. Instead, paste your active **GitHub Personal Access Token (PAT)**, then press Enter. *(Note: The terminal won't show characters while pasting/typing a password; this is normal—just paste and press Enter!)*

### Option B: Using GitHub Desktop (Super Easy & Visual)
If you prefer a visual interface, you can use the official free **GitHub Desktop** app:
1. Download and install [GitHub Desktop](https://desktop.github.com/).
2. Open GitHub Desktop and log in with your GitHub credentials.
3. Click on **File** (in the top menu bar) -> **Add Local Repository...**
4. Browse to and select: `/path/to/your/direct-nvr-viewer`
5. GitHub Desktop will instantly recognize your repository and show that your local branch is ahead of the online branch.
6. Click the **"Publish branch"** or **"Push origin"** button at the top right to safely upload everything to GitHub!

---

## 📱 Part 2: How to Set Up and Use Your Mobile App

Your mobile app is built using **Capacitor**, which wraps your high-performance web dashboard into a native mobile framework.

### 1. Start Your NVR Backend Server
The mobile apps are client-side "players." They need your Node backend server to be running on your home network to function.
On your Mac/Server, start the backend:
```bash
cd /path/to/your/direct-nvr-viewer
npm start
```
*(Take note of your server's IP address on your local network, e.g., `http://192.168.1.100:3010`, or your Tailscale IP).*

---

### 2. How to Run the iOS App (For iPhone/iPad)

To run the iOS version of your app:
1. Ensure you have **Xcode** installed (available free on the Mac App Store).
2. Open the iOS project from the Terminal:
   ```bash
   npx cap open ios
   ```
   *(This opens Xcode automatically).*
3. In the left-side panel of Xcode, click on the **App** project (the blue folder icon at the top of the list).
4. Go to the **Signing & Capabilities** tab in the main window.
5. Under **Team**, select your Apple account (or click "Add Account" to sign in with your Apple ID). This is required so Apple lets you install apps on your personal devices.
6. Connect your iPhone to your Mac via USB.
7. In the top bar of Xcode, select your connected iPhone as the build target (instead of "Any iOS Device").
8. Click the **Play button (triangle)** at the top left to compile and install the app!
9. *Note: If this is your first time installing a custom app, go to your iPhone's **Settings -> General -> VPN & Device Management**, tap your developer certificate, and select **Trust**.*

---

### 3. How to Run the Android App

To run the Android version of your app:
1. Ensure you have [Android Studio](https://developer.android.com/studio) installed.
2. Open the Android project from the Terminal:
   ```bash
   npx cap open android
   ```
   *(This opens Android Studio automatically).*
3. Connect your Android phone to your Mac via USB and make sure **USB Debugging** is enabled in your phone's Developer Options.
4. In Android Studio, wait for the project to finish loading (a green checkmark or progress bar at the bottom will complete).
5. In the top toolbar, select your physical Android device from the device dropdown.
6. Click the green **Run (triangle)** icon to compile and launch the app on your phone!

---

### 4. Setting up the App on First Launch
When you open the app on your mobile device for the first time:
1. You will be greeted by a **Connect to Backend Server** overlay screen.
2. Enter your server's backend URL. 
   - E.g., if your server is running on Tailscale: `http://100.x.x.x:3010`
   - E.g., if you are on the same local Wi-Fi: `http://192.168.1.100:3010`
3. Click **Connect & Initialize**.
4. The app will connect to your server, fetch all camera feeds, and start showing low-latency real-time video instantly!

*If you ever need to change this URL later, click **Settings & SMTP** in the header, look under **App Connection Settings**, enter a new URL, and save.*

---

## 🔄 Part 3: How to Sync Future Changes

If you ever edit files inside the `public/` directory (like updating `app.js` or `index.html`) and want to update the mobile apps:

1. Save your changes to the files.
2. In the terminal, run:
   ```bash
   npx cap sync
   ```
3. Re-run your app in Xcode or Android Studio to apply the updates!
