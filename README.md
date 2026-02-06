# Smart-Home-Automation-Security-System
A Raspberry Pi Pico W-based IoT system featuring environmental automation, motion-triggered security, and a multi-layer control hierarchy (Web, Auto, and Physical Override).The system improves energy efficiency, comfort, and home convenience through sensor-based automation and Wi-Fi-ready control architecture.
The system was simulated using Wokwi, providing a safe and cost-effective environment for prototype development.

Objectives
Design a Raspberry Pi/Pico-based system for remote home appliance control.
Implement wireless control using Wi-Fi and web/mobile interface concept.
Improve energy efficiency and user convenience through automation.

Features
Web Dashboard: Remote "Away Mode" toggle via an asynchronous MicroPython web server.
Climate Automation: AC (Blue LED) triggers automatically when temperatures exceed 27°C using a DHT22 sensor.
Smart Lighting: House lights (Yellow LED) activate based on ambient light levels (LDR).
Security System: Motion-activated lighting (White/Red LED) with a 60-second sustain timer to ensure visibility after movement stops.
Manual Override (House Light): A dedicated hardware switch provides an immediate bypass for the primary living area lighting, ensuring accessibility regardless of sensor state.
Non-Blocking Logic: Built using time.ticks_ms() to ensure the web server and sensors remain responsive simultaneously.

Component - Pin (GPIO) - Function:
Raspberry Pi Pico W - Main Controller
DHT22- GP22 - Temperature & Humidity
LDR (Photoresistor) - GP28 - Ambient Light Detection
PIR Sensor- GP27 - Motion Detection
Slide Switch - GP16 - Manual Override
AC (Blue LED) - GP13 - Climate Output
Security (Red LED) - GP14 - Security Output
House (Yellow LED) - GP15 - Lighting Output

Software & Tools Used
  Wokwi Simulator
  MicroPython / Python
  GitHub for version control

Software Design & Logic Hierarchy:
To ensure the system is both user-friendly and energy-efficient, the logic follows a specific priority path:
Manual Override (House Light Only): The physical switch on GP16 always forces the House Light ON, regardless of sensor data or "Away Mode" status.
Away Mode (Web Toggle): If set to "Away" via the browser, the AC and Security systems are disabled to conserve power.
Autonomous Mode: When "Home," the system uses real-time sensor data and software timers to manage the environment.

Simulation:

This project was developed and tested using Wokwi simulation platform.
Simulation Includes:
  Sensor connections
  Automation logic implementation
  Appliance control demonstration

System Architecture
The project follows a classic Input-Process-Output (IPO) model, utilizing the Raspberry Pi Pico W as the central intelligence hub to bridge environmental data with physical actuators and web-based control.
1. Input Layer (Data Acquisition)These components monitor the environment and user intent, sending signals to the processing unit:
     DHT22 (Digital): High-accuracy temperature sensing via a single-bus digital signal.
     LDR (Analog): Provides variable voltage levels converted by the Pico's 16-bit ADC to determine lux levels.
     PIR Sensor (Digital): A passive infrared sensor that sends a HIGH signal upon detecting thermal movement.
     Slide Switch (Hardware): A physical toggle providing a direct GPIO input for manual override.
     Web Client (Network): Incoming HTTP requests via Wi-Fi that act as "virtual" inputs for system state changes.
2.   Processing Unit (Intelligence)The Raspberry Pi Pico W (RP2040) acts as the brain, running a MicroPython firmware stack that manages:
       Threshold Comparison: Evaluating if $Temp > 27^{\circ}C$ or $Lux > 30000$.
       Timing Management: Utilizing ticks_ms for non-blocking sustain timers (60s security light) and sensor polling intervals.
       Networking: Managing the TCP/IP stack to host a lightweight web server.
       Priority Arbiter: Software logic that decides which input (Manual, Web, or Sensor) takes precedence at any given millisecond.
3. Output Layer (Actuation)The results of the processing unit's decisions are manifested through these actuators:
     Blue LED (GP13): Visual representation of the AC System activation.
     Red LED (GP14): Visual representation of the Security Lighting system.
     Yellow LED (GP15): Visual representation of the House Lighting system.
     Web Dashboard: An HTML response sent back to the user's browser, providing real-time status feedback and system telemetry.

Lessons Learned
Developing this Smart Home system provided deep insights into the complexities of real-time embedded systems. Below are the key takeaways from this project:
  1. The Importance of Non-Blocking Code.
       Early iterations used time.sleep(), which caused the system to become unresponsive to motion or web requests while waiting. Switching to a polling mechanism using time.ticks_ms() was a turning point. It taught me           how to manage multiple "clocks" simultaneously, ensuring that the security timer, sensor intervals, and web server loop never block one another.

  2. Network Reliability in Embedded Systems
     I learned that networking on a microcontroller like the Pico W is fragile. If the code "waits" for a client to connect (blocking socket), the entire house freezes. Implementing non-blocking sockets      (s.setblocking(False)) taught me how to handle asynchronous communication and error handling (OSError) in a resource-constrained environment.
     
  3. Hardware Priority & Safety Logic
    Designing the "House Light Only" override taught me about User Experience (UX) in Hardware. I realized that while automation is great, a user should never be "locked out" of their own lights by a sensor failure or a   lost Wi-Fi connection. Prioritizing the physical switch in the code hierarchy is a critical safety and usability feature.

  4. Circuit Grounding and Signal Integrity
     Debugging the "lights not turning on" issue reinforced the fundamental rule of electronics: A common ground is mandatory. I learned that even if the code is perfect, a "floating" circuit will never function. This project improved my ability to read wiring diagrams and systematically trace power paths.

     
