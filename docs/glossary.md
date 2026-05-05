## FRC Programming Glossary

This glossary covers common terms, libraries, tools, and concepts used in FIRST Robotics Competition (FRC) software development. It's written for students and mentors who are learning or teaching robot programming.

## FRC Programming Glossary

This glossary covers common terms, libraries, tools, and concepts used in FIRST Robotics Competition (FRC) software development. It's written for students and mentors who are learning or teaching robot programming.

### Acronyms

- WPILib — The primary robotics library used in FRC. Provides hardware interfaces, control loops, kinematics, simulation helpers, and more.
- PID — Proportional–Integral–Derivative controller. A common feedback loop used to control motors and mechanisms.
- CAN — Controller Area Network. A robust bus protocol used for communication between motor controllers, sensors, and other devices on the robot.
- PWM — Pulse Width Modulation. A signal used to control legacy motor controllers and servos.
- UDP / TCP — Networking protocols often used for telemetry, vision data, or dashboard communication.

### Libraries & Frameworks

- WPILib — The official library maintained by FIRST. Includes the command-based framework, hardware abstraction, simulation tools, and extensive documentation.
	- Language support: Java and C++ are the primary languages; Python is supported via RobotPy.
- RobotPy — Python ports of WPILib APIs. Useful for teams who prefer Python for quick development or education.
- Vendor libraries:
	- CTRE Phoenix — Libraries for CTRE hardware such as Talon SRX and Talon FX (Falcon) motor controllers.
	- REV Robotics libraries — For Spark MAX motor controllers and REV color sensors.
	- Kauai Labs navX — IMU libraries for orientation and motion sensing.

### Tools & Software

- Driver Station — The official app used during competition or testing to enable the robot, run programs, and view logs.
- Shuffleboard / SmartDashboard — Dashboard tools for telemetry, debugging, and operator interfaces.

### Hardware & Controllers

- Talon SRX / Talon FX (Falcon) — Motor controllers commonly used in FRC; support closed-loop control on the device.
- Spark / Spark MAX — Motor controllers from REV, supporting brushless motors and onboard closed-loop control.
- PCM (Pneumatic Control Module) — Controls solenoids for pneumatics via CAN.

### Control Concepts

- Command-based programming — A high-level structure provided by WPILib that separates robot behavior into "commands" and reusable "subsystems." It helps organize complex behaviors and simplifies scheduling.
- Iterative robot — A loop-based structure that runs periodic methods each robot cycle.
- Feedforward vs Feedback:
	- Feedforward — Predictive control using a mathematical model (for example, kV and kA) to calculate motor outputs.
	- PID — A closed-loop control strategy that uses a feedback sensor and a setpoint to gain precise, repeatable control of mechanisms.
	- PIDF — PID with an added feedforward term to improve tracking of dynamic setpoints.

### Sensors & Perception

- Encoders — Measure wheel or shaft rotation (position and velocity). Used for closed-loop control and odometry.

	#### Types of encoders

	- Relative encoders (incremental, quadrature) — Many modern brushless motors include a relative encoder integrated into the motor or controller. Relative encoders require no additional hardware but lose absolute position across power cycles; teams commonly re-zero them on startup with a homing routine, limit switch, or absolute reference.
	- Absolute encoders — Provide an absolute position value (angle) on power-up. Useful when the exact shaft angle is required after restart. Examples include CTRE's [CANCoder](https://store.ctr-electronics.com/cancoder) and REV's [Through-Bore Encoder](https://www.revrobotics.com/rev-11-1162/). Absolute encoders often use magnetic or optical sensing to preserve position across power cycles.

	#### Common uses and best practices

	- Closed-loop control — Use encoders as feedback for velocity or position PID loops. For drivetrain control, combine encoders with feedforward for better trajectory following.
	- Odometry and pose estimation — Use wheel encoders together with an IMU (or other heading source) to estimate robot pose over time. Account for wheel slip and measurement noise when designing filters.
	- Units and conversion — Convert raw encoder ticks to real-world units (meters, radians) early in your code to make controllers and logging clearer.
	- Fault handling — Detect stale or invalid encoder readings and provide fallbacks (for example, rely on the IMU or disable certain controllers) to prevent unsafe behavior.

- IMU (Inertial Measurement Unit) — Provides orientation (yaw, pitch, roll) and sometimes acceleration.
- Vision systems:
	- Limelight — A popular vision camera and processing system with a simple NetworkTables interface.
	- PhotonVision — An open-source vision pipeline for detection and pose estimation.
	- OpenCV — A general computer vision library used for custom vision processing.

### Networking & Telemetry

- NetworkTables — A lightweight publish/subscribe system used to share values between robot code, dashboards, and vision systems.
- NTService / mDNS — Mechanisms used for network discovery and service advertisement in competition environments.

### Motion & Path Planning

- Trajectory generation — Creating time-parameterized paths for the robot to follow during autonomous periods.
- Ramsete / PID controllers — Controllers used to follow trajectories (Ramsete is commonly used for differential drive robots).
- Odometry — Tracking robot pose (position and heading) over time using encoders and IMU data.

### Build & Deployment

- GradleRIO — The build system used by WPILib projects to compile and deploy robot code to the roboRIO.
- roboRIO — The embedded Linux controller on which robot code runs. WPILib deploys Java/C++ runtimes or a Python environment here.

### Testing & Simulation

- WPILib Simulation — A framework to run and test robot code on a desktop, often integrated with VS Code to visualize robot state.
- Unit tests — Teams are encouraged to write unit tests (where practical) for algorithms and utility code. Use the language's normal test framework (JUnit for Java, Catch2/GoogleTest for C++, pytest for Python).

### Common terms (short definitions)

- Axis / joystick mapping — The mapping of gamepad or joystick axes and buttons to robot controls.
- Deadband — A small threshold applied to joystick input to ignore small noise when the joystick is near center.
- Smoothing / filtering — Techniques (for example, low-pass filters or moving averages) applied to reduce sensor noise or abrupt commands.