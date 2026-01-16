# Windows User Guide for gallery-dl

This guide provides Windows-specific instructions for installing and using gallery-dl, with examples ranging from simple to complex usage scenarios.

## Table of Contents

1. [Installation](#installation)
2. [Getting Started - Simple Examples](#getting-started---simple-examples)
3. [Intermediate Usage](#intermediate-usage)
4. [Advanced Scenarios](#advanced-scenarios)
5. [Configuration](#configuration)
6. [Troubleshooting](#troubleshooting)

---

## Installation

### Method 1: Standalone Executable (Recommended for Beginners)

The easiest way to use gallery-dl on Windows is with the standalone executable.

1. **Download the executable:**
   - Visit the [latest release page](https://github.com/mikf/gallery-dl/releases/latest)
   - Download `gallery-dl.exe`

2. **Install Visual C++ Redistributable:**
   - Download and install [Microsoft Visual C++ Redistributable Package (x86)](https://aka.ms/vs/17/release/vc_redist.x86.exe)

3. **Place the executable:**
   - Create a folder: `C:\gallery-dl\`
   - Move `gallery-dl.exe` to this folder
   - Add `C:\gallery-dl\` to your PATH environment variable (optional but recommended)

**Adding to PATH:**
1. Press `Win + X` and select "System"
2. Click "Advanced system settings"
3. Click "Environment Variables"
4. Under "User variables", select "Path" and click "Edit"
5. Click "New" and add `C:\gallery-dl\`
6. Click "OK" on all dialogs
7. Restart Command Prompt or PowerShell

### Method 2: Using Chocolatey

If you have [Chocolatey](https://chocolatey.org/) installed:

```powershell
choco install gallery-dl
```

### Method 3: Using Scoop

If you have [Scoop](https://scoop.sh/) installed:

```powershell
scoop install gallery-dl
```

### Method 4: Using pip

If you have Python 3.8+ installed:

```powershell
py -3 -m pip install -U gallery-dl
```

**Verify installation:**
```powershell
gallery-dl --version
```

---

## Getting Started - Simple Examples

### Scenario 1: Download a Single Image

Download a single image from a supported site:

```powershell
gallery-dl "https://www.deviantart.com/shimoda7/art/..."
```

The image will be downloaded to your current directory.

### Scenario 2: Download to a Specific Folder

Download to your Pictures folder:

```powershell
gallery-dl -d "C:\Users\YourUsername\Pictures\Downloads" "https://www.pixiv.net/en/artworks/12345678"
```

Or use the Downloads folder:

```powershell
gallery-dl -d "%USERPROFILE%\Downloads\gallery-dl" "URL"
```

### Scenario 3: Download Multiple URLs

Save multiple URLs in a text file (`urls.txt`) in your Downloads folder:
```
https://www.pixiv.net/en/artworks/12345678
https://twitter.com/artist/status/123456789
https://www.deviantart.com/artist/art/artwork-123456
```

Then download them all:

```powershell
gallery-dl -i "%USERPROFILE%\Downloads\urls.txt"
```

### Scenario 4: View URLs Without Downloading

Useful for previewing what would be downloaded:

```powershell
gallery-dl -g "URL"
```

This prints the direct URLs without downloading anything.

---

## Intermediate Usage

### Scenario 5: Using Authentication

#### Username and Password

For sites requiring login (e.g., Twitter, Danbooru):

```powershell
gallery-dl -u "your_username" -p "your_password" "URL"
```

#### Using Browser Cookies

For sites with CAPTCHA protection (e.g., Instagram, Pixiv):

1. Install a browser extension to export cookies:
   - Chrome: [Get cookies.txt LOCALLY](https://chrome.google.com/webstore/detail/get-cookiestxt-locally/cclelndahbckbenkjhflpdbgdldlbecc)
   - Firefox: [Export Cookies](https://addons.mozilla.org/en-US/firefox/addon/export-cookies-txt/)

2. Export cookies for the site to `C:\Users\YourUsername\cookies.txt`

3. Use the cookies file:

```powershell
gallery-dl --cookies "C:\Users\YourUsername\cookies.txt" "URL"
```

Or extract cookies directly from your browser:

```powershell
gallery-dl --cookies-from-browser chrome "URL"
```

Supported browsers: `chrome`, `firefox`, `edge`, `opera`, `brave`, `vivaldi`

### Scenario 6: Download User Galleries

Download all works from a specific artist:

```powershell
# Pixiv user gallery
gallery-dl "https://www.pixiv.net/en/users/123456"

# DeviantArt user gallery
gallery-dl "https://www.deviantart.com/username"

# Twitter user media
gallery-dl "https://twitter.com/username/media"
```

### Scenario 7: Filtering Downloads

#### Filter by Date

Download only recent posts:

```powershell
gallery-dl --range "1-50" "https://twitter.com/username/media"
```

#### Filter Manga Chapters

Download specific chapters (10-19) in French:

```powershell
gallery-dl --chapter-filter "10 <= chapter < 20" -o "lang=fr" "https://mangadex.org/title/..."
```

#### Filter Images by Resolution

Download only high-resolution images (width >= 1920):

```powershell
gallery-dl --image-filter "width >= 1920" "URL"
```

### Scenario 8: Custom File Organization

Organize downloads by artist name and work ID:

```powershell
gallery-dl -d "C:\Gallery" -f "{category}\{user[name]}\{id}.{extension}" "URL"
```

Example output structure:
```
C:\Gallery\
├── pixiv\
│   ├── ArtistName\
│   │   ├── 12345678.jpg
│   │   └── 87654321.png
```

---

## Advanced Scenarios

### Scenario 9: Using a Configuration File

Create a configuration file for persistent settings.

**Location:** `C:\Users\YourUsername\gallery-dl\config.json`

**Basic Configuration Example:**

```json
{
    "extractor": {
        "base-directory": "C:/Users/YourUsername/Pictures/gallery-dl/",
        "pixiv": {
            "cookies": "C:/Users/YourUsername/pixiv-cookies.txt"
        },
        "twitter": {
            "username": "your_username",
            "password": "your_password"
        }
    }
}
```

**Note:** Use forward slashes (`/`) in paths within JSON configuration files.

Now you can simply run:
```powershell
gallery-dl "URL"
```

Settings from the config file will be automatically applied.

### Scenario 10: Batch Processing with Progress Tracking

Download multiple URLs and mark them as processed:

1. Create `pending-downloads.txt`:
```
https://www.pixiv.net/en/artworks/12345678
https://twitter.com/artist/status/123456789
https://www.deviantart.com/artist/art/artwork-123456
```

2. Download and comment out completed URLs:

```powershell
gallery-dl -I "%USERPROFILE%\Downloads\pending-downloads.txt"
```

After downloading, the file will look like:
```
# https://www.pixiv.net/en/artworks/12345678
# https://twitter.com/artist/status/123456789
https://www.deviantart.com/artist/art/artwork-123456
```

### Scenario 11: Automated Daily Downloads

Create a PowerShell script (`C:\Scripts\daily-download.ps1`):

```powershell
# daily-download.ps1
$ErrorActionPreference = "Continue"

# Set download directory
$DownloadPath = "$env:USERPROFILE\Pictures\gallery-dl\daily"

# Create directory if it doesn't exist
if (-not (Test-Path $DownloadPath)) {
    New-Item -ItemType Directory -Path $DownloadPath | Out-Null
}

# Download from favorite artists
$artists = @(
    "https://twitter.com/artist1/media",
    "https://www.pixiv.net/en/users/123456",
    "https://www.deviantart.com/artist2"
)

foreach ($url in $artists) {
    Write-Host "Downloading from: $url"
    gallery-dl --range "1-20" -d $DownloadPath $url
}

Write-Host "Daily download complete!"
```

**Schedule with Task Scheduler:**

1. Open Task Scheduler (`taskschd.msc`)
2. Create Basic Task
3. Set trigger (e.g., Daily at 9:00 AM)
4. Action: Start a program
   - Program: `powershell.exe`
   - Arguments: `-ExecutionPolicy Bypass -File "C:\Scripts\daily-download.ps1"`
5. Save the task

### Scenario 12: Advanced Configuration with Multiple Sites

**Configuration file** (`C:\Users\YourUsername\gallery-dl\config.json`):

```json
{
    "extractor": {
        "base-directory": "D:/Gallery/",
        "archive": "D:/Gallery/.archive.sqlite3",
        
        "skip": true,
        "sleep-request": 1.0,
        
        "path-restrict": {
            "\\": "⧹",
            "/": "⧸",
            "|": "￨",
            ":": "꞉",
            "*": "∗",
            "?": "？",
            "\"": "″",
            "<": "﹤",
            ">": "﹥"
        },
        
        "pixiv": {
            "cookies": "C:/Users/YourUsername/cookies/pixiv.txt",
            "directory": ["Pixiv", "{user[name]}", "{id}"],
            "filename": "{id}_p{num}.{extension}",
            "ugoira": true
        },
        
        "twitter": {
            "cookies": ["chrome"],
            "directory": ["Twitter", "{author[name]}"],
            "filename": "{tweet_id}_{num}.{extension}",
            "retweets": false,
            "text-tweets": true
        },
        
        "instagram": {
            "cookies": "C:/Users/YourUsername/cookies/instagram.txt",
            "directory": ["Instagram", "{username}"],
            "filename": "{shortcode}.{extension}",
            "videos": true
        },
        
        "danbooru": {
            "username": "your_username",
            "password": "your_password",
            "directory": ["Danbooru", "{category}"],
            "filename": "{category}_{id}.{extension}"
        }
    }
}
```

### Scenario 13: Using Archive to Avoid Re-downloading

Prevent re-downloading previously saved files:

```powershell
gallery-dl --archive "C:\Gallery\archive.sqlite3" -d "C:\Gallery" "URL"
```

Or in your configuration:

```json
{
    "extractor": {
        "archive": "C:/Gallery/archive.sqlite3",
        "base-directory": "C:/Gallery/"
    }
}
```

### Scenario 14: Post-Processing Downloaded Files

#### Convert Ugoira (Pixiv animations) to video:

**Configuration:**
```json
{
    "extractor": {
        "pixiv": {
            "ugoira": true
        }
    }
}
```

Requires [FFmpeg](https://www.ffmpeg.org/download.html) in PATH.

#### Add metadata tags to files:

**Configuration:**
```json
{
    "extractor": {
        "postprocessors": [{
            "name": "metadata",
            "mode": "tags"
        }]
    }
}
```

### Scenario 15: Proxy Configuration

Use a proxy server:

```powershell
gallery-dl --proxy "http://proxy.example.com:8080" "URL"
```

Or in configuration:

```json
{
    "extractor": {
        "proxy": "http://proxy.example.com:8080"
    }
}
```

For SOCKS proxy (requires `PySocks`):

```powershell
py -3 -m pip install PySocks
```

```json
{
    "extractor": {
        "proxy": "socks5://127.0.0.1:1080"
    }
}
```

### Scenario 16: Downloading from Pastebin or Text Lists

Download all URLs found in a web page:

```powershell
gallery-dl "r:https://pastebin.com/raw/YourPasteID"
```

The `r:` prefix tells gallery-dl to recursively search for URLs.

---

## Configuration

### Configuration File Locations (Priority Order)

Windows looks for configuration files in these locations:

1. `%APPDATA%\gallery-dl\config.json`  
   (Usually: `C:\Users\YourUsername\AppData\Roaming\gallery-dl\config.json`)

2. `%USERPROFILE%\gallery-dl\config.json`  
   (Usually: `C:\Users\YourUsername\gallery-dl\config.json`)

3. `%USERPROFILE%\gallery-dl.conf`  
   (Usually: `C:\Users\YourUsername\gallery-dl.conf`)

4. Same directory as `gallery-dl.exe` (for standalone executable only)

**Recommended location:** `%USERPROFILE%\gallery-dl\config.json`

### Creating a Configuration File

1. Create the directory:
```powershell
mkdir "$env:USERPROFILE\gallery-dl"
```

2. Create `config.json` in this folder with your preferred text editor (Notepad, VS Code, etc.)

3. Start with a basic template:

```json
{
    "extractor": {
        "base-directory": "C:/Users/YourUsername/Pictures/Downloads/"
    }
}
```

### Viewing Current Configuration

Check what settings gallery-dl is using:

```powershell
gallery-dl --extractor-info
```

---

## Troubleshooting

### Issue 1: "gallery-dl is not recognized"

**Solution:** Add gallery-dl to your PATH or use the full path:

```powershell
C:\gallery-dl\gallery-dl.exe "URL"
```

### Issue 2: SSL Certificate Errors

**Solution:** Update your certificates or disable verification (not recommended):

```json
{
    "extractor": {
        "verify": false
    }
}
```

Better solution - install certificates:
```powershell
py -3 -m pip install --upgrade certifi
```

### Issue 3: Rate Limiting / HTTP 429 Errors

**Solution:** Add delays between requests:

```json
{
    "extractor": {
        "sleep-request": 2.0
    }
}
```

Or use random delays:
```json
{
    "extractor": {
        "sleep-request": [2.0, 5.0]
    }
}
```

### Issue 4: Authentication Failures

**Solutions:**

1. **For username/password:** Ensure credentials are correct and the site hasn't changed login methods

2. **For cookies:** Export fresh cookies after logging in to the site

3. **Check cookie expiration:** Re-export cookies regularly

4. **Use browser extraction:**
```powershell
gallery-dl --cookies-from-browser chrome --cookies-from-browser profile-name "URL"
```

### Issue 5: Files Not Downloading to Expected Location

**Solution:** Check the effective configuration:

```powershell
gallery-dl -E "URL"
```

This shows what settings are active for that URL.

### Issue 6: Invalid Path Characters

Windows doesn't allow certain characters in filenames: `\ / : * ? " < > |`

**Solution:** Configure path restrictions in your config:

```json
{
    "extractor": {
        "path-restrict": {
            "\\": "⧹",
            "/": "⧸",
            "|": "￨",
            ":": "꞉",
            "*": "∗",
            "?": "？",
            "\"": "″",
            "<": "﹤",
            ">": "﹥"
        }
    }
}
```

### Issue 7: PowerShell Execution Policy Blocks Scripts

**Solution:** Set execution policy for current user:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### Issue 8: Updating gallery-dl

**For standalone executable:**
1. Download the latest `gallery-dl.exe` from [releases](https://github.com/mikf/gallery-dl/releases/latest)
2. Replace your existing executable

**For pip installation:**
```powershell
py -3 -m pip install -U gallery-dl
```

**Using the built-in updater:**
```powershell
gallery-dl --update
```

### Issue 9: Getting More Information for Debugging

Enable verbose output:

```powershell
gallery-dl -v "URL"
```

Or save output to a log file:

```powershell
gallery-dl -v "URL" 2>&1 | Out-File -FilePath "$env:USERPROFILE\gallery-dl-log.txt"
```

---

## Additional Resources

- **Official Documentation:** https://gdl-org.github.io/
- **Supported Sites:** https://github.com/mikf/gallery-dl/blob/master/docs/supportedsites.md
- **Configuration Options:** https://gdl-org.github.io/docs/configuration.html
- **GitHub Repository:** https://github.com/mikf/gallery-dl
- **Discord Community:** https://discord.gg/rSzQwRvGnE

---

## Quick Reference Commands

| Task | Command |
|------|---------|
| Download single URL | `gallery-dl "URL"` |
| Download to specific folder | `gallery-dl -d "C:\Path\To\Folder" "URL"` |
| Download with authentication | `gallery-dl -u "username" -p "password" "URL"` |
| Use cookies from browser | `gallery-dl --cookies-from-browser chrome "URL"` |
| Download from text file | `gallery-dl -i "urls.txt"` |
| Preview URLs without downloading | `gallery-dl -g "URL"` |
| Show version | `gallery-dl --version` |
| Update gallery-dl | `gallery-dl --update` |
| Check for updates | `gallery-dl --update-check` |
| View available keywords | `gallery-dl -K "URL"` |
| View extractor info | `gallery-dl -E "URL"` |
| Enable verbose logging | `gallery-dl -v "URL"` |

---

*Last updated: January 2026*
