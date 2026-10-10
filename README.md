# amir1200 - GTA VI desktop wallpaper studio (Windows 11)

amir1200 turns your Windows 11 desktop into a GTA VI showcase:

1. **Countdown** to the GTA VI release, drawn straight onto your wallpaper (4 layout presets).
2. **Live Wallpaper**: choose your own video (MP4, WebM, MOV) and play it behind your desktop icons.
3. **GTA VI Gallery**: import your own screenshots and rotate them as the wallpaper with crossfades.

> **Status: IMPLEMENTED, PARTIALLY VERIFIED.** The scene and control panel were run and tested in a real
> browser engine. The Windows-specific C# code was written carefully but **has not been compiled or run**
> (the build environment had no .NET SDK and no Windows). See `docs/BUILD-REPORT.md` for exactly what was and was not tested.

## Release date
Default target: **19 November 2026, 00:00 in your PC's time zone**. The date comes from press reports of Rockstar's
announcement; the exact launch time has not been confirmed. Change both any time under *Countdown*.

## Quick start
See `docs/INSTALL-WINDOWS11.md` (written for beginners). In short: install the .NET 10 SDK, then in PowerShell run
`.\build\build.ps1`, then double-click `Start-amir1200.bat`.

## Architecture
| Part | Folder | What it does |
|---|---|---|
| Core logic | `src/Amir1200.Core` | Countdown and time-zone maths, settings model + validation + atomic saving, slideshow ordering, media checks. No Windows code, fully unit-tested. |
| Windows app | `src/Amir1200.App` | WPF shell, tray icon, desktop integration (Win32), one WebView2 wallpaper window per monitor, slideshow clock, power/lock/display handling. |
| Scene and panel | `src/Amir1200.App/Scene` | `scene.html/css/js` = the wallpaper (video, gallery, countdown). `ui.html/css/js` = the control panel. Both are plain HTML so the preview you see is exactly what the desktop shows. |
| Tests | `tests/` | xUnit tests for Core, Node tests for the countdown maths. |
| Tools | `build/` | `build.ps1` (build), `Verify-amir1200.ps1` (PC check). |

**How the wallpaper gets behind your icons:** the app asks the shell to create the WorkerW layer, then re-parents each
wallpaper window under it (Windows 10 / early Windows 11) or under Progman just below the icon layer (Windows 11 24H2+).
If Windows refuses, amir1200 says so and falls back to a bottom-most window (icons may be hidden; stop it from the tray).

**Why WebView2:** one GPU-composited surface can hold video, slideshow and countdown together, so the countdown can sit
*over* the video without window-layering tricks. Cost: roughly 100-200 MB RAM while running, and video codecs are
limited to what Chromium/Windows decode (H.264 MP4 and WebM are safe).

## Dependencies
| Package | Why | License |
|---|---|---|
| Microsoft.Web.WebView2 1.0.2903.40 | Hosts the scene (video/gallery/countdown). Needs the Evergreen WebView2 Runtime (preinstalled on Windows 11). | Microsoft (redistributable) |
| xunit 2.9.3, Microsoft.NET.Test.Sdk, xunit.runner.visualstudio | Tests only | Apache-2.0 / MIT |

No other third-party packages. Logging is a small built-in file logger (planned Serilog dependency was dropped to keep the app lean).

## Known limitations
- Same wallpaper on all monitors (or main monitor only); independent wallpapers per monitor are **not** implemented.
- Dark theme only. Light theme is not implemented.
- No installer is provided; amir1200 is a portable folder. (Windows "uninstall" = delete the folder; settings live in `%LocalAppData%\amir1200`.)
- Gallery thumbnails use the full image file (decoded lazily); very large folders (thousands of 4K images) will be slow to preview.
- Seamless video looping relies on the browser engine's loop; a tiny hitch is possible on some files.
- The GTA VI artwork is **not** included. Import your own screenshots.

خطا از اینه که دستور را بدون . اول آن نوشته‌ای. باید .\build\build.ps1 باشد، نه \build\build.ps1. نقطه یعنی «از همین پوشه‌ای که الان توش هستم». بدون نقطه، PowerShell دنبال مسیر \build\build.ps1 در ریشه درایو می‌گردد و پیدایش نمی‌کند.
قدم‌ها (همه در PowerShell):
فایل amir1200.zip را Extract کن، مثلاً در C:\amir1200. داخلش یک پوشه دیگر به اسم amir1200 هست. داخل همان پوشه‌ی داخلی را باز کن، همان که فایل‌های build، src و Start-amir1200.bat را دارد.
در File Explorer روی نوار آدرس همان پوشه کلیک کن، بنویس powershell و Enter بزن. PowerShell داخل همان پوشه باز می‌شود.
اول ببین پوشه درست است:
powershell
   dir build
باید build.ps1 و Verify-amir1200.ps1 را ببینی. اگر ندیدی، داخل پوشه اشتباهی هستی. یک پوشه بالاتر یا پایین‌تر برو.
4. بعد دستور را با نقطه بزن:
powershell
   .\build\build.ps1 -Shortcut
اگر بعدش خطای «running scripts is disabled» دیدی، اول این را بزن (فقط برای همین پنجره اعمال می‌شود) و دوباره دستور بالا را اجرا کن:
powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
اگر dotnet --version هم عدد ۱۰ نشان نداد، .NET 10 SDK را نصب کن و PowerShell را ببند و دوباره باز کن. هر خطای دیگری دیدی، اولین خط قرمز را همین‌جا بفرس






