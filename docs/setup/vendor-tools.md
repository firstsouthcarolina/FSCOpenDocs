---
icon: lucide/cpu
---

# Vendor Tools & Additional Software

WPILib provides the foundation, but to update firmware, tune PID values, and configure CAN IDs on your motors, you need the specific software provided by the hardware vendors.

## Essential Vendor Clients

<div class="grid cards" markdown>

-   :material-alpha-r-circle: **Redux Alchemist**
    
    ---
    
    The configurator for **Canand** devices (like the Canandgyro and Canandcoder).
    
    * **OS:** Windows, macOS, Linux
    * **Key Feature:** Requires a "CANLink" server running on the robot code to communicate.
    
    [Download Alchemist](https://github.com/Redux-Robotics/Alchemist/releases)

-   :material-lightning-bolt: **CTRE Phoenix Tuner X**
    
    ---
    
    Used for all **Talon FX (Krakens/Falcons)**, **CANCoder**, and **Pigeon 2.0**.
    
    * **OS:** Windows (Microsoft Store), Android, iOS
    * **Key Feature:** Includes the Swerve Generator and advanced signal logging.
    
    [Download Tuner X](https://docs.ctr-electronics.com/en/latest/docs/tuner/index.html)

-   :material-refresh: **REV Hardware Client**
    
    ---
    
    Used for **Spark MAX**, **Spark Flex**, and **NEO** motors. 
    
    * **OS:** Windows (Only)
    * **Key Feature:** Native USB-C connection to controllers for standalone tuning.
    
    [Download RHC2](https://rhc2.revrobotics.com/download/download.html)

-   :material-camera: **Limelight Hardware Manager**
    
    ---
    
    Used to discover Limelights on the network and flash them with new OS images.
    
    * **OS:** Windows, macOS (Intel & Silicon)
    * **Key Feature:** Finds cameras even if mDNS is failing.
    
    [Download Hardware Manager](https://docs.limelightvision.io/docs/resources/downloads)

</div>

---

## Redux Alchemist & CANLink
Unlike other vendors, Redux Alchemist requires your robot code to be running a **CANLink Server** to bridge the connection between your laptop and the CAN bus.

To enable communication, you must include the `ReduxLib` vendordep and add the following to your `robotInit()`:

=== "Java"

    ```java
    import com.reduxrobotics.canand.CanandEventLoop;

    // Inside robotInit()
    CanandEventLoop.getInstance();
    ```

=== "C++"

    ```cpp
    #include <redux/canand/CanandEventLoop.h>

    // Inside RobotInit()
    redux::canand::EnsureCANLinkServer();
    ```

!!! success "Verification"
    Once the code is deployed, navigate to `http://roborio-####-FRC.local:7244` in your browser. If you see the "Redux CANLink" ASCII art, Alchemist is ready to connect.

---

## The REV "RHC2" Firmware Gap
!!! danger "Critical Warning for REV Users"
    For the 2026 season, REV Devices must be running **Firmware 26.x.x** or newer to be compatible with **REV Hardware Client 2 (RHC2)**. 

    If you have a brand-new Spark MAX or one running 2025 firmware, RHC2 will flag it as an **"Outdated REV Device"** and may fail to interact with it properly. 

    **The Fix:** 
    
    1.  Ensure you have installed the drivers found in the RHC2 settings menu. 
    2.  If the device is still not detected, use **Recovery Mode**: Hold the Mode button while plugging in USB, then use the Client to "Load Firmware" manually. 
    3.  In some cases, you may need to use the older **RHC 1.7.6** to perform the initial jump to 2026 firmware before RHC2 will recognize the device.

---

## SD Card Flashing & Network Utilities

Before you can use a Coprocessor (like an Orange Pi, Rubik Pi, or Limelight), you must flash its storage with an operating system.

*   **[Balena Etcher](https://etcher.balena.io/):** The standard tool for flashing PhotonVision or WPILibPi images to MicroSD cards.
*   **[Angry IP Scanner](https://angryip.org/):** If you can't find your coprocessor at `photonvision.local`, use this to scan your subnet (`10.TE.AM.x`) to find the actual IP address.
*   **[dfu-util](https://dfu-util.sourceforge.net/):** An advanced command-line tool for Linux/macOS users to flash Spark MAX controllers in DFU mode without using the REV Hardware Client.

!!! danger "How to use `dfu-util`"
    If you put the Spark MAX/Flex into recovery mode, you can use `sudo dfu-util -a 0 -D SparkMAX-[version]-[hash].dfu` in the terminal (if it is installed) to flash the device.