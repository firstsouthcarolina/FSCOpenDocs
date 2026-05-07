---
icon: lucide/focus
---

# Intro to Vision in FRC

Computer Vision can get very complicated very quickly.
This is a simple guide to get your bot up and running with vision.

!!! note
    This is meant to serve as a quick start guide for teams that are new to vision.  
    If you're looking for more in depth vision stuff, you can start [here](/vision/advanced).

There are two major options for vision:

<div class="grid" markdown>
<div class="card" markdown>
### PhotonVision
 - Open source
 - *Can* run on cheap hardware
 - Takes some work to get set up
 - AprilTags
 - Widely used in FIRST SC
</div>
<div class="card" markdown>
### Limelight
 - Proprietary
 - Expensive hardware
 - "Just works"
 - AprilTags, object detection, and arbitrary Python scripts
 - Not as popular in FIRST SC
</div>
</div>

PhotonVision is recommended, but Limelight is easier to set up if budget allows.

<!-- Need to document Limelight (or maybe just point to their docs?) -->

## Recommended Hardware

<div class="grid cards" markdown>

- ### ThriftyCam
    ThriftyCams are excellent for AprilTags.
    
    [Buy](https://www.thethriftybot.com/products/thriftycam)

- ### Raspberry Pi 5
    Inexpensive, powerful, and standard architecture. They have never given us any trouble whatsoever.

</div>
