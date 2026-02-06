[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-FFDD00?logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/rocksec)           [![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-FFDD00?logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/rocksec)

# 🦅 JS Secret Scanner (Docker Edition)

A powerful, threaded, and automated **CLI security tool** designed to aggressively scan JavaScript files for **secrets, API keys, tokens, credentials, and hidden endpoints**.

Built for **security researchers and bug bounty hunters**, the JS Secret Scanner combines **active crawling**, **passive intelligence**, and **hybrid secret detection** into a single Dockerized workflow.

---

## 📖 About

**JS Secret Scanner** is a "Swiss Army Knife" for JavaScript reconnaissance. It doesn't just scan files you give it; it finds them for you. It combines massive subdomain enumeration with automated JS discovery (Wayback Machine, AlienVault, etc.) to uncover every JS file associated with a target.

Then, it uses a **hybrid scanning engine** (Custom Regex + Trufflehog) to find secrets, and automatically performs **advanced analysis** like unwrapping Source Maps (`.js.map`) to reveal the original TypeScript/source code.

---

## 🚀 Features

### 🔍 Discovery & reconnaissance
- ⚡ **Automated JS Discovery** – Finds JavaScript files using 5 engines: **Katana, Waybackurls, Gau, Subjs, and Hakrawler**.
- 🌍 **Massive Subdomain Enumeration** – **[NEW]** Optional `--subdomains` flag launches 11+ tools (Subfinder, Amass, Assetfinder, Findomain, MassDNS, etc.) to widen your scope before scanning.

### 🛡️ Advanced Analysis (New!)
- 🗺️ **Source Map Unpacker** – **[NEW]** Automatically detects `.js.map` files, downloads them, and extracts the **original unminified source code** (e.g., TypeScript, Webpack sources) to find secrets that are lost in the minified bundle.
- 🕵️ **Obfuscation Detection** – **[NEW]** Detects if a file is hiding code using `eval()`, `atob()`, or packing techniques.
- 🚨 **Risk Classification** – **[NEW]** Smartly tags findings with **Risk Levels**:
    - **{Fore.RED}HIGH RISK{Style.RESET_ALL}**: Hardcoded Credentials (AWS/Stripe keys) or IDOR indicators (e.g., `/user/{id}`, `/admin/delete`).
    - **MEDIUM/LOW**: General API endpoints or information disclosure.

### 🛠️ Core Scanning
- 🛠️ **Hybrid Secret Scanning** – Detects secrets using **200+ custom regex signatures** AND **Trufflehog** (high entropy checks).
- 🕷️ **Content Extraction** – Extracts URLs, API endpoints, S3 buckets, and dependency versions.
- 🧵 **Multi-Threaded** – Blazing fast concurrent scanning with configurable threads.
- 🛡️ **Proxy Support** – Rotation support via HTTP/SOCKS proxies (Tor, Burp) to evade WAFs.

## 📦 Docker Image

**Image Name**
```bash
rocksec/js-wrapper:latest
```
*(Supports both AMD64 and ARM64/Apple Silicon)*

---

## ⚡ Quick Reference

| Flag | Long Flag | Description |
|-----:|-----------|-------------|
| `-d` | `--domain` | Scan a single domain or a specific `.js` URL |
| `-l` | `--list` | Scan a list of domains or JS URLs from a file |
| `-o` | `--output` | Save results to a file |
| `--subdomains` | **[NEW]** | Enable aggressive subdomain enumeration (Slow but powerful) |
| `-t` | `--threads` | Number of concurrent scans (default: `10`) |
| `-p` | `--proxy` | Route traffic through a proxy |
| `-st` | `--scan-type` | `regex`, `tools`, or `all` |

---

## 🛠️ Installation

### Pull the Docker Image
```bash
docker pull rocksec/js-wrapper:latest
```

Docker Hub : https://hub.docker.com/r/rocksec/js-wrapper
---

## ▶️ Usage

### 🌍 Standalone Subdomain Discovery
If you only want to find subdomains without scanning for secrets, use the `--subs-only` flag. This runs all 11 enumeration tools, saves the results, and exits.

**Command:**
```bash
docker run --rm -v $(pwd):/app js-wrapper -d example.com --subs-only -o subdomains.txt
```

---

### 1️⃣ Basic Scan (Hello World)

Scan a single domain (Auto-finds JS files):
```bash
docker run --rm rocksec/js-wrapper -d example.com
```

Scan a single JavaScript file directly:
```bash
docker run --rm rocksec/js-wrapper -d https://target.com/assets/app.chunk.js
```

---

### 2️⃣ Full Reckon Scan (Subdomains + JS) 🌍

Use the new **`--subdomains`** flag to run the full suite (Subfinder, Amass, etc.) before scanning.

```bash
docker run --rm rocksec/js-wrapper -d example.com --subdomains
```

---

### 3️⃣ Save Results to a File (IMPORTANT 💾)

Docker containers are ephemeral. To **persist output**, you must map your current directory using `-v`.

#### Windows (Command Prompt)
```cmd
docker run --rm -v %cd%:/app rocksec/js-wrapper -d example.com -o report.txt
```

#### Linux / macOS / PowerShell
```bash
docker run --rm -v $(pwd):/app rocksec/js-wrapper -d example.com -o report.txt
```
📌 *The `report.txt` file will appear in your current folder.*

---

### 4️⃣ Scan a List of Targets or List of JS URLs

Create a file `targets.txt` in your current folder:
```txt
example.com
sub.test.com
https://site.com/main.js
```

Run the scan (remember to map the volume so Docker can see the file):
```bash
docker run --rm -v $(pwd):/app rocksec/js-wrapper -l targets.txt -o results.txt
```

---

### 5️⃣ Bypass Rate Limits (Tor/Burp) 🛡️

Use a proxy to hide your IP or debug traffic.

```bash
docker run --rm js-wrapper -d example.com -p socks5://127.0.0.1:9050
```

---

## 📄 Output Format

The tool generates a clean, pipe-separated report (`secrets_report.txt`) with the new **Risk** and **Source** context:

```text
Domain: example.com | URL: https://example.com/app.js.map | Type: AWS Access Key | Secret: AKIAIOSFODNN7EXAMPLE | Risk: HIGH | Source: SOURCEMAP
Domain: example.com | URL: https://example.com/main.js | Type: EXTRACTED_ENDPOINT | Secret: /api/v1/user/{id} | Risk: HIGH | Reason: Potential IDOR | Source: DIRECT
Domain: sub.example.com | URL: https://example.com/vendor.js | Type: EXTRACTED_S3_BUCKET | Secret: my-bucket.s3.amazonaws.com | Risk: LOW | JS_OBFUSCATED: TRUE
```

*   **Risk: HIGH**: Pay attention! Credentials or IDORs.
*   **Source: SOURCEMAP**: This secret was found inside the *original* source code, not the minified file.
*   **JS_OBFUSCATED**: This file is trying to hide something.

---

## ⚠️ Disclaimer

This tool is for **educational purposes and authorized security testing only**. You must have permission to scan the target domains. The author is not responsible for any misuse or damage caused by this tool.

---

If you find secrets, endpoints, or credentials — **report responsibly**.

## 🔥 Happy Hacking

## ☕ Buy Me a Coffee

If this tool helps you find valid bugs, consider supporting the project! ❤️

[![Buy Me a Coffee](https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png)](https://buymeacoffee.com/rocksec)
