# 🚀 Direct NVR Viewer: PM2 Background Setup

Use these instructions to run the Direct NVR Viewer 24/7 in the background on your server.

---

## 1. Prerequisites
*   Node.js installed on your server.
*   PM2 installed globally: `npm install -g pm2`

## 2. Initial Setup
Run these commands in the project root folder:
```bash
# Install all dependencies (including new AI features)
npm install

# Start the background process
pm2 start server.js --name direct-nvr-viewer
```

## 3. Configure Gemini AI (Optional)
To enable Tactical AI Briefs and Semantic Search:
1.  Create a file named `.env` in this folder.
2.  Add your key: `GEMINI_API_KEY=your_google_ai_studio_key`
3.  Restart the process: `pm2 restart direct-nvr-viewer`

## 4. Maintenance Commands
*   **Check Status:** `pm2 status`
*   **View Live Logs:** `pm2 logs direct-nvr-viewer`
*   **Restart Server:** `pm2 restart direct-nvr-viewer`
*   **Stop Server:** `pm2 stop direct-nvr-viewer`

---
**Note:** This project runs on port **3010** by default.
