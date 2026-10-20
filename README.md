# Stockra Desktop (Windows)

## Build the .exe installer
1. Install **Node.js LTS** (https://nodejs.org) on a Windows PC with internet access.
2. Unzip this folder anywhere.
3. Double-click **`bin\build-exe.bat`**.
4. Your installer appears in `dist\` as **Stockra Setup 1.0.0.exe**. Run it to install (Desktop + Start-menu shortcuts, custom icon).

### Installer window size
The setup window is built compact and wide-shaped (much shorter than before, with small text). To adjust it, open `scripts/dist.js` and change `SIZE` (8 small = default, 9 a bit larger, 10 larger) or set `WIDE_FONT` to `''` for the standard Windows shape. The wide shape applies to Latin and Cyrillic installer languages (English, French, Spanish, German, ...); Arabic, Chinese, Japanese and similar keep the normal Windows font at the same size. After changing it, run `bin\build-exe.bat` again.

To try the app without installing: double-click **`bin\run-dev.bat`**.

If the build stops with "Cannot create symbolic link", run `build-exe.bat` as Administrator or enable Windows Developer Mode.
Windows SmartScreen may warn about an unsigned app; choose "More info > Run anyway" (a code-signing certificate removes this).

## What happens when the app starts
A splash screen shows the application's activities (inventory, sales, purchases, treasury, debts, warehouses, reports, secure access, activity log) with a progress bar, then the login screen opens.

## Accounts and roles
| Account | Password | Role |
|---|---|---|
| admin | admin123 | Administrator |
| user  | user123  | User |

**Change both passwords at first login.** Only an admin can change passwords (Users page, key button next to each account); regular users have no password-change option.

| Feature | Admin | User |
|---|---|---|
| Items, customers, suppliers, invoices, vouchers, treasury, debts, warehouses, prices, reports, export backup | yes | yes |
| **Delete anything** (items, customers, suppliers, debts/payments, warehouses, invoices, users) | yes | no (can create and edit, never delete) |
| **Reports & profit, Debts, Backup/export, buy prices, margins, stock value** | yes | hidden |
| **Reset (zeroize all data)** | yes | no |
| **Restore from backup / load demo data** | yes | no |
| Users page: create any number of accounts, delete them, change any password | yes | no |
| Activity log page | yes | no |

The bottom bar always shows **"Admin connected"** (red) or **"User connected"** (blue), the account name, the live Windows clock and a Logout button.

## Activity log
Every action (login/logout, failed logins, opening pages, saving or deleting records, invoices, vouchers, treasury, debts, transfers, price changes, backup export/restore, reset, printing, denied access attempts) is written with the **Windows system date and time**.

* File: `%APPDATA%\Stockra\activity.log` (tab-separated: time, user, role, action, details)
* Admins can read and export it (CSV) from the **Activity log** page.

## Data location and security notes
* Business data and accounts live in the app's local storage under `%APPDATA%\Stockra`. Export a backup regularly.
* Passwords are stored hashed, but this is a local desktop app: someone with full access to the Windows account can still read or edit the files. Use a protected Windows account and BitLocker for sensitive data.

## No Windows PC / no installs? Build in the cloud (GitHub Actions)
1. Create a free GitHub repository and upload the *contents* of this folder (keep `.github`).
2. Open the **Actions** tab, run **Build Stockra** (or just push).
3. Download **Stockra-Windows-Installer** (the .exe) and **Stockra-Android-APK** from the run's Artifacts.

## Android
* **Quickest test:** host `pwa/` on any HTTPS site (GitHub Pages, Netlify), open it in Chrome on the phone, menu > *Install app*. Works offline; backup export works.
* **Real APK, nothing to install:** double-click `bin\build-apk.bat`. It downloads portable Node, JDK 17 and the Android SDK into a `.tools` folder (about 1.5 GB, first run only, internet required) and creates **`Stockra.apk`** in this folder. Copy it to the phone and install it after allowing unknown sources. Delete `.tools` afterwards to free the space.
* If the APK build fails, the window shows the reason and a full log is saved as `build-apk.log` in this folder. If the folder path is long or has non-English characters, move the Stockra folder to `C:\Stockra` and run again.
* The APK needs Android 6.0 or newer (the build sets this automatically).
* APK limits: printing and file download (backup export) do not work inside the Android WebView; use the PWA for those. Data stays on the phone.

## All platforms at a glance
| Platform | Result | How | Needs |
|---|---|---|---|
| Windows | `Stockra Setup 1.0.0.exe` | `bin\build-exe.bat` | Node.js |
| Android | `Stockra.apk` | `bin\build-apk.bat` | nothing (portable tools) |
| Windows + Android | both | `bin\build-all.bat` | as above |
| macOS | `Stockra-1.0.0-universal.dmg` | cloud build, or `bin/build-mac.command` on a Mac | Apple hardware (or GitHub cloud) |
| iPhone | unsigned `.ipa` | cloud build, or `bin/build-ios.command` on a Mac | Apple hardware (or GitHub cloud) |
| iPhone, no build | home-screen app | open the `pwa` folder's hosted URL in Safari > Share > Add to Home Screen | HTTPS hosting |

Apple only allows macOS and iOS apps to be built on Apple hardware, so Windows cannot produce them. The GitHub cloud build (Actions) runs on Apple machines for you and produces the `.dmg` and `.ipa` as downloads.
* **.dmg** is unsigned: on first launch right-click the app > Open.
* **.ipa** is unsigned: install it with a sideloading tool such as AltStore or Sideloadly (free Apple ID, re-sign every 7 days), or sign it with a paid Apple Developer account for TestFlight/App Store.

## Printing and staying signed in
* **Windows:** Print opens the Windows print dialog; the PDF button saves the receipt as a PDF and opens it.
* **Android APK:** Print opens Android's print screen (choose a printer or "Save as PDF"). Rebuild the APK after updating.
* **Sign-in:** once signed in, the app stays signed in after you close it. Only the Logout button (or an admin changing/deleting that account's password) asks for credentials again.

## Installer look
The installer opens with a welcome page showing the Stockra logo, a preview of the home screen and the feature list. To use real screenshots, replace `build/installerSidebar.bmp` (984x1884, 24-bit BMP, same 164:314 ratio). If the build ever complains about `build/installer.nsh`, delete that file: the installer still builds, with the images but the default welcome text.

If the installer window looks wrong on your screen (layout shifted), remove the `customHeader` block from `build/installer.nsh` and rebuild.

## Installer language
The installer follows the Windows language of the PC. Buttons and standard texts are translated by Windows/NSIS; the Stockra feature list on the welcome page is translated for English, French, Arabic, Spanish, German, Italian, Portuguese (PT/BR), Turkish, Russian and Dutch (other languages fall back to the standard text). To change a translation, edit the `LangString STK_FEATURES` lines in `build/installer.nsh` and keep the file saved as UTF-8 with BOM.

## Installer language
The installer follows the Windows display language automatically (26 languages, including French, Arabic, Spanish, German, Italian, Portuguese, Turkish, Russian, Chinese, Japanese and Korean). Regional variants such as French (Morocco), French (Canada) or Arabic (Egypt) also work. To change the welcome text, edit `build/installer.nsh` (keep the file UTF-8 with BOM). The sidebar and header images are `build/installerSidebar.bmp` (984x1884) and `build/installerHeader.bmp` (300x114).

---

# v1.1 — Backups, Google Drive, internet and automatic updates

## Look
* **Installer welcome page:** logo on top with a green check beside it, then a glimpse of the real home-screen tiles (`build/installerSidebar.bmp`, 984x1884).
* **Start-up screen** (Windows, and the dark launch screen on Android/PWA): dark background that matches the app, no more pale blue.

## Backups (Backup page, admin)
* **Local, automatic:** every change is saved (after ~20 s of quiet) as `stockra-YYYYMMDD-HHMMSS-auto.json`; the newest 30 are kept.
  * Windows: folder `Documents\Stockra Backups` (change it or open it from the page). Android: inside the app (removed if the app is uninstalled) — use *Share / save file* to copy one out, and Google Drive for real protection.
* **Manual:** *Back up now* and *Save as…* (Windows) / *Share / save file* (Android).
* **Restore** from any listed copy or a file. A safety copy of the current data is saved first.

## Google Drive (one-time setup, about 5 minutes)
1. console.cloud.google.com → create a project → enable **Google Drive API**.
2. **OAuth consent screen:** User type *External*, then press **Publish app (In production)**. If you leave it in *Testing*, Google ends the connection after 7 days. The scope used (`drive.file`) is non-sensitive, so no Google review is needed.
3. **Credentials → Create credentials → OAuth client ID → type "TVs and Limited Input devices".**
4. In Stockra → Backup → Google Drive, paste the Client ID and Client Secret, press **Connect**, open the link, type the code, approve.
Backups go to a **Stockra Backups** folder in your Drive (the app only sees files it created). Upload is automatic whenever the internet is available (at most every 10 minutes) and retried when the connection returns. The last 30 automatic copies are kept.

## Internet and automatic updates (no more USB / Bluetooth APKs)
The bottom bar shows **Online / Offline**. The app works offline and catches up when the connection returns.

**One-time setup:** put the project in a **public** GitHub repository (the releases are what users download), keep `.github`.

**To publish an update for everyone:** bump nothing by hand, just tag:
```
git tag v1.2.0
git push --tags
```
GitHub Actions then builds the Windows installer, the APK and the web bundle, and publishes them as a release.
* **Windows:** installed copies check at start and every 4 hours, download in the background and install when the app is closed (or from *Restart and install*).
* **Android:** the app (all screens and logic) is updated over the air from the release (`www.zip`), checked at start, every 4 hours, when the phone comes back online, and when the app is reopened. The new version applies the next time the app starts, or immediately with *Restart and install*.
* **When an APK is still needed:** only if the native layer changes (a new Android plugin or permission). Then edit `mobile-app/NATIVE_VERSION` to the new version before tagging; phones on an older APK see a *Download* button for the new one. Android never allows an app to install a new APK silently outside Google Play — that is a system rule.
* **PWA:** refreshes itself when opened with internet.
* If you build the APK locally with `build-apk.bat`, set the update address once on the Backup page → *Update file URL*: `https://github.com/OWNER/REPO/releases/latest/download/update.json`

Not changed: data is still stored on each device. Backups and updates use the internet; the stock data itself is not synchronised live between devices.

## Installer size and images
- `npm run dist` (and the GitHub workflow) go through `scripts/dist.js`, which makes the Windows setup window about 37% larger. Edit `SIZE` at the top of that file (8 = standard, 10 = about 25% larger, 11 = about 37% larger). If the enlarging step fails for any reason the build continues with the standard size.
- `build/installerSidebar.bmp` is 225x432 pixels, the exact size of the picture area in the larger window, so Windows never has to shrink it. If you change `SIZE`, resize the picture to about (SIZE/8 x 164) by (SIZE/8 x 314) pixels.
- The app icon (`build/icon.png`, `build/icon.ico`, `pwa/icon-*.png`) shows the green check badge attached at the bottom-right corner.

## Google Drive one-click sign-in (set up once by the developer)
In Google Cloud Console (Drive API enabled, scope `drive.file`) create OAuth clients and paste the IDs in `GCFG` near the top of the Google Drive section of `app/index.html` (then copy to `pwa/` and `mobile-app/www/`):
- **Desktop app** client -> `GCFG.desktop.id` + `.secret` (Windows/Mac)
- **Android** client (package `com.stockra.app`, enable "custom URI scheme") -> `GCFG.android.id`
- **Web** client (authorized JS origin = your PWA URL) -> `GCFG.web.id`
Users then just press "Connect Google Drive", pick their account, and backups run automatically. If an ID is empty, the old manual Client ID screen is shown.

## OTA updates (self-hosted, creator-only admin)
1. Deploy `ota-server/` (see its README) with your own `ADMIN_PASSWORD`.
2. Once: `node scripts/set-ota-url.js https://your-ota-server` then rebuild the apps (users need this one new install; after that updates are automatic).
3. To ship an update: `OTA_PASSWORD=... node scripts/push-update.js 1.3.0 "What's new"` (web/UI changes for Android, no reinstall).
   Add `--apk path/Stockra.apk` for native changes, `--win dist` for the Windows installer (latest.yml + .exe + .blockmap). Or use the web panel at `https://your-ota-server/admin`.

## Android signing
All APKs are signed with `mobile-app/signing/stockra.jks` (fingerprints in `Stockra-Android.sha`), so the SHA-1 never changes and updates install over old versions. Keep this file private and back it up. Phones with an older APK (other signature) must uninstall once.

## Free OTA with GitHub (no server)
1. Public repo `stockra-ota` (with a README so `main` exists). 2. `winget install GitHub.cli` then `gh auth login`.
3. `node scripts/set-ota-url.js github:YOUR-NAME/stockra-ota`, THEN build the apps.
4. Push: `node scripts/push-update.js 1.3.3 "notes" [--apk file] [--win dist]` (no password needed, GitHub login is the admin).

## Over-the-air updates (v1.3.6+)
The update address is built into the app (`UPDATE_URL` in `mobile-app/www/index.html`); users just tap **Check for updates** then **Restart and install**. There is no URL field anymore.
To publish a new web update: `node scripts/make-ota-zip.js 1.3.7 "notes"` (run `npm install` inside `mobile-app` once first so `lib/zxing.min.js` exists), then upload `ota/www-1.3.7.zip` and `ota/update.json` to the stockra-ota repo.
