# 📄 PDFviewerSync

> High-performance PDF.js document viewer engine featuring real-time Firebase collaboration, WebAssembly codec acceleration, and developer inspection tools.

![JavaScript](https://img.shields.io/badge/JavaScript-ESM-f7df1e?style=for-the-badge&logo=javascript&logoColor=black)
![WebAssembly](https://img.shields.io/badge/WebAssembly-WASM-654ff0?style=for-the-badge&logo=webassembly&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-Firestore-ffca28?style=for-the-badge&logo=firebase&logoColor=black)
![License](https://img.shields.io/badge/License-Apache_2.0-blue?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-emerald?style=for-the-badge)

---

## Description

**PDFviewerSync** is an advanced, standalone document viewer engine built upon the modern ECMAScript module architecture of Mozilla's PDF.js. It integrates real-time cloud synchronization powered by Google Firebase Firestore, enabling multiple participants to browse, scroll, and review documents in lockstep across disparate devices. 

In addition to standard viewing capabilities, PDFviewerSync incorporates native WebAssembly (WASM) decoders for complex image compression formats, sandboxed JavaScript evaluation via QuickJS, and a dedicated developer debugging suite with font inspection and canvas operator stepping. Delivered as a zero-dependency static application, it can be deployed instantly to any web server or static hosting provider.

---

## Features

- **Real-Time Viewport Synchronization**: Bidirectional synchronization of horizontal and vertical scroll positions (`scrollRatioX`, `scrollRatioY`) across connected sessions via Firebase Firestore.
- **Fingerprint-Based Collaboration Rooms**: Automatically extracts the document's unique cryptographic fingerprint (`pdfDocument.fingerprints[0]`) to create dynamic, document-scoped sync channels (`pdf_sessions/<fingerprint>`) without requiring manual room setup.
- **Feedback Loop & Jitter Suppression**: Built-in echo filtering using client session IDs, a 2-pixel deadband scroll threshold, and 50ms debounced event dispatch to eliminate scroll jitter and infinite broadcast loops.
- **Interactive Sync Toggle**: On-screen toolbar button with dynamic green/red status indicators, allowing users to toggle synchronization on or off at will with persistence in `localStorage`.
- **WebAssembly Codec Acceleration**:
  - `jbig2.wasm`: Accelerated decoding for JBIG2 bi-level monochrome images with automatic JavaScript fallback.
  - `openjpeg.wasm`: High-speed JPEG 2000 image decompression with fallback.
  - `qcms_bg.wasm`: Color management and ICC color profile transformation engine.
  - `quickjs-eval.wasm`: Sandboxed WebAssembly runtime for safe execution of PDF JavaScript and AcroForm calculations.
- **Developer Debugging Suite**:
  - `FontInspector`: Inspects embedded document fonts, CSS font family mappings, glyph tables, and text layer bounding boxes.
  - `StepperManager`: Step-by-step canvas rendering debugger that allows stepping (`s`) or continuing (`c`) through PDF graphics operators (`OPS`), inspecting arguments, and setting per-page breakpoints.
  - `Stats`: Real-time page rendering benchmarks and timing metrics.
- **Comprehensive Asset Libraries**: Bundled with complete Adobe binary character maps (`cmaps/`) for Asian and Unicode typography, standard PostScript 14 font metrics (`standard_fonts/`), and Mozilla Fluent multi-language localization bundles (`locale/`).
- **Dynamic Document Loading**: Supports loading external or hosted PDF files on demand using the `?file=<url_or_path>` query parameter.

---

## Tech Stack

- **Viewer Engine**: [PDF.js](https://github.com/mozilla/pdf.js) (ECMAScript Modules, Web Workers)
- **WebAssembly Acceleration**: [JBIG2 WASM](https://github.com/mozilla/pdf.js), [OpenJPEG WASM](https://github.com/uclouvain/openjpeg), [QCMS](https://github.com/mozilla/qcms), [QuickJS WASM](https://bellard.org/quickjs/)
- **Real-Time Backend**: [Google Cloud Firebase Firestore](https://firebase.google.com/products/firestore) (Modular SDK v10.8.0)
- **Font & Internationalization**: Adobe Binary CMaps (`.bcmap`), PostScript Type 1 Fonts (`.pfb`), Mozilla Fluent Localization (`.ftl`)
- **Styling & UI**: Custom PDF.js Viewer CSS with responsive toolbar components

---

## Getting Started

### Prerequisites

PDFviewerSync is a zero-dependency, client-side web application. It does not require Node.js compilation or package installations. However, because modern web browsers enforce security policies on Web Workers and ES Modules, **it must be served over an HTTP or HTTPS origin** (cannot be opened directly via `file://`).

### Running Locally

You can serve the directory using any static web server:

**Option 1: Using Node.js (`npx serve`)**
```bash
# Clone the repository
git clone https://github.com/AryansDevStudios/PDFviewerSync.git
cd PDFviewerSync

# Serve locally on port 3000
npx serve .
```

**Option 2: Using Python 3**
```bash
# Start Python built-in HTTP server
python -m http.server 8080
```

**Option 3: Using PHP Built-in Server**
```bash
php -S localhost:8080
```

Once running, navigate to `http://localhost:8080` (or `http://localhost:3000`) in any modern web browser.

---

## Usage

### Viewing Documents

By default, the viewer loads the embedded sample document `compressed.tracemonkey-pldi-09.pdf`. To view any other document, pass the URL or relative path via the `file` query parameter:

```
http://localhost:8080/?file=path/to/document.pdf
http://localhost:8080/?file=https://example.com/hosted-document.pdf
```

### Sync Operations

1. Open the same PDF file across two separate browser windows or devices.
2. Ensure the **Sync** button in the top-right toolbar displays a green icon.
3. Scroll through pages on one window; the second window will automatically track and scroll to the matching relative position.
4. To disengage from collaborative scrolling, click the **Sync** button (the icon turns red). Local scrolling will no longer broadcast or listen to external updates.

### Developer Debugging Tools

To access the embedded debugger tools:
1. Append `#debugger` or load `debugger.mjs` within the browser developer console.
2. Use the **Font Inspector** panel to view loaded fonts and highlight rendered text spans.
3. Use the **Stepper** to pause canvas execution at specific operator indices and single-step through complex PDF vector drawing instructions.

---

## Project Structure

```text
PDFviewerSync/
├── build/
│   ├── pdf.mjs                 # Core PDF.js library API
│   ├── pdf.sandbox.mjs         # QuickJS script evaluation sandbox
│   └── pdf.worker.mjs          # Background Web Worker for PDF rendering
├── cmaps/                      # Adobe Binary CMap character encoding files (.bcmap)
├── iccs/                       # ICC color management device profiles
├── images/                     # Toolbar icons, cursors, and UI graphic assets
├── locale/                     # Mozilla Fluent localization bundles (.ftl)
├── standard_fonts/             # Standard 14 PostScript Type 1 font files (.pfb)
├── wasm/                       # WebAssembly codecs and JavaScript fallbacks
│   ├── jbig2.wasm              # JBIG2 image decoder (WASM)
│   ├── jbig2_nowasm_fallback.js# JBIG2 pure JS fallback
│   ├── openjpeg.wasm           # JPEG 2000 image decompressor (WASM)
│   ├── openjpeg_nowasm_fallback.js # JPEG 2000 pure JS fallback
│   ├── qcms_bg.wasm            # QCMS color management (WASM)
│   ├── quickjs-eval.wasm       # QuickJS interpreter (WASM)
│   └── quickjs-eval.js         # QuickJS JS bridge
├── debugger.css                # Debugger UI styles
├── debugger.js                 # Debugger loader script
├── debugger.mjs                # FontInspector, StepperManager & Stats debugger
├── index.html                  # Main application shell with Firebase Sync client
├── viewer.css                  # PDF viewer UI stylesheets
├── viewer.mjs                  # Main PDF.js viewer application controller
├── compressed.tracemonkey-pldi-09.pdf # Default demo PDF document
└── LICENSE                     # Apache License 2.0
```

---

## Contributing

Contributions are welcome! Please adhere to the following workflow:
1. Fork the repository.
2. Create a focused topic branch (`git checkout -b feature/improved-sync`).
3. Test changes across multiple browser viewports and verify that Web Worker threads execute without CSP violations.
4. Submit a detailed Pull Request.

---

## License

This project is licensed under the **Apache License 2.0**. See the [LICENSE](LICENSE) file for complete terms. Third-party WebAssembly libraries bundled in `wasm/` are subject to their respective open-source licenses as documented within the directory (`LICENSE_JBIG2`, `LICENSE_OPENJPEG`, `LICENSE_QCMS`).
