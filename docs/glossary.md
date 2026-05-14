---
icon: lucide/book
---

## FRC Programming Glossary

This glossary covers common terms, libraries, tools, and concepts used in FIRST Robotics Competition (FRC) software development. If you ever wonder what an acronym or fancy tool is, this is the place to find out!

### Acronyms

<a id="wpilib-official-definition"></a>
- WPILib (Official definition) —  The WPI Robotics Library (WPILib) is the standard software library provided for teams to write code for their FRC® robots. WPILib contains a set of useful classes and subroutines for interfacing with various parts of the FRC control system (such as sensors, motor controllers, and the driver station), as well as an assortment of other utility functions.


- CAN — Controller Area Network. A robust bus protocol used for communication between motor controllers, sensors, and other devices on the robot.
- PWM — Pulse Width Modulation. A signal used to control legacy motor controllers and servos.
- UDP / TCP — Networking protocols often used for telemetry, vision data, or dashboard communication.

### Vendor libraries
- CTRE Phoenix — Libraries for CTRE hardware such as Talon FX and Talon FXS motor controllers.
- REVLib — For Spark MAX motor controllers
- Kauailabs NavX Library — IMU library for the NAVX Gyros.

### Tools & Software

- Driver Station - The official app used during competition or testing to enable the robot, run programs, and view logs.
- Elastic / Advantagescope - Dashboard tools for telemetry, debugging, and operator interfaces.

### Hardware & Controllers

- Talon FX/FXS — Motor controllers commonly used in FRC; support closed-loop control on the device. Note that Kraken X44/X66 and Falcon 500 motors use Talon FX motor controllers, while the Talon FXS is used standalone from a motor like the CTRE Minion.
- Spark MAX/Spark Lite — Motor controllers from REV, supporting brushed/brushless motors and onboard closed-loop control.
- PCM (Pneumatic Control Module) — Controls solenoids for pneumatics via CAN.

### Control Concepts

- Command-based programming - A high-level structure provided by WPILib that separates robot behavior into "commands" and reusable "subsystems." It helps organize complex behaviors and simplifies scheduling.
- Iterative robot - A loop-based structure that runs periodic methods each robot cycle.
- Feedforward vs Feedback:
	- Feedforward - Predictive control using a mathematical model (for example, kV, kS, and kA) to calculate motor outputs.
	- PID - A closed-loop control strategy that uses a feedback sensor and a setpoint to gain precise, repeatable control of mechanisms. Stands for Proportional-Integral-Derivative.
	- PIDF - PID with added feedforward terms.

### Sensors & Perception

- Encoders - Measure wheel or shaft rotation (position and velocity). Used for closed-loop control and odometry.

	#### Types of encoders

	- Relative encoders (incremental, quadrature) - Many modern brushless motors include a relative encoder integrated into the motor or controller. Relative encoders require no additional hardware but lose absolute position across power cycles; teams commonly re-zero them on startup with a homing routine, limit switch, or absolute reference.
	!!! tip "A note about the KitBot"
        By default, the stock KitBot drivetrain utilizes brushed CIM motors, which do **NOT** include relitive encoders. If you are looking into doing odometry and running a path generation tool you will have to swap the motors out for another CIM class brushless motor (like Neos), or install an external encoder.

	- Absolute encoders - Provide an absolute position value (angle) on power-up. Useful when the exact shaft angle is required after restart. Examples include CTRE's [CANCoder](https://store.ctr-electronics.com/cancoder) and REV's [Through-Bore Encoder](https://www.revrobotics.com/rev-11-1162/). Absolute encoders often use magnetic or optical sensing to preserve position across power cycles.

	#### Other sensor related terms

	- Closed-loop control - Use encoders as feedback for velocity, position, or torque PID loops. For drivetrain control, combine encoders with feedforward for better trajectory following.
	- Odometry and pose estimation - Use wheel encoders together with an IMU (or other heading source) to estimate robot pose over time. Account for wheel slip and measurement noise when designing filters.
	- Units and conversion - Convert raw encoder ticks to real-world units (meters, radians) early in your code to make controllers and logging clearer.

- IMU (Inertial Measurement Unit) - Provides orientation (yaw, pitch, roll) as well as acceleration.
- Vision systems:
	- Limelight - An out of the box proprietary vision solution, single camera. It will be included on the Systemcore firmware.
	- PhotonVision - An open-source vision tool, can use multiple cameras for less overhead cost. More options for customization, but needs a co-processor. 

### Networking & Telemetry

- NetworkTables - A lightweight publish/subscribe system used to share values between robot code, dashboards, and vision systems.
- Angry IP Scanner - Tool used to scan your robot's LAN network and see what networked processors are online at what IP/Port.

### Motion & Path Planning

- Trajectory generation - Creating preplanned and on the fly paths for the robot to follow during autonomous / automated periods. Must have odometry to use this strategy.
- PathPlanner/Choreo - Popular trajectory generation tools that allow for easy auto creation.
- Odometry - Tracking robot pose (position and heading) over time using encoders and IMU data. Data can be added from vison systems as well to prevent drift.

### Build & Deployment

- GradleRIO - The build system used by WPILib projects to compile and deploy robot code to the roboRIO.
- roboRIO - The (legacy, replaced with SystemCore in 2027) embedded Linux controller on which robot code runs. WPILib deploys Java/C++ runtimes or a Python environment here.
- SystemCore - The new 2027 robot controller will be powered by a Rasberry Pi 5 compute module chip, it will also be shared between FTC and FRC for one larger ecosystem. We do not know much yet so most of these docs contain documentation for the RoboRio system, but we do not expect major changes that make this info obsolete yet.

### Other terms

- Axis / joystick mapping - The mapping of gamepad or joystick axes and buttons to robot controls.
- Deadband - A small threshold applied to joystick input to ignore small noise when the joystick is near center.