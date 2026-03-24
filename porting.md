# Urine Tracker: Developer & Porting Guide

This guide provides instructions for previewing the app locally, porting it to Android, and extending it to a smart watch.

---

## 1. Local Preview
Since the app is a single-file SPA (`index.html`), you can preview it easily on your machine:

### Option A: Simple Browser Open
*   Right-click `index.html` and select **Open with...** -> **Google Chrome** (or any modern browser).
*   *Note:* Some browsers may restrict IndexedDB access for local files (`file://`). If you experience issues saving data, use Option B.

### Option B: Local Server (Recommended)
Using a local server ensures full IndexedDB and API support.
*   **Python (pre-installed on most systems):**
    ```bash
    # Run in the project directory
    python3 -m http.server 8000
    ```
    Open `http://localhost:8000` in your browser.
*   **Node.js (serve):**
    ```bash
    npm install -g serve
    serve .
    ```
    Open `http://localhost:3000`.

---

## 2. Porting to Android
The easiest way to port this HTML/JS app to Android is using **Capacitor**. It wraps your code in a native WebView.

### Step-by-Step Porting:
1.  **Initialize Node Project:**
    ```bash
    npm init -y
    npm install @capacitor/core @capacitor/cli
    ```
2.  **Initialize Capacitor:**
    ```bash
    npx cap init UrineTracker com.example.urinetracker --web-dir .
    ```
3.  **Add Android Platform:**
    ```bash
    npm install @capacitor/android
    npx cap add android
    ```
4.  **Sync Code:**
    ```bash
    npx cap copy
    ```
5.  **Build & Run:**
    *   Open the `/android` folder in **Android Studio**.
    *   Connect your phone and click the **Run** button.
    *   Your `index.html` will now run as a full-screen, offline Android app.

---

## 3. Creating a Watch App (Wear OS / watchOS)
A watch app is ideal for "quick-log" events (starting/stopping the timer or logging a 250ml drink).

### Strategy for Wear OS (Android):
1.  **Standalone or Companion:**
    *   **Standalone (Recommended):** Build a small Wear OS app in Android Studio (Kotlin/Compose) that replicates just the "Track" screen.
2.  **Data Synchronization:**
    *   Since the main app is private/no-cloud, you can't use a central server.
    *   Instead, use **DataLayer API** (on Android) to sync the watch logs to the phone's storage.
    *   When the phone app opens, it listens for DataLayer events and adds them to the IndexedDB.
3.  **Key Watch Features:**
    *   **Start/Stop Timer:** One big button for the urination stream.
    *   **Quick Intake:** Small "+" icons for common drink amounts (250ml, 500ml).
    *   **Tile/Complication:** Add a Tile to the watch face showing today's session count or hydration percentage.

### Quick Workflow:
*   In Android Studio, add a **Wear OS Module** to your project.
*   Create a simple UI with three buttons: `💧 Start Stream`, `🥤 +250ml`, `📈 Summary`.
*   Use `Wearable.getDataClient(context)` to push log events to the phone app.

---

## Privacy Reminder
When porting, ensure that **no analytics or cloud backup** services are enabled by default (e.g., Firebase, Google Analytics). This app's core value is its absolute, 100% on-device privacy for sensitive health data.
