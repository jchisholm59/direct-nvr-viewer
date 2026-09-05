# Direct NVR Smart Monitor & Alert Engine

> 📱 **NEW: Native iOS & Android Mobile Apps are now supported!** Check out the [Mobile App Setup & Usage Guide](MOBILE_APP_GUIDE.md) to get the app running on your phone.

A lightweight, ultra-high-performance web application designed to run seamlessly on top of your existing **Frigate** and **go2rtc** NVR stack. It delivers low-latency, non-proxied live video streams straight to your browser, enables you to draw interactive polygonal scanning windows (Regions of Interest) and exclusion zones on each feed, and integrates with **Frigate's Edge Coral TPU over MQTT** to trigger instant email notifications, save event clips, and maintain a real-time smart dashboard with **0% local CPU decoding or local AI inference load**.

---

## 🚀 Key Features

- **Edge Coral TPU Integration via MQTT:** Completely eliminates local CPU decoding and local TensorFlow.js inference. The backend hooks into your active MQTT broker, subscribes to the `frigate/events` topic, and processes high-accuracy detections streamed in real-time from your Frigate Edge TPU.
- **Interactive Scanning & Exclusion Zones:**
  - **Yellow Scan Windows (ROIs):** Draw custom polygonal regions of interest.Detections are ignored unless they cross into these active target scanning areas.
  - **Red Ignore Zones (Exclusion Masks):** Draw exclusion polygons over areas prone to false alerts (e.g., wind-blown bushes, swaying trees, public walkways). Centroids entering these zones are instantly discarded.
- **Apple Safari/macOS/iOS Compatibility Patches:** When H.265 (HEVC) streams record clips, Frigate packages them as standard `hev1` segments which Apple's native media frameworks reject. Our backend automatically intercepts downloaded event clips and binary-patches them in-memory to Apple-compatible `hvc1` containers with **zero CPU transcoding overhead**, making clips instantly playable in macOS, iOS, and Safari!
- **Multi-Server Configuration Profiles:** Manage multiple locations (e.g., "Home Server" and "Cottage Server") with ease. Swap profiles with a single dropdown in the header; the backend will hot-reload camera feeds, disconnect from the old MQTT broker, and seamlessly boot up the new MQTT client.
- **Persistent Handshake Event History:** Events and linked video playback locations are saved to disk (`alerts_history.json`). Your event log sidebar fully survives browser refreshes and server reboots, complete with WebSocket-synchronized clear features across all connected monitors.
- **Smart SMTP Global Toggle:** Turn Gmail SMTP email notifications on or off instantly with a checkbox in the header of the SMTP section—no need to delete or re-type your app password.
- **Direct low-latency WebRTC/MJPEG streaming:** Embeds direct go2rtc streams for real-time live feeds with zero video buffering latency.

---

## 🛠️ Installation & Setup

### 1. Prerequisites
- **Node.js** (v18 or higher recommended)
- **Frigate NVR** with an active Edge Coral TPU running on your network (usually auto-discovered or configured via settings).
- **An active MQTT Broker** used by Frigate (to publish events on the `frigate/events` topic).

### 2. Startup
Navigate to the directory and run:

```bash
cd /path/to/your/direct-nvr-viewer
npm install
npm start
```

Open your browser and visit:
👉 **[http://localhost:3010](http://localhost:3010)**

To stop the server easily at any time, run:
```bash
npm stop
```

---

## 📐 Dynamic Frigate Integration (MQTT & API)

This version connects directly to your Frigate server's APIs and event streams:

1. **Auto-Discovery & Fallbacks:**
   On boot, the backend probes candidate IPs to locate your active Frigate server. It dynamically fetches Frigate's parsed JSON configuration from the `/api/config` endpoint, resolving camera resolutions, restream configurations, and automatically parsing your active **MQTT broker IP, username, and password**.
2. **The MQTT Event Pipeline:**
   Instead of grabbing and processing raw JPEGs, the server subscribes to the `frigate/events` topic on your MQTT broker. 
   When Frigate's Edge Coral TPU identifies an object (like a person or car), it publishes a live event payload:
   - When an event is initiated (`type: "new"`), our server maps the bounding box centroid against your custom yellow Scan Windows and red Ignore Zones. If it crosses your criteria, it plays an audio chime, logs it in the persistent sidebar history, and dispatches a Gmail notification.
   - When the event terminates (`type: "end"`), the backend fetches the complete MP4 video segment from Frigate's HTTP event API, binary-patches the HEVC container headers to `hvc1` for iOS/macOS compatibility, saves it to disk, and updates the historical alert log with an instant "Play Clip" button.

---

## ⚙️ Configuration & Alerts

Click the **Settings & SMTP** button in the header of the web page to open the configuration panel.

### 1. Server Profile Management
* **Profile Selection:** Switch active server profiles directly from the selector dropdown in the header of the app.
* **New Server Profile:** Create a cloned baseline profile, input its distinct Frigate and MQTT coordinates, and save it under a unique name (e.g. "Cottage Server").
* **Delete Profile:** Safely remove older or inactive profiles with automatic safe fallback routing.

### 2. Gmail SMTP Setup
To send alerts via `smtp.gmail.com` with direct camera snapshots attached:
1. Go to your [Google Account Settings](https://myaccount.google.com/).
2. Enable **2-Step Verification**.
3. Search for **App passwords** in the search bar.
4. Generate a new App Password (e.g., name it "Smart NVR Monitor").
5. Copy the 16-character password (e.g., `xxxx xxxx xxxx xxxx`).
6. Paste this app password into the **Gmail App Password** field in the Settings panel (without spaces).
7. Toggle the **Enable email alerts** checkbox on or off as needed, and click **Save**.

### 3. Tracked Classes
Check the checkboxes for any objects you want to alert on (such as `person`, `car`, `cat`, `dog`, `bear`, `bird`). Only classes ticked here will trigger logs and alerts.
