# Universal Batch Converter (Serverless)

A professional-grade, privacy-focused file converter that runs entirely in the browser. It supports Images, Video, Audio, and PDF manipulation using WebAssembly and HTML5 APIs.

**Core Tech:** HTML5, CSS3 (Variables + Animations), Vanilla JS, FFmpeg.wasm, jsPDF, JSZip.

---

## 📂 Project Structure

| File | Purpose |
|:---|:---|
| **`index.html`** | The entire application (UI, Logic, and Conversion Engines) in one file. |
| **`vercel.json`** | **CRITICAL.** Configures "Cross-Origin" headers required for FFmpeg to run. |
| **`contact.html`** | A `mailto:` wrapper for support. |
| **`feedback.html`** | A specialized contact page with pre-filled subject lines. |
| **`terms.html`** | Legal disclaimer (Zero-knowledge privacy policy). |

---

## 🛠️ Code Deep Dive: How `index.html` Works

The application is monolithic (single-file) for simplicity. Here is the breakdown of logic by section:

### 1. The UI & Theming (CSS)
*   **Lines 10-23 (`:root`)**: Defines the color palette using CSS Variables (`--primary`, `--surface`).
*   **Lines 25-39 (`[data-theme="dark"]`)**: Overrides those variables when Dark Mode is active.
*   **Lines 42-63 (Animations)**: Defines `@keyframes` (`fadeInUp`, `slideInRight`) to handle entrance effects.

### 2. State Management (JS)
*   **Lines 315-320**: Defines global state variables.
    *   `queue`: An array holding the files currently waiting to be converted.
    *   `completedBlobs`: Stores the converted result data to generate the ZIP file later.
    *   `ffmpeg`: Singleton instance of the video engine (loaded only once).

### 3. The "Brain" (Event Listeners)
*   **Lines 330-368 (`fileInput.addEventListener`)**:
    1.  Filters files (checks count and size limits).
    2.  Decides the "Mode" (Batch vs Merge) based on file types.
    3.  Renders the list of files to the DOM.
*   **Lines 426-474 (`startBtn.addEventListener`)**:
    1.  Loops through the `queue`.
    2.  Routes each file to the correct Engine (`convertImage` or `convertMedia`).
    3.  Updates the UI badges (Pending -> Processing -> Done).

### 4. The Engines (The Heavy Lifting)
*   **Lines 486-498 (`convertImage`)**: Uses HTML5 Canvas. Draws the image to a hidden canvas and re-exports it as a new format (`toBlob`).
*   **Lines 500-517 (`convertMedia`)**: Uses **FFmpeg WASM**.
    1.  Writes the file to a virtual in-memory file system.
    2.  Runs a CLI command (e.g., `-i input.mov output.mp4`).
    3.  Reads the result back to a Blob.
*   **Lines 519-532 (`mergeAllToPDF`)**: Uses **jsPDF**. Loops through all images, scales them to fit A4 paper, and adds them as pages.

---

## ⚙️ Developer Customization Guide

Here is exactly what to change in `index.html` to modify specific behaviors.

### A. Changing Limits & Restrictions
*Location: Lines 311-313 (Inside `<script>`)*

```javascript
// Line 311: Change max video size (currently 75MB)
const MAX_VIDEO_SIZE = 75 * 1024 * 1024; 

// Line 312: Change max files allowed in one go
const MAX_VIDEO_COUNT = 20;
const MAX_PDF_COUNT = 15;





B. Adding New File Formats 
Location: Lines 383-403 (Inside function populateOptions)
To add a new output format (e.g., converting video to .mkv):
1. Go to Line 395 (inside the video check).
2. Add a new line:

javascript - 

} else if (mimeType.startsWith('video/')) {
    add('mp4', 'MP4 Video');
    add('mkv', 'MKV Video'); // <--- Added this
}

Note: FFmpeg supports MKV out of the box, so no engine changes are needed.


C. Tuning Performance
Location: Line 497 (Inside convertImage) - index.html
If the browser crashes on mobile devices with high-res images, lower the quality setting.

javascript- 
// Change 0.9 (High Quality) to 0.7 (Medium) or 0.5 (Low)
return await new Promise(r => canvas.toBlob(r, `image/${format}`, 0.9));


D. Changing Colors / Branding
Location: Line 11 (Inside <style> :root)

CSS- 

:root {
    --primary: #0070f3; /* Change this Hex code for main brand color */
    --bg: #fafafa;      /* Change background color */
}


E. Editing Email Addresses
Target Files: contact.html and feedback.html (Not index.html)
1. Open contact.html.
2. Search for YOUR_EMAIL@gmail.com (approx Line 15).
3. Replace with your actual email address.

{content: }



https://share.google/aimode/rIzm4rlWnueGKm78o
