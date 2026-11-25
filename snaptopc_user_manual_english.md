# SnapToPC User Manual

**SnapToPC** is a PWA web app that uses WebRTC (P2P) technology to directly transfer and save videos and photos captured or selected on multiple smartphones to a nearby laptop (PC) without storing them on any external server. It works seamlessly on the same network (Wi-Fi) as well as over **external networks (5G/LTE)** using TURN settings, and supports switching between Korean and English via the **`EN / KO`** toggle button in the top navigation bar. Even if the network or laptop connection drops, files are safely cached on the smartphone and automatically transmitted once reconnected. You can also choose to keep a local copy on your smartphone.

## 1. Before You Begin

1. **Language Toggle (`EN / KO`)**
   * Click the **`EN`** or **`KO`** button at the top right of the header to switch the interface between English and Korean at any time.

2. **Network Connection (Wi-Fi & 5G/LTE External Networks)**
   * Both the laptop and smartphone must be **connected to the internet** (used for room code matching and P2P signaling).
   * Connecting to the same Wi-Fi router or hotspot enables fast, direct local routing.
   * If the smartphone is on **5G/LTE mobile data** or on a different network, enable **`External (TURN)`** at the top of the laptop screen and apply your TURN server credentials.

3. **Access URL (HTTPS Recommended)**
   * To use the camera in a mobile browser, you must access the app via `https://` (or `localhost`).
   * If running in a local development environment (e.g., `127.0.0.1`), you can specify an accessible address (such as `https://munjojung.github.io/snaptopc/` or your local IP address) in the QR URL field on the left panel of the laptop screen.

4. **Install as an App (PWA)**
   * Click the **`Install App`** button at the top right to see how to add SnapToPC to your home screen on Android, iPhone, or PC.

## 2. Laptop / PC (Receive Mode)

Use the laptop where you want to collect and save videos and photos.

1. On the initial screen, click the **`Laptop / PC (Receive)`** card (`Open Receive Room`).
2. The default connection code `Z9PZT` and a **QR code** will appear on the left, and the **`Connection Status`** will update to waiting/ready.
   * **Fixed & Custom Codes:** Your PC connection code is saved and maintained as `Z9PZT` by default. You can click **`Change`** to enter a custom code or generate a random code, and click **`Reset Z9PZT`** at any time to revert to the default.
3. **External Network (TURN) Setup (For 5G/LTE Access)**
   * Click the **`External (TURN)`** button at the top, toggle the switch ON, enter the Server URL (separate multiple URLs with a comma `,`), Username, and Credential, then save and apply.
   * When the indicator dot next to the button turns **green**, TURN is active. If your credentials expire (`ttl`), please generate and enter new ones.
4. **QR Code Scanning Tip**
   * If the QR code appears dense and your phone camera has trouble focusing, hold the camera slightly closer to the screen for a clearer scan.
5. **Auto-Save Setting**
   * When **`Auto-save on completion`** is enabled (default), received media is automatically saved to your laptop's default Downloads folder as soon as the transfer finishes.
6. **Check Connected Devices**
   * In the **`Connected Devices`** section at the bottom left, you can monitor the number of connected smartphones and their device names in real time.
7. **View & Save Received Media**
   * Files currently being transferred display real-time progress (%) at the top.
   * Completed videos and photos appear in **`Received Media`** on the right. Use the **device filter tabs** at the top to view items from a specific device.
   * Click the save button on individual cards or **`Save All`** at the top right to download everything at once.
   * **File Naming Convention:** Files are automatically named as `[DeviceName]_[YYYYMMDD_HHmmss]_[Sequence].[ext]` (e.g., `Alex_Phone_20261124_120000_001.webm` or `.jpg`) to prevent overwriting when multiple users send files.

## 3. Smartphone (Capture & Send Mode)

Use one or more smartphones to capture and send videos and photos.

### 3.1 Connecting to the Laptop (Wi-Fi & 5G/LTE)

* **5G/LTE Pre-check:** No extra registration is needed on the smartphone. Just ensure **`External (TURN)`** is active (green) with valid credentials on the laptop.
* **Method A (Link or QR Code Scan - Recommended):** Open the shared `?room=Z9PZT` link or scan the QR code on the laptop screen using your phone's default camera. The connection code will be filled in automatically, and it will attempt to connect.
* **Method B (Manual Entry):**
  1. Select **`Smartphone (Capture & Send)`** (`Start Camera & Send`) on the initial screen.
  2. Enter an identifiable name in **`My Device Name`**, e.g., `Alex-Phone`.
  3. Enter the laptop's connection code (default `Z9PZT`) in **`Laptop Code`** and tap **`Connect`**.
  4. Once **`Laptop Connection`** shows connected, you are ready to send.
