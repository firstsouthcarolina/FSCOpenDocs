---
icon: lucide/download
---

# Installing WPILib

WPILib is the core software suite for FRC. It installs a custom version of VSCode, Java compilers, and tools like AdvantageScope, Shuffleboard, and PathWeaver.

!!! warning "Do Not Use Standard VSCode!"
    WPILib installs a **separate** version of VS Code (e.g., `2026 WPILib VS Code`). Do not use an existing installation of VSCode on your machine, and *do not* use the VSCode Settings Sync feature, as it will break your FRC environment.

## 1. Downloading the Installer

Go to the [WPILib GitHub Releases Page](https://github.com/wpilibsuite/allwpilib/releases/latest) and download the correct installer for your OS (it will be an `.iso`, `.dmg`, or `.tar.gz`).

## 2. Extracting & Running

=== "Windows"

    1. Right-click the `.iso` file and select **Mount**.
        * *If "Mount" isn't an option, use [7-Zip](https://www.7-zip.org/) to "Extract to..."*
    2. Run `WPILibInstaller.exe`.
    3. Click **Start**.
    4. Select **Everything** (Installs tools, Java, C++, and VSCode).
    5. Choose **Install for all Users** (requires Admin rights).
    6. Click **Download for this computer only** when prompted for VSCode.

=== "macOS"

    1. Double-click the downloaded `.dmg` file.
    2. Double-click the **WPILibInstaller** application.
        * *If macOS warns you about it being from the internet, click **Open**.*
    3. Select **Everything** and let the installer run.
    4. Drag the newly installed WPILib VS Code application to your dock.
    5. Eject the `.dmg` from your desktop.

=== "Linux (Ubuntu/Mint/Arch)"

    Ubuntu treats executables as shared libraries, so you must run it from the terminal:
    
    ```bash
    tar -xf WPILib_Linux-<version>.tar.gz
    cd WPILib_Linux-<version>/
    ./WPILibInstaller
    ```
    
    **Ubuntu 23.10+ Fix:** Newer Ubuntu versions block sandboxed apps. To make VSCode run, install the AppArmor profiles:
    ```bash
    sudo cp ~/wpilib/2026/frccode/AppArmor/* /etc/apparmor.d/
    sudo systemctl reload apparmor.service
    ```

## 3. C++ Simulation (Optional)
If your team programs in C++ and uses Physics Simulation, you need native compilers installed on your machine:

* **Windows:** Install Visual Studio 2022 (Check "Desktop Development with C++").
* **macOS:** Install Xcode 14+ from the App Store.
* **Linux:** Run `sudo apt install build-essential`.