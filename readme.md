<p align="center">
	<img src="https://www.ortussolutions.com/__media/coldbox-185-logo.png">
	<br>
	<img src="https://www.ortussolutions.com/__media/wirebox-185.png" height="125">
	<img src="https://www.ortussolutions.com/__media/cachebox-185.png" height="125" >
	<img src="https://www.ortussolutions.com/__media/logbox-185.png"  height="125">
</p>

<p align="center">
	<a href="https://github.com/ColdBox/coldbox-platform/actions/workflows/snapshot.yml"><img src="https://github.com/ColdBox/coldbox-platform/actions/workflows/snapshot.yml/badge.svg" alt="ColdBox Snapshots" /></a>
	<a href="https://forgebox.io/view/coldbox"><img src="https://forgebox.io/api/v1/entry/coldbox/badges/downloads" alt="Total Downloads" /></a>
	<a href="https://forgebox.io/view/coldbox"><img src="https://forgebox.io/api/v1/entry/coldbox/badges/version" alt="Latest Stable Version" /></a>
	<a href="https://forgebox.io/view/coldbox"><img src="https://img.shields.io/badge/License-Apache2-brightgreen" alt="Apache2 License" /></a>
</p>

<p align="center">
	Copyright Since 2005 ColdBox Platform by Luis Majano and Ortus Solutions, Corp
	<br>
	<a href="https://www.coldbox.org">www.coldbox.org</a> |
	<a href="https://www.ortussolutions.com">www.ortussolutions.com</a>
</p>

----

# ⚡ ColdBox BoxLang Desktop App Template

Production-ready desktop application template built with ColdBox, BoxLang MiniServer, Electron, Vite, Alpine.js, and Bootstrap.

This template gives you a full desktop shell around a local BoxLang web application. Electron owns the native desktop lifecycle, while BoxLang MiniServer serves the app locally and the BrowserWindow loads it like a native desktop experience.

## What This Template Gives You

- ColdBox application structure inside a desktop runtime
- Electron shell with window lifecycle, menu, tray, and shortcuts
- Local BoxLang MiniServer process managed by Electron
- Vite build pipeline for JS and SCSS assets
- Electron Forge packaging for macOS, Windows, and Linux
- Packaged MiniServer runtime under `runtime/bin` and `runtime/lib`
- Development and production runtime config split via `.boxlang-dev.json` and `.boxlang.json`

## Project Structure

```text
app/
  config/
  electron/
  handlers/
  interceptors/
  layouts/
  models/          (includes ViteHelper.bx)
  modules/
  views/

public/
  Application.bx
  index.bxm
  includes/

resources/
  assets/
    fonts/
    js/
    scss/

runtime/
  Package.bx
  VERSION
  bin/
  lib/
  modules/

miniserver.json
forge.config.cjs
vite.config.mjs
```

## Architecture

### Runtime Model

1. Electron starts from `app/electron/Main.js`
2. `Main.js` wires the desktop modules
3. `app/electron/BoxLang.js` starts BoxLang MiniServer using `miniserver.json`
4. MiniServer loads the ColdBox app from `public/`
5. Electron waits for the local URL to become ready, then loads it in a `BrowserWindow`

### Main Files

- `app/electron/Main.js`: Electron bootstrap and module wiring
- `app/electron/BoxLang.js`: MiniServer process management, readiness checks, restart strategy
- `app/electron/AppMenu.js`: app menu
- `app/electron/TrayMenu.js`: tray integration
- `app/electron/Shortcuts.js`: global keyboard shortcuts
- `app/config/Coldbox.bx`: ColdBox configuration
- `app/config/Router.bx`: routes
- `public/Application.bx`: web-facing bootstrap
- `public/index.bxm`: default entry point
- `miniserver.json`: MiniServer host, port, web root, and dev runtime config
- `runtime/Package.bx`: MiniServer runtime packager
- `vite.config.mjs`: frontend asset build config
- `forge.config.cjs`: Electron Forge packaging config

## Requirements

- BoxLang OS runtime
- CommandBox
- Node.js
- Java 21+

Important: This template packages the BoxLang MiniServer runtime, but it does not package a JRE/JDK. Java 21+ must already be available on the host machine.

## Quick Start

### Install Dependencies

```bash
box install
npm install
```

### Run in Development

```bash
npm run dev
```

This starts Vite and then launches Electron once the dev server is available.

## Build & Package

### Vite Build

