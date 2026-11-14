# Orbit Habit Tracker (Electron Desktop)

A cross-platform desktop Habit Tracker application built with **Electron**, pure **HTML5**, **CSS3 (Glassmorphism)**, and **Vanilla JavaScript**. Designed with zero external database dependencies, complete offline local file persistence, and platform-adaptive custom frameless titlebars.

---

## ✨ Features & Architecture Highlights

1. **Enterprise Security Posture**:
   - `contextIsolation: true`
   - `nodeIntegration: false`
   - `sandbox: true`
   - Hardened `webPreferences` with strict Content Security Policy (CSP).
   - Safe IPC communication channels exposed exclusively via `contextBridge`.

2. **Cross-Platform Frameless Titlebar**:
   - macOS: Native traffic lights automatically positioned at `{ x: 18, y: 16 }` with appropriate left padding clearance (`padding-left: 72px`).
   - Windows & Linux: Native window controls are replaced with custom glassmorphic Minimize, Maximize / Restore, and Close buttons with standard Windows hover behaviors (red `#e81123` close hover, dynamic maximize/restore SVG toggle).
   - Native drag region handling (`-webkit-app-region: drag` / `no-drag` on interactive controls).

3. **Zero-Dependency Local JSON Storage Engine**:
   - Uses Node.js native `fs` and Electron's `app.getPath('userData')`.
   - **Atomic Write Strategy**: Writes payloads to a temporary file (`orbit-habits.tmp`) and atomically renames to `orbit-habits.json` to prevent file corruption during abrupt shutdowns or system crashes.
   - **Automatic Backup & Recovery**: Automatically preserves previous state to `orbit-habits.backup.json` on write.
   - **Pre-seeded Dataset**: Seeds starter habits with 28 days of deterministic history on first boot.

4. **Modern Dark-Mode Glassmorphic UI Shell**:
   - Deep slate and navy backdrop (`#070b12`, `#0e1424`) with subtle ambient radial glow accents.
   - Smooth CSS variables and frosted backdrop filters (`backdrop-filter: blur(24px) saturate(160%)`).
   - Inter / system typography stack with crisp `-webkit-font-smoothing`.
   - **Three Main Views**:
     - **Dashboard**: Today's active habits, circular checkboxes with instant streak counters, 4 stat cards, and an interactive "New Habit" modal.
     - **Analytics**: 28-day consistency activity heatmap matrix (GitHub/Linear-style), category mastery breakdowns, and top streaks leaderboard.
     - **Settings**: Live local file engine diagnostics (real storage path, file size in KB, platform detection, force sync button, and JSON export).

---

## 📁 Project Structure

```
Orbit/
├── package.json              # Electron & electron-builder configuration
├── main.js                   # Main Process (BrowserWindow, lifecycle, IPC handlers)
├── preload.js                # ContextBridge security layer exposing safe APIs
├── storage.js                # Local JSON atomic file storage engine
├── index.html                # Semantic UI shell with embedded vector icons
├── styles.css                # Glassmorphic dark theme stylesheet
├── renderer.js               # Vanilla JavaScript frontend controller
├── assets/
│   └── icons/
│       └── icon.svg          # High-resolution vector icon
└── README.md                 # Documentation
```

---

## 🚀 Getting Started

### 1. Prerequisites
- **Node.js**: v18+ (tested on v24)
- **npm**: v9+ (tested on v11)

### 2. Run in Development Mode
```bash
npm start
```

### 3. Package and Build Distributables
To build distributables for your current platform:
```bash
# Package into unpacked directory (fast test)
npm run pack

# Build complete installers (DMG/Zip on macOS, NSIS on Windows, AppImage on Linux)
npm run dist

# Target specific operating systems
npm run dist:mac
npm run dist:win
npm run dist:linux
```

---

## 🔒 IPC API Reference (`window.electronAPI`)

| Method | Type | Channel | Description |
| :--- | :--- | :--- | :--- |
| `platform` | Property | — | Returns `process.platform` (`'darwin'`, `'win32'`, `'linux'`). |
| `loadData()` | Async | `load-data` | Loads local habit JSON data or seeds initial schema if not present. |
| `saveData(data)` | Async | `save-data` | Atomically writes data to `userData/orbit-habits.json`. |
| `getStorageStats()` | Async | `get-storage-stats` | Returns file path, size in bytes, and last modified timestamp. |
| `getAppInfo()` | Async | `get-app-info` | Returns version, platform, and `userData` directory. |
| `windowControls.minimize()` | Sync | `window-controls` | Minimizes the BrowserWindow. |
| `windowControls.maximize()` | Sync | `window-controls` | Toggles maximize / unmaximize state. |
| `windowControls.close()` | Sync | `window-controls` | Closes the BrowserWindow. |
| `windowControls.isMaximized()` | Async | `window:is-maximized` | Queries if the window is currently maximized. |
| `windowControls.onStateChange(cb)`| Event | `window:state-changed`| Subscribes to window maximize/restore events. |
