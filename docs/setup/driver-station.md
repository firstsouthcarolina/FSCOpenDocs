---
icon: lucide/gamepad-2
---

# The Driver Station

The Driver Station (DS) is the application that reads your Xbox/PlayStation controllers and sends the enable/disable packets to the robot. 

## The 2026 Landscape
For the 2026 season, the official FRC Driver Station is **Windows Only**. If you are connecting to a robot to drive it on the official field, you must use a Windows laptop. 

## Building the Ultimate Competition Driver Station
Your driver station laptop is the single point of failure between your drive team and the robot. You do not want Windows Update restarting your computer during Einstein finals. 

Here is the checklist for a reliable, bulletproof competition laptop:

### 1. The Yearly Wipe
**Wipe the laptop completely clean before every kickoff.** Do not just uninstall last year's tools. A fresh OS install removes old firewall rules, hidden VPN configurations, and leftover bloatware that could block mDNS or network tables.

### 2. The Operating System
While Windows 11 is the standard, many FRC laptops are older, low-spec machines. 
!!! tip "Consider Tiny11"
    Many top teams use **Tiny11** (a stripped-down, debloated version of Windows 11). It removes background telemetry, Xbox Game Bar, OneDrive, and Cortana, freeing up CPU and RAM so your dashboards never lag. It also lets you use windows without a online account.

### 3. The Software Stack
Install **only** what you need to run the robot and diagnose issues in the pit. Do not install Discord, Steam, or personal files.

* [x] **NI Game Tools (The FRC Driver Station):** Required to connect to the FMS.
* [x] **WPILib (Tools Only):** You don't need the full VSCode suite on the DS, but you *do* need the WPILib tools to run dashboards like **AdvantageScope** or **Elastic**.
* [x] **Git:** Crucial for the pit crew. If a programmer pushes an emergency fix to GitHub from the stands, the pit crew needs Git to `git pull` and deploy the code.
* [x] **A Lightweight Web Browser:** Useful for accessing Coprocessor interfaces (like `photonvision.local:5800`). 
* [x] **Vendor Tools:** Hardware clients to tune motors (See the [Vendor Tools](vendor-tools.md) page).

### 4. System Settings to Change
* **Disable Windows Updates:** Pause them for the maximum allowed time before your event.
* **Disable Windows Defender / Security:** Turning this off can prevent issues when connecting to the field, robot, coprocessors, etc.
* **Disable Sleep/Hibernate:** Set "When lid is closed" to "Do Nothing." You don't want the laptop going to sleep while you are carrying it to the field!
* **Disable Bluetooth & Wi-Fi:** When plugging into the FMS ethernet tether, turn off your Wi-Fi and Bluetooth to prevent packet interference.

---

## Future DS

Starting in 2027 (and available for off-season events sooner), FIRST is releasing a brand-new, built-from-scratch Driver Station. 

*   :simple-linux: **Cross-Platform Support:** The DS will natively run on Windows, macOS, and Linux (both x64 and ARM64!).
*   :material-controller-classic: **Better Gamepad Support:** Axes now have 16-bit resolution (up from 8-bit) for ultra-fine swerve control.
*   :material-clock-fast: **WPILog Native:** DS logs are now saved natively in the `.wpilog` format.
*   :material-stop-circle: **New E-Stop Recovery:** Holding ++esc+i++ for 1 second will reset the E-Stop state without a full robot reboot.

!!! danger "FMS is still Windows Only!"
    Even though the 2027 DS runs on Mac and Linux, **Field Management System (FMS)** support is restricted to Windows and approved hardware appliances (like the upcoming SystemCore).