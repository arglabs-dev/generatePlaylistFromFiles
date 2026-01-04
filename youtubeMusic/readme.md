# 🎵 YTM Migrator (Local to YouTube Music)

A robust Python CLI tool to migrate your local music library (M4A/MP3) to **YouTube Music**. It automatically searches for matches in the official catalog and uploads local files if no match is found.

## 🚀 Features

* **Smart Matching:** Scans your local metadata (JSON) and finds the corresponding song in YouTube Music.
* **Auto-Upload:** If a song is missing from the streaming catalog, it uploads your local file to your private library.
* **Legacy Account Support:** Supports standard Google Accounts as well as Legacy/Brand accounts via browser authentication.
* **Dual Reporting:** Generates a detailed Markdown table and a technical log file for every run.

---

## 🛠️ Prerequisites

### 1. System Dependencies (Ubuntu/Debian)
You need `exiftool` to generate the metadata JSON file required by the script.

```bash
sudo apt update && sudo apt install libimage-exiftool-perl

```

### 2. Python Environment

Ensure you have Python 3.8+ installed.

```bash
# Create a virtual environment
python3 -m venv venv

# Activate it
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

```

*(Create a `requirements.txt` file with `ytmusicapi` inside).*

---

## 🔐 Authentication Setup

You have two options to authenticate. **Try Option 1 first.** If you have a Brand Account, G Suite, or a Legacy YouTube account (created before 2009) and receive errors, use Option 2.

### Option 1: OAuth (Standard)

Best for standard personal Gmail accounts.

1. Run the setup command:
```bash
ytmusicapi oauth

```


2. Follow the link provided in the terminal to authorize with your Google Account.
3. This will generate a file named `oauth.json`.

### Option 2: Browser Cookies (Robust / Legacy Accounts)

Use this if Option 1 fails (e.g., Error 400/403) or if you want to use a specific Brand/Legacy account.

1. Open **[music.youtube.com](https://music.youtube.com)** in your browser (Chrome/Firefox) and ensure you are logged into the correct account.
2. Open **Developer Tools** (Press `F12` or `Ctrl+Shift+I`).
3. Go to the **Network** tab.
4. In the "Filter" box (top left of the panel), type: `browse`.
5. Refresh the page (`F5`).
6. Click on the first network request named `browse` (or `browse?ctoken=...`).
7. In the details panel (right side), go to the **Headers** tab and scroll down to **Request Headers**.
8. Right-click on the word `browse` in the list -> **Copy** -> **Copy request headers**.
* *Alternatively, manually copy the text starting with `Cookie: ...` and `Authorization: ...*`.


9. Go back to your terminal and run:
```bash
ytmusicapi browser

```


10. Paste the headers, press **Enter**, then press **Ctrl+D**.
11. This will generate a file named `browser.json`.

---

## ⚙️ Configuration

Ensure your script loads the correct authentication file.

**File:** `secrets_config.py` (or inside your main script)

```python
from ytmusicapi import YTMusic

# If using Option 1 (OAuth)
# auth_file = 'oauth.json'

# If using Option 2 (Browser Cookies) - RECOMMENDED for stability
auth_file = 'browser.json'

yt = YTMusic(auth_file)

```

---

## 🏃 Usage

### Step 1: Generate Metadata

Before running the Python script, use `exiftool` to index your music files into a JSON file.

```bash
# Run this inside your music folder containing .m4a/.mp3 files
exiftool -json -Title -Artist -Album -Year -Duration *.m4a > library.json

```

### Step 2: Run the Migrator

Move the `library.json` file to your project folder and run the script.

**Basic Run:**

```bash
python3 migrator.py --json library.json --playlist "My Playlist Name"

```

**Run with Uploads (Recommended):**
This enables the script to upload songs found in your local folder if they don't exist on YouTube Music.

```bash
python3 migrator.py --json library.json --playlist "Salsa Fest 2019" --music-dir "/path/to/my/local/music"

```

---

## 📊 Outputs

After execution, check the generated files:

1. **`migration_report_YYYYMMDD.md`**: A summary table of results.
2. **`migration_log_YYYYMMDD.log`**: Detailed debug logs (check here if uploads fail).

## ⚠️ Note on Uploads

Songs uploaded via the API take **5-10 minutes** to be processed by Google. They will appear in your "Uploads" section eventually.

