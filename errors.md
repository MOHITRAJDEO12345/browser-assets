# EduCode Lock-Down Browser Error Codes & Solutions

This document serves as the single source of truth for all standardized error codes (`ERR_*`) emitted by the EduCode browser. If a student or invigilator encounters one of these error codes, use this sheet to diagnose and resolve the issue.

## 📋 Security & Access Errors

| Error Code | Trigger Condition | Technical Root Cause | Step-by-Step Resolution Path |
|---|---|---|---|
| **`ERR_FATAL_PANIC_00`** | Global Panic Hook | An unhandled system exception occurred (e.g., OS failure, memory corruption, broken system API). The app immediately terminated to prevent unpredictable behavior. | **1.** Locate the log at `%LOCALAPPDATA%\theeducode\theeducode_debug.log` (or `C:\ProgramData\theeducode\theeducode_debug.log`).<br>**2.** Provide this log to IT support for analysis.<br>**3.** Restart the device. |
| **`ERR_ADMIN_DENIED_01`** | `is_admin() == false` | The application was launched without UAC administrative privileges. The browser cannot apply its mandatory registry security guards (like disabling Task Manager). | **1.** Right-click the EduCode shortcut and select "Run as Administrator".<br>**2.** Ensure the logged-in Windows user account has permission to elevate to Admin. |
| **`ERR_DEBUGGER_DETECTED_02`** | Debugger instrumentation | A debugger (like x64dbg or WinDbg) attempted to attach to the Tauri application process, likely to reverse-engineer or manipulate exam logic. | **1.** Close all development tools and debuggers.<br>**2.** Ensure no kernel-level debug mode is enabled via `bcdedit`. |
| **`ERR_INTEGRITY_FAIL_03`** | `verify_sidecar_integrity()` | The hash of the `process_killer.exe` sidecar does not match the embedded secure SHA-256 hash. The binary was tampered with, corrupted, or replaced. | **1.** Completely uninstall the EduCode Browser.<br>**2.** Download a fresh, untampered installer from the official portal.<br>**3.** Ensure antivirus did not quarantine the binary. |
| **`ERR_UNAUTHORIZED_ACTIVITY_13`** | Sidecar unexpectedly killed | The background `process_killer.exe` sidecar was forcefully terminated mid-exam. *(Note: EDRs may aggressively flag the filename 'process_killer.exe'. Process suspension via NtSuspendProcess is a known unmitigated evasion).* | **1.** Add an exclusion to Windows Defender or your third-party antivirus, which may be aggressively quarantining or killing the sidecar. |
| **`ERR_CORRUPT_ASSET_12`** | `validate_checksums()` fails | The offline compiler assets (Python, Java, C++) or embedded result persistence layer (SQLite) have been manually corrupted, replaced, or deleted from the local AppData folder. | **1.** Run the Uninstaller and select **Level 4: The Deep Nuke** to wipe the corrupted 150MB compilers.<br>**2.** Re-run the installer while connected to the internet to freshly download compilers. |

## 💻 Environment & Hardware Errors

| Error Code | Trigger Condition | Technical Root Cause | Step-by-Step Resolution Path |
|---|---|---|---|
| **`ERR_VM_DETECTED_04`** | `is_vm() == true` | The student is attempting to run the browser inside a Virtual Machine (VMware, VirtualBox) or Sandbox, likely to bypass screen capture protections. *(Note: Hyper-V and WSL2 evasions are documented deferred limitations).* | **1.** Close the Virtual Machine.<br>**2.** Run the application natively on the host Windows Operating System. |
| **`ERR_REMOTE_DESKTOP_05`** | `is_remote_session()` | An active Remote Desktop (RDP) session was detected, violating proctoring constraints. *(Note: Third-party local-session casting tools like AnyDesk or Parsec may evade this and must be mitigated by active proctor monitoring).* | **1.** Disconnect all remote desktop clients.<br>**2.** Take the exam physically on the local machine. |
| **`ERR_MULTIPLE_MONITORS_10`** | `DisplayMonitor` count `> 1` | The student has connected a second monitor or screen casting device, violating the single-screen focus policy. *(Note: DXGI Desktop Duplication and Miracast may evade hardware enumeration).* | **1.** Unplug all external monitors.<br>**2.** Disable any software-based screen casting (like Windows Wireless Display). |
| **`ERR_BLUETOOTH_ACTIVE_15`** | `is_bluetooth_active()` | Bluetooth is currently enabled, which poses a severe risk for unauthorized hidden communication devices or earpieces. | **1.** Turn off Bluetooth entirely in Windows Settings.<br>**2.** Relaunch the application. |

