FortiClient Uninstall Script (Advanced | Intune Ready)

A production-grade PowerShell script to fully remove FortiClient from Windows endpoints. Built for enterprise environments with safe uninstall logic, reboot orchestration, and post-removal cleanup.

---

🚀 Key Features

* 🔍 Detects all installed FortiClient components (32-bit & 64-bit)
* ⚙️ Supports:

  * MSI uninstall (GUID-based)
  * EXE uninstall (fallback with safe parsing)
* 🚫 Supports exclusion of specific GUIDs
* 🔁 Retry-safe (ideal for Intune remediation)
* 🔄 Handles pending reboot scenarios gracefully
* 🧹 Performs deep cleanup (services + leftover folders)
* 📢 User reboot notification (15-minute countdown)
* 📝 Detailed logging for troubleshooting

---

📁 Log File Location

```text
C:\ProgramData\Microsoft\IntuneManagementExtension\Logs\FortiClient_Uninstall.log
```

---
▶️ Usage

Run in **elevated PowerShell (Administrator)**:

```powershell
.\FortiClient-Uninstall.ps1
```

---

⚙️ Script Workflow

1. Pre-Checks

* Ensures log directory exists
* Detects pending reboot:

  * If found → exits safely (prevents deployment failures)

---

2. Application Discovery

Searches registry:

* `HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall`
* `HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall`

Filters:

* Matches `FortiClient`
* Ignores null display names
* Removes duplicates

---

3. GUID Exclusion Logic

The script allows skipping specific MSI packages:

```powershell
if ($app.PSChildName -eq "{}") {
    continue
}
```

👉 Useful when:

* You want to preserve VPN-only module
* Exclude a known problematic version
* Avoid removing dependent components

---

4. Uninstall Methods

MSI Uninstall

```powershell
msiexec /x {GUID} /quiet /norestart
```

Handled exit codes:

* `0` → Success
* `1605` → Already removed
* `3010` → Reboot required (auto-handled)

---

EXE Uninstall

* Safely parses uninstall string
* Auto-adds:

  * `/quiet`
  * `/norestart`

Prevents:

* Broken command parsing
* Missing silent switches

---

5. Reboot Handling

If required:

* Notifies user:

  ```
  Restart required in 15 minutes
  ```

* Schedules reboot:

```powershell
shutdown.exe /r /t 900
```

* Prevents duplicate scheduling

---

6. Post-Uninstall Cleanup

Removes:

Services

* Matches patterns:

  * `Forti`
  * `FC`

Folders

* `C:\Program Files\Fortinet`
* `C:\Program Files (x86)\Fortinet`
* `C:\ProgramData\Fortinet`

---

📊 Exit Codes

| Code | Meaning                   |
| ---- | ------------------------- |
| 0    | Success                   |
| 3010 | Success (Reboot required) |
| 1605 | Product already removed   |
| 1+   | Failure                   |

---

🏢 Enterprise Use Cases

* Microsoft Intune remediation scripts
* SCCM uninstall deployments
* Security agent replacement
* Automated endpoint cleanup

---

🔒 Design Principles

* ✔ Idempotent (safe to run multiple times)
* ✔ Silent execution
* ✔ Minimal user disruption
* ✔ Robust error handling
* ✔ Enterprise logging standard

---

🛠️ Requirements

* Windows 10 / 11
* PowerShell 5.1+
* Administrator privileges

---

📌 Best Practices

* Test in pilot group before full deployment
* Use with Intune:

  * Detection script + remediation script
* Monitor logs for validation
* Customize GUID exclusion if needed

---

✍️ Author

Ashpak Shaikh

---

📄 License

MIT License (recommended)

---
⭐ Contributing

Pull requests and improvements are welcome via GitHub.
