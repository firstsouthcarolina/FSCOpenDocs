---
icon: lucide/laptop
---

# Choosing Your Operating System

Before installing any software, you need to decide which operating system (OS) you are going to program on. 

Historically, FRC was entirely tied to Windows. However, with WPILib's cross-platform support and the upcoming **2027 Cross-Platform Driver Station**, you can now write code and drive the robot on macOS and Linux!

Here are the tradeoffs for each platform to help you decide:

<div class="grid cards" markdown>

-   :fontawesome-brands-windows: **Windows**
    
    ---
    
    Windows is the only platform that will support the **FMS (Field Management System)**. If you are the drive team laptop, it *must* be Windows.
    
    * **Pros:** Guaranteed to work at competitions; native support for the 2026 Driver Station.
    * **Cons:** Resource-heavy; Windows 10 support ends in October 2025.

-   :fontawesome-brands-apple: **macOS**
    
    ---
    
    Excellent battery life and performance for compiling code. 
    
    * **Pros:** Fast compile times (especially on M-series chips); great UNIX terminal.
    * **Cons:** Cannot connect to the official FMS at competitions. Requires Xcode for C++ simulation.

-   :fontawesome-brands-linux: **Linux**
    
    ---
    
    Extremely lightweight and perfect for older laptops.
    
    * **Pros:** Perfect for testing and "shop" laptops; natively runs Bash and Git without extra tools.
    * **Cons:** No FMS support; requires manual tweaks (like AppArmor for Ubuntu 23.10+). 

</div>

!!! tip "Judson's Recommendation"
    *"As a CSA, I'm going to tell you to only bring a Windows laptop to the driver station. But at our shop, that means I can bring out two laptops with Ubuntu and let them be used exclusively for FRC testing."*
    
    **TL;DR:** Program on whatever you want, but drive the real match on Windows.

## Minimum Requirements
* **RAM:** 8GB minimum (32GB heavily recommended if your team uses C++).
* **Architecture:** 64-bit only (32-bit is no longer supported).