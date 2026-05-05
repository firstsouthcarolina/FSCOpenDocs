---
icon: lucide/computer
---

# Robot Controller

The robot controller is how the code gets ran on the bot. It takes your Java, C, Python, etc and executes it and talks to the motors and sensors to make it move.

## The RoboRIO

Depending on which version of the RoboRIO your team owns, the imaging process is fundamentally different. 

=== "RoboRIO 2.0 (MicroSD)"

    The RoboRIO 2.0 runs its entire Operating System (OS) from a **MicroSD card**. 
    
    *   **The Workflow:** You do **not** use the RoboRIO Imaging Tool for the OS. Instead, you flash the SD card directly using your laptop.
    *   **Tools Needed:** [Balena Etcher](https://etcher.balena.io/) or Raspberry Pi Imager.
    *   **Process:**

        1.  Remove the MicroSD card from the RIO.
        2.  Download the latest image (usually found in `~/wpilib/YYYY/images/`).
        3.  Flash the image to the card using Balena Etcher.
        4.  Insert the card back into the RIO and boot.
    
    !!! tip "Judson's Pro-Tip: Card Quality Matters"
        Standard consumer SD cards often fail under the vibration and heat of a robot. FSC recommends using **SanDisk Industrial MicroSD cards**. They are designed for high write cycles and are much less likely to corrupt mid-match.

=== "RoboRIO 1.0 (Internal Flash)"

    The RoboRIO 1.0 has internal memory. You must "push" the image to it over a USB cable.
    
    *   **Tools Needed:** **RoboRIO Imaging Tool** (installed with NI Game Tools).
    *   **Process:**

        1.  Connect the RIO to your laptop via a **USB-B cable**.
        2.  Open the Imaging Tool.
        3.  Select your RIO, enter your team number, and click **Reformat**.
    
    !!! warning "RIO 1 Memory Issues"
        The RIO 1 has significantly less RAM than the RIO 2. To prevent crashes during a match, we recommend **disabling the Web Dashboard**. This frees up precious memory for your actual robot code.

---

## Formatting the SD Card (RIO 2.0)

When you image an SD card for a RIO 2.0, the card is partitioned into several sections. 

1.  **Boot Partition:** Small area for startup files.
2.  **System Partition:** Where the Linux OS lives.
3.  **User Data:** Where your actual robot code is deployed.

**Never** try to drag-and-drop files onto the SD card through Windows Explorer. Always use a proper imaging tool (Balena Etcher) to ensure the partitions are aligned correctly.

---

## The Future

Starting in 2027, the FRC control system is evolving into **SystemCore**. This is more than just a CPU update; it is a unified robotics controller.

<div class="grid cards" markdown>

-   :material-camera: **Integrated Vision**
    
    ---
    SystemCore is designed to handle onboard vision processing natively, potentially reducing the need for external Orange Pis or Rubik Pis.

-   :material-web: **Web-Based Setup**
    
    ---
    Expect a move away from standalone "Imaging Tools" toward a modern web interface. You will likely configure your team number and firmware via a browser-based dashboard hosted directly on the device.

-   :material-expansion-card: **Built-in IMU**
    
    ---
    High-fidelity motion tracking (similar to a Pigeon or NavX) is planned to be built directly into the SystemCore hardware, simplifying the CAN bus.

</div>

!!! info "Alpha Testing"
    SystemCore is currently in Alpha testing with 50 teams globally. While the RoboRIO will remain legal for the near future, the transition to SystemCore will represent a "software-first" shift in how we think about robot control.

---

## Common Troubleshooting

*   **RIO 1 Not Showing Up:** Ensure you are using a data-ready USB-B cable. Some cheap cables only provide power.
*   **"Unteathered" Imaging:** You cannot image a RIO over Wi-Fi. Always use USB or Ethernet.
*   **Firewall Blocks:** If the Imaging Tool fails to "see" the RIO, temporarily disable your Windows Firewall. FRC networking protocols are often flagged as "suspicious" by default Windows settings.