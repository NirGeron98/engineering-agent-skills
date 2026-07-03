# Sample Task

## User prompt

"Review this file-upload endpoint for unsafe input handling and validation gaps."

## Abbreviated run

- Inspect the upload route: accepted content types, filename handling, storage destination, and size limits.
- Trace filename from request to filesystem write to check for path traversal risk.
- Obstacle: the storage backend config (path canonicalization, chroot/bucket isolation) is not visible in the reviewed files.

## Evidence requests

- Storage backend configuration or code that writes the file to disk/bucket.
- Any existing content-type/magic-byte validation logic.
- Deployment context: is upload storage isolated from the app filesystem.

## Expected output

An input validation review with the input surface map, a trust-boundary finding on filename handling marked pending until storage backend config is seen, gaps in content-type validation, a remediation plan (canonicalize paths, allowlist content types, size limits), and test recommendations for malformed/oversized/traversal-attempt filenames without literal payload strings.

## Why this passes the quality bar

The agent traces the finding to actual code, does not assume the storage layer is safe without evidence, and does not include a working traversal payload.
