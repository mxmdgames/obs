# DeckMap (Desktop + Mobile OBS Web Remote)

DeckMap is a single-file web application that turns any browser into a customizable control surface for OBS Studio using the OBS WebSocket API. It is designed to work on both **desktop and mobile**, with a responsive UI, persistent button mapping, and QR-based configuration.

---

## Features

* Connect to OBS via WebSocket (port `4455`)
* Control OBS actions:

  * Start/Stop Recording
  * Start/Stop Streaming
  * Save Replay Buffer
  * Scene switching
  * Audio mute toggling
* Fully customizable button grid (15 slots)
* Edit mode for assigning actions
* Persistent configuration via browser localStorage
* QR code scanning for quick connection setup
* Scene/audio discovery from OBS
* Responsive layout for desktop and mobile

---

## Requirements

* OBS Studio with WebSocket server enabled
* OBS WebSocket version 5.x (built into modern OBS)
* Same network access between client and OBS host
* A modern browser (Chrome, Edge, Firefox, Safari)

---

## OBS Setup

Before connecting:

1. Open OBS Studio
2. Navigate to:

   ```
   Tools → WebSocket Server Settings
   ```
3. Enable the WebSocket server
4. Ensure the port is:

   ```
   4455
   ```
5. **Disable authentication (no password)**

### Why disable password

* DeckMap connects using the OBS WebSocket protocol
* Password authentication requires SHA-256 cryptographic hashing
* Some browser environments (especially mobile over HTTP) do not support required crypto APIs
* Disabling authentication ensures:

  * Reliable connections
  * No handshake failures
  * Cross-device compatibility

---

## Running the Application

This project is a static HTML file.

### Option 1 — Local file

Open directly in a browser:

```
DeckMap.html
```

### Option 2 — GitHub Pages (recommended for desktop/mobile)

1. Upload the repository to GitHub
2. Enable GitHub Pages:

   ```
   Settings → Pages → Deploy from branch
   ```
3. Select the root or `/docs` folder
4. Access via:

   ```
   https://<username>.github.io/<repo>/
   ```

---

## Initial Connection

1. Open the page in your browser
2. Enter:

   * OBS host IP (e.g. `192.168.x.x`)
   * Port (`4455`)
   * Password (leave empty if disabled)
3. Click **Connect**

Alternatively:

* Use **📷 Scan QR** to scan a QR code containing connection details

---

## UI Overview

### Connection Panel

* Host: OBS machine IP or hostname
* Port: WebSocket port (default `4455`)
* Password: Optional (should be empty for best compatibility)
* Connect button establishes WebSocket connection
* Status indicator shows connection state

---

### Toolbar

* **Edit Mode**: Enables assigning actions to buttons
* **Clear All**: Removes all button mappings
* **Save Config**: Saves layout and connection settings
* **Load Config**: Restores saved configuration

---

### Deck Grid

* 15 configurable buttons arranged in a 5×3 grid
* Each button can be assigned:

  * OBS scenes
  * Audio inputs (mute toggle)
  * Built-in OBS commands

---

## Edit Mode Workflow

1. Click **✏️ Edit Mode**
2. Tap any empty or existing button
3. A modal opens showing available actions:

   * Scenes (fetched from OBS)
   * Audio inputs (for mute toggling)
   * Static OBS commands
4. Select an action
5. The button is assigned and saved automatically

---

## Button Actions

Each button triggers a WebSocket request to OBS using the RPC interface.

Examples:

* Scene switch:

  ```
  SetCurrentProgramScene
  ```

* Start recording:

  ```
  StartRecord
  ```

* Toggle mute:

  ```
  ToggleInputMute
  ```

* Start stream:

  ```
  StartStream
  ```

DeckMap dynamically builds requests and sends them using the OBS WebSocket protocol.

---

## How Communication Works

### 1. WebSocket Connection

The client connects to OBS using:

```
ws://<host>:4455
```

---

### 2. Handshake

OBS sends a `Hello` message indicating whether authentication is required.

* If no password is set:

  * The client sends an `Identify` message directly
* If a password is set:

  * The client computes a SHA-256 authentication response

---

### 3. Request/Response Model

DeckMap uses OBS RPC messages:

* Requests → `op: 6`
* Responses → `op: 7`

Each request includes a unique request ID so responses can be matched asynchronously.

---

### 4. Real-Time Updates

Once connected, OBS can send:

* Scene changes
* Source updates
* Event notifications

DeckMap listens for these messages and updates UI state accordingly.

---

## QR Code Support

DeckMap supports QR scanning for quick configuration.

### Supported formats

1. JSON:

```json
{
  "host": "192.168.1.10",
  "port": "4455",
  "password": ""
}
```

2. Simple string:

```
192.168.1.10:4455
```

3. With password:

```
192.168.1.10:4455:password
```

---

## Data Persistence

DeckMap uses `localStorage` to store:

* Button layout
* Assigned actions
* Connection settings

This allows:

* Reload persistence
* Offline layout retention
* Quick reconnection

---

## Desktop vs Mobile Behavior

### Desktop

* Mouse-driven interaction
* Stable crypto APIs
* Password authentication works if HTTPS is used

### Mobile

* Touch-based interaction
* QR scanning supported
* May require no password for reliability on HTTP
* Responsive grid adapts to screen orientation

---

## Limitations

* Runs over HTTP (no encryption unless hosted with HTTPS)
* Password authentication may fail on some browsers without HTTPS
* Requires OBS WebSocket to be enabled
* Requires same LAN connectivity
* No external cloud sync

---

## Troubleshooting

### Cannot connect

* Verify OBS WebSocket is enabled
* Confirm correct IP address
* Ensure same network (no guest WiFi isolation)
* Check firewall rules for port `4455`

---

### Buttons do nothing

* Ensure connection status shows “Connected”
* Reconnect if WebSocket dropped
* Verify OBS is running

---

### Scenes not showing

* Ensure OBS is connected before assigning buttons
* Scenes are fetched dynamically after connection

---

### QR scan not working

* Allow camera permissions
* Use a supported browser
* Ensure adequate lighting

---

## Security Notes

* No encryption when using HTTP
* No authentication if OBS password is disabled
* Intended for trusted local networks only

---

## Summary

DeckMap functions as a lightweight OBS control surface by:

* Running entirely in the browser
* Using WebSocket RPC to communicate with OBS
* Dynamically querying OBS for scenes and inputs
* Persisting configuration locally
* Providing QR-based configuration for quick setup

It effectively turns any device into a customizable Stream Deck replacement without requiring additional software installations.