## 🌐 Network & System Requirements Errors

| Error Code | Trigger Condition | Technical Root Cause | Step-by-Step Resolution Path |
|---|---|---|---|
| **`ERR_NO_INTERNET_09`** | Connectivity probes fail | The browser cannot reach the EduCode cloud portal or verification servers via HTTP. | **1.** Check WiFi/Ethernet connection.<br>**2.** Ensure no captive portal (like a hotel or library login) is blocking external HTTP connectivity probes. |
| **`ERR_NET_TIMEOUT_16`** | Session key fetch fails | The browser was unable to fetch the dynamic authentication session key from the backend after 5 retry attempts (15 seconds total). Typically caused by an unstable network connection immediately after an OTA updater restart. | **1.** Check your internet connection.<br>**2.** Restart the browser. |
| **`ERR_PROXY_DETECTED_08`** | `is_proxy_enabled()` or `is_vpn_active()` | A system-level Proxy or hardware/virtual VPN adapter (e.g., WireGuard, TAP, Tunnel) is active, which can be used to bypass IP-based exam restrictions or intercept traffic. *(Note: VPN driver renaming is a known deferred evasion due to Constrained Language Mode limits).* | **1.** Disable all VPN clients and close their system tray icons.<br>**2.** Turn off "Use a proxy server" in Windows Network Settings.<br>**3.** Remove or disable virtual TAP adapters in Device Manager. |
| **`ERR_INSUFFICIENT_DISK_06`** | Free Space `< 500 MB` | The system drive lacks the minimum 500 MB required to safely extract offline compilers and log student data. | **1.** Clear out unused files or empty the Recycle Bin to free up at least 500 MB on the `C:\` drive. |
| **`ERR_INSUFFICIENT_RAM_07`** | Available RAM `< 512 MB` | The system does not have enough *available physical memory* (after background apps) to safely launch the exam browser. *(Note: If memory drops below this threshold mid-exam and crashes the local compiler, the Cloud Fallback mechanism will automatically route execution to AWS Judge0 without interrupting the student).* | **1.** Close Chrome, Teams, Discord, and other heavy background apps to free up memory.<br>**2.** Ensure the machine has at least 512 MB of completely free RAM. |
| **`ERR_WEBVIEW2_OUTDATED_11`** | `webview_version() < 112` | The host OS is running a highly vulnerable, legacy version of Microsoft Edge WebView2, leaving the system exposed to known DLL sideloading vulnerabilities patched in WebView2 112+. | **1.** Download the latest Microsoft Edge WebView2 Evergreen Bootstrapper from Microsoft.<br>**2.** Install it as an Administrator. *(This manual step is required if the EduCode automated installer skipped upgrading a pre-existing but dangerously old version).* |

---
*Note: This sheet is continuously updated as new security mitigations and edge cases are integrated into the browser.*

## ⚙️ Minimum System Architecture & Legacy OS Blocking

The EduCode Lock-Down Browser does **not** rely on custom code to block legacy operating systems. It natively utilizes the Windows Kernel to physically block execution on unsupported platforms due to modern API requirements.

### The `19041+` Win32 API Baseline
This executable is strictly compiled targeting the **Windows 10 May 2020 Update (Build 19041)** and newer APIs. This is a non-negotiable architectural constraint required by modern security hooks (like `SetWindowDisplayAffinity`) used in our Rust/Tauri v2 stack. *(Note: While `SetWindowDisplayAffinity` blocks local screenshots, it can be bypassed via hardware capture or Virtual Machines, which is why `ERR_VM_DETECTED_04` acts as the secondary defense layer).*

### What happens on Windows 7, Windows 8, or Windows 10 (Pre-19041)?
If a student copies `TheEduCode.exe` to one of these legacy machines and double-clicks it, **the application will not start, and no `ERR_` code will be generated.** 

Instead, the Windows Kernel itself will instantly intercept the launch and throw a fatal system dialog, typically:
> `"The procedure entry point X could not be located in the dynamic link library KERNEL32.dll"`
> *(or)* `"The application was unable to start correctly (0xc000007b)"`

The legacy kernel does not recognize the modern API calls embedded in the executable's header and chokes before line 1 of the code ever executes. **This is an architectural side-effect, not a deliberate anti-cheat mechanism.**

### Deployment & Compatibility Breakdown
- **Windows 11:** 100% natively compatible (All 19041+ APIs are fully available).
- **Windows 11 ARM64:** Supported (Tauri v2 supports ARM64 natively; x64 builds will run under Windows 11's x64 emulation, though native ARM64 compilation is recommended for optimal performance).
- **Windows 10 (Build 19041+):** Fully compatible. *Note: If system updates were disabled, these machines may still trigger `ERR_WEBVIEW2_OUTDATED_11` and require the Evergreen Bootstrapper. This is a separate failure mode from the OS version.*
- **Windows 10 LTSC 2019 / Pre-19041:** 0% compatible (Hard-blocked by Kernel). *Warning: LTSC 2019 (Build 17763) is common in education labs and will fail to launch.*
- **Windows 7 & 8:** 0% compatible (Hard-blocked by Kernel).
- **Pirated/Unactivated OS:** 100% compatible. Microsoft does not enforce "Genuine Windows" validation for the WebView2 renderer.

---

## 🧹 4-Tier Granular Uninstallation Guide

When resolving severe errors, IT support may need to completely wipe the application.

### Current Implementation: IT/Dev Batch Scripts (Option B)
Currently, during the testing phase, we provide 3 batch scripts located in `scripts\cleaners\` to manually perform these wipes:
1. **Level 1 (Basic):** Run Windows Uninstaller. Deletes only `C:\Program Files` binaries. Leaves user sessions and the 150MB compilers intact.
2. **Level 2 (Cache Wipe):** Run `cleanup_level_2_cache.bat`. Wipes `%LOCALAPPDATA%\com.educode.lockdown`. Fixes generic webview glitches (white screen).
3. **Level 3 (Log Purge):** Run `cleanup_level_3_logs.bat`. Wipes all local diagnostic logs in `C:\ProgramData` and `%LOCALAPPDATA%`.
4. **Level 4 (The Deep Nuke):** Run `cleanup_level_4_nuke.bat`. Wipes `%APPDATA%\theeducode`. Forces a total 150MB+ redownload on the next install. Use only for `ERR_CORRUPT_ASSET_12` or total factory reset.

### Future Roadmap (Production UX)
For the final production release, we will implement one of the following automated solutions to replace the batch scripts:
- **Option A (NSIS Installer Hooks):** Inject custom `.nsh` hooks into the Tauri `bundle.windows.nsis` config. This allows us to add checkboxes to the Windows Uninstaller dialog safely without overwriting the 1000-line default Tauri template.
- **Option C (In-App Reset):** Build a "Factory Reset" admin menu inside `preflight.html` that triggers Rust IPC commands (`clear_cache`, `clear_logs`, `clear_compilers`) to wipe the directories cleanly from within the UI before uninstalling.
