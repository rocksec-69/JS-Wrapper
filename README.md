
# 🦅 JS Secret Scanner (Docker Edition)

A powerful, threaded, and automated **CLI security tool** designed to aggressively scan JavaScript files for **secrets, API keys, tokens, credentials, and hidden endpoints**.

Built for **security researchers and bug bounty hunters**, the JS Secret Scanner combines **active crawling**, **passive intelligence**, and **hybrid secret detection** into a single Dockerized workflow.

---

## 📖 About

# JS Secret Scanner

A powerful, threaded, and automated Command Line Interface (CLI) tool designed to aggressively scan JavaScript files  
for secrets, API keys, tokens, and endpoints. It combines active crawling with passive discovery (Wayback Machine, AlienVault)  
to find every JS file associated with a domain and uses a hybrid scanning engine (Custom Regex + Trufflehog)  
to uncover sensitive information.

---

## 🚀 Features

- ⚡ **Automated JS Discovery** – Finds JavaScript files using Katana, Waybackurls, Gau, Subjs, and Hakrawler.
- 🛠️ **Hybrid Secret Scanning** – Detects secrets using 200+ custom regex patterns and Trufflehog.
- 🕷️ **Content Extraction** – Extracts URLs, API endpoints, and S3 buckets from JavaScript files.
- 🧵 **Multi-Threaded** – Fast concurrent scanning with configurable threads.
- 🛡️ **Proxy Support** – Supports HTTP/SOCKS proxies (Tor, Burp, etc.) to bypass rate limits.

---

## 📦 Docker Image

**Image Name**
```
rocksec/js-wrapper:latest
````

---

## ⚡ Quick Reference

| Flag | Long Flag | Description |
|-----:|-----------|-------------|
| `-d` | `--domain` | Scan a single domain or a specific `.js` URL |
| `-l` | `--list` | Scan a list of domains or JS URLs from a file |
| `-o` | `--output` | Save results to a file |
| `-t` | `--threads` | Number of concurrent scans (default: `10`) |
| `-p` | `--proxy` | Route traffic through a proxy |
| `-st` | `--scan-type` | `regex`, `tools`, or `all` |

---

## 🛠️ Installation

### Pull the Docker Image
```bash
docker pull rocksec/js-wrapper:latest
````

---

## ▶️ Usage

### 1️⃣ Basic Scan (Hello World)

Scan a single domain:

```bash
docker run --rm rocksec/js-wrapper -d example.com
```

Scan a single JavaScript file:

```bash
docker run --rm rocksec/js-wrapper -d https://target.com/app.js
```

---

### 2️⃣ Save Results to a File (IMPORTANT 💾)

Docker containers are ephemeral.
To **persist output**, you must map your current directory.

#### Windows (Command Prompt)

```bash
docker run --rm -v %cd%:/app rocksec/js-wrapper -d example.com -o report.txt
```

#### Linux / macOS / PowerShell

```bash
docker run --rm -v ${PWD}:/app rocksec/js-wrapper -d example.com -o report.txt
```

📌 The output file will appear in your current directory.

---

### 3️⃣ Scan a List of Domains or List of JS Urls

If you have a file called `targets.txt`:

```txt
example.com
https://site.com/main.js
```

Run:

#### Windows

```bash
docker run --rm -v %cd%:/app rocksec/js-wrapper -l targets.txt -o results.txt
```

#### Linux / macOS

```bash
docker run --rm -v ${PWD}:/app rocksec/js-wrapper -l targets.txt -o results.txt
```

---

### 4️⃣ Bypass Rate Limits Using a Proxy 🛡️

#### Using Tor (SOCKS5)

*(Tor must be running on your host)*

```bash
docker run --rm \
  -p host.docker.internal:9050:9050 \
  js-wrapper \
  -d example.com \
  -p socks5://host.docker.internal:9050
```

### 🎨 No Color Output

Docker may disable TTY by default.

**Fix**

```bash
docker run --rm -it rocksec/js-wrapper -d example.com
```

---

## 🧠 Tips

* Increase threads (`-t 30`) for faster scans
* Use proxies for aggressive targets
* Scan both **domains** and **direct JS URLs**
* Always save results using `-o`

---

## 🔥 Happy Hacking

If you find secrets, endpoints, or credentials — **report responsibly**.


## ☕ Buy Me a Coffee

If this project helps you, consider supporting it ❤️  

[![Buy Me a Coffee](https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png)](https://buymeacoffee.com/rocksec)

```

