# R-Browser v1.0

R-Browser is a client-side S3 file explorer for Cloudflare R2 and other S3-compatible storage. It is deployed as one index.html file and has no application backend.

## Deploy

Upload index.html to GitHub Pages or any HTTPS static host. For local testing, serve it from a local web server, for example http://127.0.0.1:5500; do not open it using a file URL.

For Cloudflare R2, use:

    Endpoint: https://<ACCOUNT_ID>.r2.cloudflarestorage.com
    Bucket name: <YOUR_BUCKET_NAME>
    Region: auto

Do not append :443 to the endpoint. Supplying a bucket name avoids the account-level ListBuckets request, which may not be permitted by browser CORS rules.

## CORS: required for every R-Browser user

Every browser origin that uses this UI must be allowed by the bucket's CORS policy. An origin is only the scheme, host, and optional port. It must match exactly—do not include a trailing slash or a path.

Example policy:

    [
      {
        "AllowedOrigins": [
          "http://127.0.0.1:5500",
          "http://localhost:5500",
          "https://pixel4bit.github.io",
          "https://files.example.com"
        ],
        "AllowedMethods": ["GET", "HEAD", "PUT", "POST", "DELETE"],
        "AllowedHeaders": ["*"],
        "ExposeHeaders": ["ETag", "Content-Length", "Content-Type"],
        "MaxAgeSeconds": 3600
      }
    ]

Remove origins that you do not use. If another person hosts the UI at a different domain or port, add that exact origin to AllowedOrigins before they connect.

### Cloudflare R2

Cloudflare Dashboard → R2 Object Storage → choose the bucket → Settings → CORS Policy → paste the JSON above → Save. Wait up to about 30 seconds for the policy to apply.

### Amazon S3

AWS Console → S3 → choose the bucket → Permissions → Cross-origin resource sharing (CORS) → Edit → paste the JSON above.

### MinIO

Configure CORS on the relevant bucket in MinIO Console, or with mc cors set. Use the same origin, method, and header values shown above.

## Security

- Use separate, least-privilege S3 credentials for each user. Limit access to only the intended bucket and required operations.
- R-Browser never transmits credentials to an R-Browser server. Nevertheless, a browser UI cannot keep a key as safe as a server-side secret.
- Remember connection uses AES-GCM and a user-supplied passphrase. The passphrase is never written to localStorage.
- Rotate any key accidentally shown in a screenshot, chat, or source repository.

## Transfers

The Transfer window displays progress, byte size, speed, elapsed time, remaining-time estimate, and per-file controls.

- Files above 8 MB are sent in multipart parts.
- Pause takes effect after the current part completes; Resume continues with the next part.
- Cancel aborts the active request or multipart upload.
- Completed, failed, and cancelled entries are kept locally in transfer history (up to 50 records).

## v1.0 scope

Includes bucket browsing, upload/download, folder and empty-file creation, rename, copy/cut/paste, delete, property inspection, media/PDF preview, encrypted remembered connection, bilingual UI, dark/light themes, and transfer history.
