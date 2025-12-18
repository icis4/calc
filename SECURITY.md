# Security Policy

## Supported Versions

This project is a small, single-file calculator app.

- **Supported**: the latest version on the default branch
- **Unsupported**: older commits / forks (best-effort only)

## Reporting a Vulnerability

If you believe you’ve found a security issue, please **do not** open a public issue with exploit details.

Instead, report it privately with:

- A clear description of the issue and potential impact
- Steps to reproduce (PoC if possible)
- Your browser + OS
- Any relevant screenshots / console output

### Contact

- Create a **private report** via your preferred secure channel (e.g., direct message/email used by the maintainer), or
- If no private channel is available, open a GitHub issue with **minimal details** and ask for a private contact method.

## Disclosure Process

We aim to:

- Acknowledge receipt within **7 days**
- Provide a fix or mitigation as soon as reasonably possible
- Coordinate public disclosure after a patch is available

## Security Notes

- The app runs entirely in the browser and does not use a backend.
- It persists simple UI preferences (e.g., mode toggles) using cookies.
- Avoid adding features that introduce risk (e.g., `eval`, remote script injection, or untrusted HTML rendering).