Builds frontend assets and outputs them to `public/includes/resources/`:

```bash
npm run build
```

### Start Production Build

Runs the built application without development mode:

```bash
npm run prod
```

### Generate Icons

Generates platform-specific icon files from the PNG source iconset:

```bash
npm run generate:icons
```

This creates:
- `public/includes/icon.icns` - macOS
- `public/includes/icon.ico` - Windows
- `public/includes/icon.png` - Generic fallback

### Package Application

Packages the application as a distributable for your platform:

```bash
# Current platform
npm run package

# Specific platform
npm run package:mac
npm run package:win
npm run package:linux

# Full packaging (downloads MiniServer runtime first)
npm run package:full
```

### Package MiniServer Runtime

Downloads and extracts the BoxLang MiniServer runtime into `runtime/bin/` and `runtime/lib/`:

```bash
npm run package:miniserver
```

The version is controlled by `.bvmrc`.

## Electron Forge

This template uses **Electron Forge** for building and packaging desktop applications.

### Platform-Specific Outputs

**macOS (`npm run package:mac`)**
- Output: `dist/Electron/` directory
- Files: `ColdBox-BoxLang-Desktop-x.x.x-arm64.dmg`, `.zip`
- Code signing: Requires `CSC_*` or `APPLE_*` environment variables

**Windows (`npm run package:win`)**
- Output: `dist/Electron/` directory
- Files: NSIS installer `.exe`, `.msi`, `.zip` (Squirrel.Windows)
- Icon: Reads from `public/includes/icon.ico` (generated by `npm run generate:icons`)
- Requires: Node.js 20 LTS (cross-zip dependency compatibility)

**Linux (`npm run package:linux`)**
- Output: `dist/Electron/` directory
- Files: `.deb`, `.rpm`, `.flatpak`, `.zip`
- Icon: Uses `public/includes/icon.png`

### Configuration

`forge.config.cjs` controls all packaging behavior:

- `asar`: **Always false** (MiniServer must be spawned as a real filesystem executable)
- `icon`: Resolves to `public/includes/icon` (Forge appends platform-specific extensions)
- `makers`: Array of active makers (dmg, zip for macOS; squirrel, zip for Windows; deb, rpm, flatpak for Linux)
- `postMake`: Hook that copies unsigned-build helper scripts into ZIP artifacts

### Icon Resolution

Forge resolves icon filenames by appending platform-specific extensions:

| Platform | Icon File              | Required | Generated by          |
|----------|------------------------|----------|------------------------|
| macOS    | public/includes/icon.icns | Yes   | npm run generate:icons |
| Windows  | public/includes/icon.ico  | Yes   | npm run generate:icons |
| Linux    | public/includes/icon.png  | No    | Included in repo       |

**Missing icons will cause build failures on the target platform.**

## Icons

Icons are stored in `public/includes/icon.iconset/`:

```text
public/includes/
└── icon.iconset/
    ├── icon_16x16.png
    ├── icon_32x32.png
    ├── icon_64x64.png
    ├── icon_128x128.png
    ├── icon_256x256.png
    ├── icon_512x512.png
    └── icon_1024x1024.png
```

Run `npm run generate:icons` to create:
- `public/includes/icon.icns` (macOS)
- `public/includes/icon.ico` (Windows)

These generated files **should be committed** to your repository to ensure faster CI builds and consistent packaging.

## Configuration

### MiniServer Configuration

**`miniserver.json`**

Controls the local development server:

```json
{
  "app": {
    "name": "ColdBox BoxLang Desktop",
    "logdir": "./logs"
  },
  "server": {
    "port": 59700,
    "host": "127.0.0.1",
    "webroot": "./public"
  },
  "warmupUrl": "http://127.0.0.1:59700/",
  "healthCheckUrl": "http://127.0.0.1:59700/health"
}
```

### Runtime Configuration

**`.boxlang-dev.json`**

Development-only overrides (loaded by `miniserver.json`):

```json
{
  "boxlang": {
    "compiler": {
      "nullChecking": true
    },
    "runtime": {
      "allowImplicitStructKeyAccess": true
    }
  }
}
```

**`.boxlang.json`**

Production configuration (used in packaged builds):

```json
{
  "boxlang": {
    "compiler": {
      "nullChecking": false
    }
  }
}
```

## Frontend Assets

The Vite pipeline compiles assets to `public/includes/resources/`:

