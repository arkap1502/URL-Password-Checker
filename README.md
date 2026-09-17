# Sentinel AI — Advanced URL Threat Scanner

Sentinel AI is a browser-based security analysis tool that evaluates URLs using a deterministic, client-side heuristic engine. It is designed as a lightweight teaching and triage tool for identifying suspicious URL patterns before a user visits a page. It also includes a password-strength analyzer and a live QR-code scanner that feeds decoded URLs straight into the threat engine.

> **Important:** Sentinel AI performs URL-structure analysis only. It does not fetch or execute the target webpage, does not use a live machine-learning model, and is not a replacement for maintained threat-intelligence services.

## Features

### URL Threat Scanner
- Analyze a URL directly in the browser.
- Generates a **0–100 risk score**.
- Provides a clear verdict:
  - **HIGH RISK — likely phishing**
  - **SUSPICIOUS — proceed with caution**
  - **LOW RISK — minor red flags**
  - **LIKELY SAFE — no significant indicators**
- Shows individual findings with severity and weighted points.
- Displays category-level analysis.
- Generates a deterministic natural-language security summary.
- Reports a confidence level based on the number and severity of triggered signals.
- Keeps a local scan history of up to six recent URLs.
- Allows the generated report to be copied to the clipboard.

### Detection Models

The URL engine evaluates four major categories:

1. **Domain Reputation**
   - Known-brand impersonation patterns
   - Suspicious top-level domains
   - URL shorteners
   - Other domain-related indicators

2. **Structural Analysis**
   - Raw IP addresses
   - Excessive subdomains
   - Suspicious query parameters
   - Open-redirect patterns
   - Non-standard ports
   - Other unusual URL structures

3. **Lexical / Content Signals**
   - Credential and account-related keywords
   - Login, verification, banking, password, billing, recovery and similar terms
   - Dangerous file extensions
   - Other phishing-oriented wording

4. **Encoding / Obfuscation**
   - Punycode / IDN homograph indicators
   - Non-ASCII domains
   - Base64-like or encoded parameters
   - Double URL encoding
   - Other obfuscation patterns

The implementation contains reference lists for known brands, suspicious TLDs, URL shorteners, suspicious keywords, dangerous file extensions, and redirect parameters.

## Password Strength Analyzer

Sentinel AI also includes a password-strength analysis section.

It evaluates four password categories:

- **Length**
- **Composition**
- **Predictability**
- **Exposure Risk**

The password analyzer:
- Produces a strength score.
- Shows a verdict such as weak, average, or strong.
- Displays triggered signals and their severity.
- Generates a deterministic explanatory summary.
- Shows a confidence indicator.
- Masks the password in the displayed report.
- Provides show/hide and clear controls.

The password value is processed locally in the browser and is not persisted, logged, or sent to a server.

## Live QR Scanner

Sentinel AI includes a live QR-code scanning section.

It supports two input modes, both decoded 100% in the browser:

- **Live camera scan** — point the device camera at a QR code; decoding runs in the tab via the `html5-qrcode` library.
- **Image upload fallback** — upload a photo/screenshot of a QR code (useful on desktops with no camera, or when the page is opened via `file://` and the browser blocks camera access).

The QR scanner:

- Shows a live camera viewport with start/stop controls.
- Lists available cameras in a switcher dropdown when more than one is detected.
- Displays decode status and friendly errors (permission denied, no camera found, camera busy, no QR in image).
- Shows the decoded text with a **URL detected** / **Text** badge.
- Offers a **Scan URL →** button when the decoded content looks like a URL, which fills the URL bar and runs the full threat-engine analysis.
- Provides copy and clear controls.
- Auto-stops the camera when the tab is hidden.

## Privacy

Sentinel AI is designed around client-side processing.

- No URL is sent to a backend by the scanner.
- URL checks run inside the browser tab.
- Password analysis also runs locally.
- QR decoding (camera and image upload) also runs locally; camera frames never leave the tab.
- No live page content is fetched by the URL scanner.
- Scan history exists only in the current page session.
- The generated report can be copied manually by the user.

## How It Works

```text
User enters URL
       │
       ▼
URL parsing
       │
       ▼
┌──────────────────────────────┐
│  Domain Reputation Model     │
│  Structural Model            │
│  Lexical / Content Model     │
│  Encoding Model              │
└──────────────────────────────┘
       │
       ▼
Weighted threat signals
       │
       ▼
Risk score (0–100)
       │
       ├── Verdict
       ├── Confidence
       ├── Category breakdown
       └── Detailed findings
```

The score is calculated by adding the weighted points from triggered rules and capping the final result at 100.

```text
QR code (camera or image)
        │
        ▼
In-browser QR decode (html5-qrcode)
        │
        ▼
Decoded text ── URL? ──yes──▶ URL Threat Scanner (flow above)
        │
        no
        ▼
   Display as text
```

## Example Signals

The scanner can identify patterns such as:

- Punycode domains (`xn--`)
- Possible brand impersonation
- Suspicious TLDs
- URL shorteners
- Login or verification language
- Dangerous file extensions
- Open redirects
- Excessive query parameters
- Base64-like encoded parameters
- Double URL encoding
- Non-standard ports
- Raw or obfuscated IP addresses

## Interface

The application uses a dark cybersecurity/HUD-style interface with:

- Responsive desktop, tablet and mobile layouts
- Animated scan indicator
- Risk score gauge
- Severity badges
- Category breakdown
- AI-style summary panel
- Scan history
- Copy-report functionality
- Example URLs for testing
- Password visibility controls
- Live QR camera viewport with start/stop and camera switcher
- QR image-upload fallback with status messages
- QR-to-URL handoff (`Scan URL →`) into the threat engine

## Technology Stack

### Frontend
- HTML5
- CSS3
- JavaScript
- SVG
- Browser Clipboard API
- Browser Media APIs (`getUserMedia` for the QR camera)
- Google Fonts:
  - Inter
  - JetBrains Mono

### Third-party CDN
- `html5-qrcode` (via unpkg) — in-browser QR decoding for the live camera and image-upload scanner. Requires internet access; all decoding still happens locally.

### Architecture
- Single-page client-side application
- No required backend
- No database required
- No external API required for the built-in heuristic analysis

## Project Structure

```text
Sentinel-AI/
│
├── index.html
└── README.md
```

The current implementation is contained in a single HTML file with embedded CSS and JavaScript.

## Installation

No package installation is required.

### 1. Clone or download the project

```bash
git clone <your-repository-url>
cd Sentinel-AI
```

### 2. Open the application

You can simply open:

```text
index.html
```

in a modern browser.

For VS Code, you can also use the **Live Server** extension and open `index.html` through the local development server.

> **Camera note:** browsers only allow camera access in secure contexts (HTTPS or `localhost`). If you open `index.html` directly via `file://` and the camera fails, run it through Live Server (or any `localhost` server) and allow the permission prompt. The QR image-upload option works without a camera. Internet access is needed once to load the `html5-qrcode` CDN script.

## Usage

### Scan a URL

1. Open Sentinel AI.
2. Enter a URL such as:

```text
https://example.com/login
```

3. Click **SCAN**.
4. Review the:
   - Risk score
   - Verdict
   - Confidence
   - Category breakdown
   - Individual findings
   - Security summary

### Test with examples

The interface includes example URLs for testing different detection patterns, including:

- A normal domain
- A typosquatting-style domain
- A raw-IP URL
- A Punycode domain
- A URL shortener

### Check a Password

1. Enter a password in the password analyzer.
2. Review the score and verdict.
3. Inspect the detected weaknesses.
4. Use **SHOW/HIDE** if needed.
5. Clear the field when finished.

### Scan a QR Code

1. Scroll to the **Live QR Scan** section.
2. Either:
   - Click **Start camera →**, allow the permission prompt, and point the camera at a QR code, or
   - Click **Upload image** and choose a photo/screenshot of a QR code.
3. Review the decoded text and its **URL detected** / **Text** badge.
4. If it is a URL, click **Scan URL →** to run it through the URL threat scanner.

## Risk Scoring

The URL scanner uses weighted heuristic signals.

| Score | Verdict |
|---:|---|
| 0–14 | Likely Safe |
| 15–39 | Low Risk |
| 40–69 | Suspicious |
| 70–100 | High Risk |

A high score means more suspicious indicators were detected. It does **not** prove that a URL is malicious.

## Limitations

Sentinel AI intentionally has a limited scope.

- It does not download or inspect the target webpage.
- It cannot confirm whether the page itself is malicious.
- It does not replace browser security warnings.
- It does not use a maintained live threat-intelligence database.
- Its "AI" analysis is deterministic and template-generated.
- Heuristic results can produce false positives or false negatives.
- A low-risk result does not guarantee that a website is safe.
- A high-risk result should be treated as a warning for further investigation.
- The QR camera requires a secure context (HTTPS or `localhost`), a granted camera permission, and internet access for the `html5-qrcode` CDN; blurry or cropped QR images may fail to decode.

## Responsible Use

Use Sentinel AI for:

- Security education
- URL triage
- Phishing-awareness demonstrations
- Defensive security research
- Learning URL-analysis techniques
- Portfolio and cybersecurity demonstrations

Do not rely on the tool alone for high-stakes security decisions.

## Security Design Notes

The project deliberately avoids live URL fetching in the browser because browser CORS restrictions make unrestricted cross-origin inspection unreliable. Instead, the scanner analyzes the URL string and its parsed components.

This makes the project:

- Fast
- Lightweight
- Privacy-friendly
- Easy to run
- Easy to demonstrate
- Suitable for frontend security education

## Future Improvements

Possible future versions could add:

- Backend URL sandboxing
- DNS and WHOIS checks
- TLS certificate inspection
- Reputation APIs
- Google Safe Browsing or other threat-intelligence integrations
- VirusTotal-style multi-engine lookup
- Screenshot-based webpage analysis
- Domain age and registration analysis
- Redirect-chain analysis
- Real ML classification
- Export to PDF/JSON
- User authentication
- Persistent scan database
- API endpoint for automated scanning

## Disclaimer

Sentinel AI is an educational security and triage project. Its heuristic results are indicators, not proof of malicious activity. Always verify suspicious URLs using trusted security tools and established threat-intelligence sources before making security decisions.

## Author

**Arka Patra**

Cybersecurity / Computer Science Project

---

### Project Highlights

**Sentinel AI** combines URL threat heuristics, password-strength analysis, live QR-code scanning with URL handoff, a responsive cybersecurity interface, and privacy-focused client-side processing into a single lightweight web application.