* **Switching from 5G to Wi-Fi:** If a saved Wi-Fi network is detected while on 5G, your phone's OS will prioritize Wi-Fi by default. If the connection drops briefly during a network switch, your files remain safely in the queue. Once switched, tap **`Connect`** again to resume sending.

### 3.2 Recording Video, Taking Photos, Gallery Transfer & Local Copy

* **Switch Camera:** Tap the rotate icon at the top right of the preview screen to switch between front and rear cameras.
* **Record Video:** Tap **`Record`** to begin, and tap stop to finish and immediately start transferring.
* **Take Photo:** Tap **`Take Photo`** to capture the current camera preview as a `JPEG` image and send it immediately (or queue it).
* **Send Existing Media:** Tap **`Gallery`** to choose one or more existing video or photo files from your phone.
* **Save to My Device Option:**
  * Enable the **`Save to my device`** toggle to also save a copy to your phone's default Downloads folder when recording, taking photos, or selecting from the gallery.
  * When disabled, files are only temporarily stored in the transfer queue (IndexedDB) and automatically deleted after the laptop confirms receipt.

## 4. Offline Caching & Automatic Retransmission

You can continue recording videos and taking photos even when **disconnected from the laptop** due to movement or unstable networks.

1. When offline, any recorded video, captured photo, or selected gallery file is safely stored in your mobile browser's internal storage (IndexedDB) under **`Transfer Queue (Offline Cache)`**.
2. Queued items persist even if you refresh or close the app.
3. Once reconnected to the laptop, queued items are **automatically sent in order** and removed from the queue after the laptop confirms receipt.
4. You can also tap **`Send Now`** to manually trigger the queue or delete individual items to remove them.

## 5. FAQ (Troubleshooting)

* **Q. The camera screen is black and doesn't work.**
  * Check that camera and microphone permissions are allowed for your browser. Mobile browsers also require `https://` (not `http://`) to grant camera access.
* **Q. On 5G (external network), it gets stuck connecting and then disconnects.**
  * Verify that **`External (TURN)`** on the laptop is green, the TURN URL/Username/Credential are entered correctly, and the credentials' validity period (`ttl`) has not expired.
* **Q. I entered the connection code, but it doesn't connect.**
  * Ensure both the laptop and smartphone are connected to the internet, and verify that the code matches the one displayed on the laptop screen (default `Z9PZT`).
* **Q. The QR code is hard to scan.**
  * Move your phone camera slightly closer to the QR code on the screen so it can focus clearly.
* **Q. Transfer pauses on iPhone (Safari).**
  * Due to iOS browser restrictions, putting the app in the background or turning off the screen may pause transfers. Please keep the screen awake while transferring.

## 6. Ending a Session & Exiting

There is no separate exit button. Simply finish your active tasks and close the tab or return to the home screen.

1. **Smartphone (Capture & Send Mode)**
   * If recording, stop recording and ensure any ongoing transfer or queue save is complete.
   * Close the **browser tab or installed PWA app**.
2. **Laptop / PC (Receive Mode)**
   * Confirm all received videos and photos are saved, then **close the browser tab or window**.
   * To return to the mode selection screen, click the **SnapToPC logo** at the top left.
3. **Local Server & Tunneling Tools (If Used)**
   * If you ran a local server or tunneling tool (`cloudflared`, `ngrok`, etc.), stop it in its terminal (`Ctrl + C`).
4. **Managing Queue & Browser Data**
   * Any unsent items in **`Transfer Queue (Offline Cache)`** remain saved even after closing the tab, so you can reconnect later to resume sending.
   * To clear queued items, delete each item or clear your browser's site data.

## 7. Quick Share URL & Message Template

* **Default Code (`Z9PZT`) URL:** `https://munjojung.github.io/snaptopc/?room=Z9PZT`
* **Message Template for Sharing:**

  > Please open the link below:
  > https://munjojung.github.io/snaptopc/?room=Z9PZT
  >
  > 1. Open the link and allow camera permissions.
  > 2. Tap 'Record', 'Take Photo', or 'Gallery' to send videos/photos directly to my PC.
  >
  > *(Note: For 5G/external network access, 'External (TURN)' must be enabled on the PC. If you changed the room code, update `?room=YOUR_CODE` accordingly.)*