```text
resources/
└── assets/
    ├── fonts/           → public/includes/resources/fonts/
    ├── js/
    │   └── app.js       → public/includes/resources/app-[hash].js
    └── scss/
        └── app.scss     → public/includes/resources/app-[hash].css
```

### In Your Views

Use the `ViteHelper` to load assets automatically:

```xml
<!-- app/layouts/Main.bxm -->
<head>
    #viteHelper.asset( "resources/assets/js/app.js" )#
    #viteHelper.asset( "resources/assets/scss/app.scss" )#
</head>
```

The helper automatically:
- **Development**: Loads from Vite dev server (HMR enabled)
- **Production**: Loads compiled & hashed assets from `public/includes/resources/`

## Development Scripts

### Key npm Scripts

```bash
npm run dev              # Start Vite dev server + Electron (development)
npm run build           # Build Vite assets for production
npm run start           # Start Electron only (assumes Vite already running)
npm run prod            # Production build + production runtime config
npm run package         # Package current platform (create installers/DMG)
npm run package:mac     # Package macOS DMG + ZIP
npm run package:win     # Package Windows installer + ZIP
npm run package:linux   # Package Linux deb/rpm/flatpak + ZIP
npm run package:full    # Download MiniServer runtime + package current platform
npm run generate:icons  # Generate platform-specific icon files
```

### Development Workflow

```bash
# Terminal 1: Start development
npm run dev

# This starts:
# 1. Vite dev server at http://127.0.0.1:3000
# 2. BoxLang MiniServer at http://127.0.0.1:59700
# 3. Electron window loading http://127.0.0.1:59700

# Edit your .bxm templates or .bx handlers → immediate reload
# Edit your JS/SCSS in resources/assets/ → HMR updates in window
```

### Production Build Workflow

```bash
# Build Vite assets
npm run build

# Test production build locally
npm run prod

# Package for distribution
npm run package:full    # If MiniServer runtime needs re-downloading
npm run package        # If MiniServer runtime already packaged
```

## Troubleshooting

### MiniServer Fails to Start

**Symptom**: Electron window is blank or shows "Connection refused"

**Likely Causes**:
- Java 21+ not installed: `java --version`
- Port 59700 already in use: `lsof -i :59700`
- BoxLang runtime not packaged: `npm run package:miniserver`

**Solutions**:
```bash
# Verify Java is installed
java --version

# Kill process on port 59700
lsof -i :59700 | grep LISTEN | awk '{print $2}' | xargs kill -9

# Re-package MiniServer runtime
npm run package:miniserver

# Restart development
npm run dev
```

### Permission Denied on macOS/Linux

**Symptom**: `runtime/bin/boxlang-miniserver: Permission denied`

**Solution**:
```bash
chmod +x runtime/bin/boxlang-miniserver
npm run dev
```

### Icon Error on Windows

**Symptom**: "Fatal error: Unable to set icon" during `npm run package:win`

**Cause**: `public/includes/icon.ico` missing

**Solution**:
```bash
npm run generate:icons
npm run package:win
```

### Asset 404 in Development

**Symptom**: CSS/JS not loading in dev-mode window

**Likely Causes**:
- Vite dev server not started (verify port 3000)
- ViteHelper not loading assets correctly
- publicDir setting disabled in vite.config.mjs

**Solutions**:
```bash
# Ensure both servers are running
npm run dev

# In a separate terminal, verify Vite server
curl http://127.0.0.1:3000

# Check ViteHelper.bx is loaded in your layout
grep -r "include.*ViteHelper" app/
```

### Node.js Version Issues

**Symptom**: Windows build fails with "fs.rmdir recursive not supported"

**Cause**: cross-zip (Forge dependency) requires Node.js 20 LTS; Node 22+ removed fs.rmdir

**Solution**:
```bash
# Use Node 20 LTS
nvm use 20
npm run package:win
```

## Learning Resources

- [ColdBox Documentation](https://coldbox.ortusbooks.com/)
- [BoxLang Documentation](https://boxlang.ortusbooks.com/)
- [Electron Documentation](https://www.electronjs.org/docs)
- [Electron Forge Documentation](https://www.electronforge.io/)
- [Vite Documentation](https://vitejs.dev/)

---

<div align="center">
  Made with ❤️ by <a href="https://www.ortussolutions.com">Ortus Solutions, Corp</a>

  <sub>
    THE DAILY BREAD 🍞 Give us our daily bread and forgive us our digital sins, as we forgive those who code against us.
  </sub>
</div>
