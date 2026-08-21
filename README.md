# R-Browser

> Private, browser-based file manager for S3-compatible object storage.

R-Browser is a single-page HTML application that lets you manage S3-compatible storage directly from your browser. It is designed to be deployed as a static site, including GitHub Pages, without requiring a dedicated backend server.

## ✨ Features

- 🔐 Direct browser-to-S3-compatible storage connection
- ☁️ Cloudflare R2 support
- 📁 Browse buckets, folders, and files
- ⬆️ Upload files with drag & drop or file picker
- ⬇️ Download files
- ✏️ Rename files and folders
- 🗑️ Delete selected files/folders
- 📄 Create empty files
- 📂 Create new folders
- 📋 Copy / cut / paste files and folders
- 🔎 Search files
- ℹ️ View file properties and metadata
- 👁️ Preview supported file types
- 🔗 Generate temporary share links
- 📱 Generate QR codes for share links locally in the browser
- 🎵 Built-in audio player with queue support
- ⏸️ Transfer panel with upload progress
- ⏯️ Pause/resume support for multipart transfers
- ❌ Cancel active uploads
- 🌓 Light and dark themes
- 🌐 Indonesian and English interface
- 📱 Responsive mobile layout
- 🔒 Optional encrypted credential storage using AES-GCM

The application is implemented as a single deployable HTML file and uses the AWS SDK for JavaScript v2 from its official CDN. The application metadata explicitly describes it as a private S3 file browser. [Source](v1.3.html)

## 🏗️ Architecture

R-Browser is intentionally backend-less:

```text
┌──────────────────────────┐
│        Browser           │
│                          │
│      R-Browser UI        │
│            │             │
│     AWS SDK v2           │
│            │             │
│     Signed S3 Requests   │
└────────────┬─────────────┘
             │
             │ HTTPS / S3 API
             ▼
┌──────────────────────────┐
│   S3-Compatible Storage  │
│                          │
│  Cloudflare R2 / S3 /    │
│  Other S3-compatible     │
│  providers               │
└──────────────────────────┘
```

The source code signs S3 requests directly in the browser, so there is no R-Browser server receiving the access key or secret key.

For Cloudflare R2, a bucket name can be provided so the application can connect directly without relying on an account-level `ListBuckets` request.

## 🔐 Security

R-Browser is designed so that storage credentials remain client-side.

When **Remember this connection** is enabled:

- Credentials are stored locally in the browser.
- Credentials are encrypted using **AES-GCM**.
- The encryption passphrase is **not stored**.
- Transfer history and optional remembered credentials use browser `localStorage`.
- QR codes for share links are generated locally and are not sent to an external QR service.

> **Important:** R-Browser is a client-side application. Treat your S3 credentials as sensitive and only use credentials with the minimum permissions required for the intended bucket.

## ☁️ Cloudflare R2

For Cloudflare R2, the connection form supports:

| Field | Description |
|---|---|
| Access Key ID | R2 API access key |
| Secret Access Key | R2 API secret |
| Endpoint | R2 S3-compatible endpoint |
| Bucket name | Target R2 bucket |
| Region | Usually `auto` for R2 |
| Passphrase | Used when saving an encrypted connection locally |

Example endpoint:

```text
https://<account-id>.r2.cloudflarestorage.com
```

The application recommends providing the bucket name for R2 so it can open the specified bucket directly.

## 🚀 Deployment

### Option 1 — GitHub Pages

Because R-Browser is a static HTML application, it can be deployed directly to GitHub Pages.

1. Create a GitHub repository.
2. Add the application file:

```text
index.html
```

3. Copy the contents of `v1.3.html` into `index.html`.
4. Commit and push the changes.
5. Open:

```text
Repository → Settings → Pages
```

6. Under **Build and deployment**, select:
   - Source: `Deploy from a branch`
   - Branch: `main`
   - Folder: `/ (root)`
7. Save the configuration.
8. Wait for GitHub Pages to publish the site.

Your R-Browser instance will then be available at your GitHub Pages URL.

### Option 2 — Any Static Web Server

R-Browser does not require Node.js, PHP, Python, or another application server.

It can be hosted on:

- GitHub Pages
- Cloudflare Pages
- Netlify
- Vercel static hosting
- Nginx
- Apache
- Any static file hosting service

The only required application file is the HTML page.

## ⚙️ CORS Configuration

Your S3-compatible storage endpoint must allow browser requests from the domain where R-Browser is hosted.

For GitHub Pages, configure the bucket CORS policy to allow your GitHub Pages origin.

Example:

