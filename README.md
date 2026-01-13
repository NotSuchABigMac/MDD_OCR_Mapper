# Pocket Scanner
A lightweight, browser-based tool designed for warehouse and IT teams to instantly map external vendor codes (e.g., Serial Numbers on boxes) to internal system codes (e.g., Asset Tags or SKUs).

It runs entirely in the browser using OCR (Optical Character Recognition) and Barcode Detection, meaning no app installation is required.

🚀 Quick Start
Host the files: Upload index.html, mapping.csv, and settings.json to any web server.

Recommended: Use GitHub Pages (Settings > Pages > Branch: main).

Edit the Mapping: Open mapping.csv and add your codes (Column A = Vendor Code, Column B = Internal Code).

Open on Mobile: Navigate to the URL on a phone. The camera will auto-start.

Note: This application requires HTTPS to access the camera. GitHub Pages provides this by default.

📂 File Structure
index.html: The application core. Contains all logic, UI, and scanning engines (Tesseract.js).

mapping.csv: The database. The app downloads this on startup to know what to look for.

settings.json: The configuration file. Allows IT to control scan speed, timeouts, and defaults without touching code.

⚙️ Configuration
1. The Mapping File (mapping.csv)
This file drives the matching logic. It is a simple two-column CSV. Do not include headers.

Format: External_String_To_Find, Internal_Code_To_Return

Code snippet

VEND-SN-9921, INT-LAPTOP-001
ABC-100-XYZ,  SKU-998877
Weight: 500kg, SHIP-HEAVY-CLASS
How Matching Works: The scanner uses a "Contains" logic. If the camera sees a long string of text like "Lot: 4421 / Ref: VEND-SN-9921 (Qty 1)", it will successfully match because "VEND-SN-9921" is found inside that text.

2. The Settings File (settings.json)
Use this to deploy standard settings to all devices.

JSON

{
  "scanIntervalMs": 800,       // Time between scan attempts. Lower = faster but hotter battery.
  "successLockMs": 3000,       // How long to "freeze" the green success screen before scanning again.
  "defaultContrast": 25,       // Default OCR contrast boost (0-50).
  "minMatchScore": 0.8,        // Confidence threshold (mostly for Tesseract).
  "defaultMode": "ocr",        // "ocr" (Text) or "barcode" (QR/Barcodes).
  "cameraFacing": "environment", // "environment" (Rear) or "user" (Front).
  "debugView": false           // Set to true to show the raw black-and-white computer vision feed.
}
📱 User Interface Guide
The "Slit Scan" Zone
To improve performance and reduce false positives, the camera does not scan the entire video feed.

It only processes a narrow horizontal strip in the center of the screen (marked by the box).

Users must align the target code inside this box.

Text outside this box is ignored by the computer, even if visible on screen.

Controls
Sync CSV: Forces a re-download of mapping.csv from the server. Useful if you updated the list mid-shift.

Upload: Allows a user to load a temporary local .csv file from their device.

Stop/Start Auto: Pauses the camera (saves battery).

🔧 Troubleshooting
Camera Permission Denied

Ensure the site is served over HTTPS.

Check browser permissions (Settings > Site Settings > Camera > Allow).

Nothing Matches (OCR)

Lighting: OCR requires good contrast. Turn on the room lights.

Contrast Slider: Open "Camera Settings" and adjust Contrast.

Glare: If scanning glossy labels, tilt the phone slightly to remove reflection.

Debug Mode: Enable "Show What Computer Sees" in settings. If the text looks washed out or black, adjust lighting/contrast.

Barcode Not Working

Ensure mode is set to "Barcode/QR" in settings.

Note: Some older Android/iOS browsers do not support the native BarcodeDetector API.

🛠️ Technical Implementation
Engine: Tesseract.js v5 (WASM) for OCR.

Storage: localStorage is used to cache the mapping list. If the network goes down, the app will still work with the last downloaded list.

Deployment: Static HTML/JS. No backend required.
