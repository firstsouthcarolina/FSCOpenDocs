---
icon: lucide/network
---

# Networking

In FRC, your robot's radio, robot controller (Systemcore or RoboRIO), coprocessors, and driver's station all communicate over a local network.

# FRC Team Networking & IP Schema

Whether you are an original legacy team or a brand new rookie, FIRST sets up the robot network on a `10.TE.AM.X` IPv4 address format. 

Understanding how to convert your team number into this IP address is crucial for static IP configuration and network troubleshooting. Use the calculator below to find your IPs instantly, or read on to learn the math behind the routing.

<div style="padding: 1.5rem; border: 1px solid var(--md-default-fg-color--lightest); border-radius: 0.4rem; background-color: var(--md-default-bg-color); box-shadow: var(--md-shadow-z1); margin: 2rem 0;">
  <h3 style="margin-top: 0;">FRC IP Calculator</h3>
  <label for="teamNumber" style="font-weight: 700; display: block; margin-bottom: 0.5rem; color: var(--md-default-fg-color);">Enter Team Number:</label>
  <input type="number" id="teamNumber" class="md-input md-input--stretch" placeholder="e.g. 10256, 254, or 8" min="1" max="99999" style="margin-bottom: 1.5rem;" />

  <div id="frcResults" style="display: none; padding-top: 1rem; border-top: 1px solid var(--md-default-fg-color--lightest);">
    <p style="margin-bottom: 0.5rem;"><strong>Radio:</strong> <code id="ipRadio"></code></p>
    <p style="margin-bottom: 0.5rem;"><strong>roboRIO:</strong> <code id="ipRio"></code></p>
    <p style="margin-bottom: 0.5rem;"><strong>FMS:</strong> <code id="ipFms"></code></p>
    <p style="margin-bottom: 1.5rem;"><strong>Driver Station:</strong> <code id="ipDs"></code></p>
    <p style="margin-bottom: 0;"><strong>mDNS (Hostname):</strong> <code id="mDns"></code></p>
  </div>
</div>

<script>
  const el = document.getElementById('teamNumber');
  el.value = "";
  el.addEventListener('input', function(e) {
    const inputVal = e.target.value;
    const resultsDiv = document.getElementById('frcResults');

    // Hide if empty or invalid
    if (!inputVal || inputVal <= 0 || inputVal >= 100000) {
      resultsDiv.style.display = 'none';
      return;
    }

    const teamNumber = parseInt(inputVal, 10);

    // The Math Formula
    const octet2 = Math.floor(teamNumber / 100);
    const octet3 = teamNumber % 100;
    
    // Construct the base IP 
    const baseIp = `10.${octet2}.${octet3}`;

    // Populate the HTML fields
    document.getElementById('ipRadio').textContent = `${baseIp}.1`;
    document.getElementById('ipRio').textContent   = `${baseIp}.2`;
    document.getElementById('ipFms').textContent   = `${baseIp}.4`;
    document.getElementById('ipDs').textContent    = `${baseIp}.5`;
    document.getElementById('mDns').textContent    = `roborio-${teamNumber}-FRC.local`;

    // Show the results div
    resultsDiv.style.display = 'block';
  });
</script>

## How the FRC IP Schema Works

The standard FRC IP notation is `10.TE.AM.X`, where the team number determines the second and third octets. 

Instead of treating the team number like a string of text and worrying about leading zeros, the easiest way to understand the schema is mathematically:

1. **Octet 2:** Divide your team number by `100` and round down to the nearest whole number.
2. **Octet 3:** Take the remainder of your team number divided by `100` (modulo).

This mathematical rule automatically scales perfectly across 1-digit, 4-digit, and 5-digit team numbers.

### Examples by Team Length

**1 & 2-Digit Teams**  
Because the team number is less than 100, the second octet is always `0`.

* **Team 8:** `8 / 100 = 0`, remainder `8`. IP is `10.0.8.2`
* **Team 16:** `16 / 100 = 0`, remainder `16`. IP is `10.0.16.2`

**3-Digit Teams**  

* **Team 254:** `254 / 100 = 2`, remainder `54`. IP is `10.2.54.2`

**4-Digit Teams (Standard)**  

* **Team 2168:** `2168 / 100 = 21`, remainder `68`. IP is `10.21.68.2`

**5-Digit Teams (Overflow)**  
For 5-digit teams, the "thousands" and "hundreds" flow cleanly into the 2nd octet.

* **Team 10256:** `10256 / 100 = 102`, remainder `56`. IP is `10.102.56.2`

## Device Endings

The 4th (and final) octet is what identifies the specific piece of hardware on your robot's subnet. FIRST reserves these addresses as follows:

* **`.1`** – The Robot Radio (OpenMesh, OM5P, or VH-109)
* **`.2`** – The roboRIO
* **`.4`** – The FMS (Field Management System)
* **`.5` through `.255`** – Assigned to your Driver Station laptops, coprocessors (like a Raspberry Pi, Orange Pi, or Limelight), and IP cameras.

## The USB Tether Connection

If you connect a USB-B cable directly from your laptop to the roboRIO, a virtual network interface is created. In this configuration, the roboRIO always has a fixed IP address:

* **USB IP Address:** `172.22.11.2`

### Drawbacks of USB Connection
While the USB tether is a great fallback, it has several limitations:

* **Subnet Isolation:** The USB connection is a point-to-point link. When connected via USB, your laptop cannot "see" other devices on the robot's Ethernet network, such as Limelights, Raspberry Pis, or IP Cameras. You can only communicate with the roboRIO itself.
* **Driver Dependencies:** This connection relies on "Ethernet over USB" (RNDIS) drivers. If these drivers are missing or corrupted on your Windows laptop, the connection will fail even if the cable is functional.
* **Mechanical Reliability:** USB-B ports are not as robust as shielded Ethernet. In a high-vibration environment or while the robot is moving, the cable is easily jarred loose.
* **Cable Length:** USB standards limit cable length significantly compared to Ethernet, making it difficult to use for long-distance testing.

## A Note on mDNS

While configuring static IPs is incredibly useful for network stability and troubleshooting at competition, modern FRC control systems utilize mDNS (Multicast DNS). 

If you are typing an address into a browser or SSH client to reach your roboRIO, simply typing `roborio-[TEAM]-FRC.local` (e.g., `roborio-254-FRC.local`) will usually find the robot dynamically without needing to worry about IPv4 subnets and octets.