```json
[
  {
    "AllowedOrigins": [
      "https://YOUR-USERNAME.github.io"
    ],
    "AllowedMethods": [
      "GET",
      "PUT",
      "POST",
      "DELETE",
      "HEAD"
    ],
    "AllowedHeaders": [
      "*"
    ],
    "ExposeHeaders": [
      "ETag"
    ]
  }
]
```

Replace:

```text
https://YOUR-USERNAME.github.io
```

with your actual deployment origin.

> Do not blindly copy this policy into production. Restrict `AllowedOrigins` and permissions to what your storage provider and use case actually require.

The application itself notes that the S3-compatible endpoint must allow CORS from the GitHub Pages domain hosting the application.

## 📦 Upload Handling

R-Browser uses different upload behavior depending on file size:

```text
File
 │
 ├── ≤ 8 MB
 │      └── Normal S3 upload
 │
 └── > 8 MB
        └── S3 multipart upload
```

Files larger than 8 MB use S3 multipart upload.

The transfer interface tracks upload progress and supports pausing between parts and cancelling multipart uploads.

## 🔗 Temporary Share Links

R-Browser can generate temporary download links for files.

Available expiry options in the current version include:

- 1 hour
- 6 hours
- 1 day
- 7 days

A QR code can also be generated for the temporary link. QR generation is performed locally in the browser.

## 🎵 Media Player

Supported audio extensions include:

```text
.mp3
.wav
.ogg
```

The application includes:

- Play / pause
- Audio queue
- Queue management
- Repeat state
- Current track information

Supported media icons also include common image, video, PDF, and archive extensions.

## 📱 Responsive Design

R-Browser includes a responsive interface for smaller screens.

Mobile-specific behavior includes:

- Mobile navigation drawer
- Touch-friendly controls
- Horizontal toolbar scrolling
- Responsive file table
- Mobile transfer panel
- Mobile media player
- Larger touch targets

The interface uses a breakpoint around `760px`.

## 🌐 Language & Theme

The interface currently supports:

```text
ID — Indonesian
EN — English
```

Themes:

```text
Light
Dark
```

Language and theme preferences are stored locally in the browser.

## 🧰 Technology Stack

| Technology | Purpose |
|---|---|
| HTML5 | Application structure |
| CSS3 | UI and responsive design |
| JavaScript | Application logic |
| AWS SDK for JavaScript v2 | S3-compatible API requests |
| Web Crypto API | AES-GCM credential encryption |
| Web Storage API | Local preferences and optional local data |
| HTML5 Audio API | Built-in audio player |
| QR Code generator | Local QR generation |

The application loads AWS SDK v2 from the official AWS CDN:

```html
<script src="https://sdk.amazonaws.com/js/aws-sdk-2.1692.0.min.js"></script>
```

## 📂 Repository Structure

A minimal repository can look like this:

```text
r-browser/
├── index.html
└── README.md
```

If you want to keep the original version filename:

```text
r-browser/
├── v1.3.html
└── README.md
```

For GitHub Pages, using `index.html` is recommended for the simplest deployment.

## ⚠️ Limitations & Considerations

- The storage provider must support the required S3-compatible API operations.
- CORS must be correctly configured on the storage endpoint.
- Browser extensions, corporate proxies, or restrictive network policies may interfere with S3 requests.
- Credentials are only as secure as the browser/device where they are entered.
- Preview availability depends on the file type and browser capabilities.
- Temporary share links depend on the capabilities of the connected S3-compatible provider.
- GitHub Pages only hosts the frontend; it does not provide the S3 backend.

## 🛡️ Recommended IAM / API Permissions

For security, create a dedicated storage credential with only the permissions R-Browser needs.

A typical setup may require permissions equivalent to:

```text
List bucket
List objects
Read objects
Write objects
Delete objects
Read object metadata
```

Exact permission names vary between Amazon S3, Cloudflare R2, and other S3-compatible providers.

## 🧪 Local Testing

You can test the application locally using a simple static HTTP server.

For example, with Python:

```bash
python -m http.server 8080
```

Then open:

```text
http://localhost:8080
```

For production deployment, use HTTPS whenever possible.

## 📜 License

No explicit project license is declared in the supplied `v1.3.html`.

If you intend to publish this project as open source, add a license file such as:

```text
LICENSE
```

and update this section accordingly.

## 🙏 Credits

- AWS SDK for JavaScript
- `qrcode-generator` by Kazuhiko Arase, used for local QR-code generation
- S3-compatible storage APIs

---

**R-Browser** — Your S3, still yours. 🔐
