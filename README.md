# EXP32-vaccumrobo

## Project Overview
This project is an open-source, decoupled autonomous mobile robot (AMR) conversion that repurposes a salvaged robotic vacuum cleaner chassis into a network-controlled, intelligent floor-care platform.
  Instead of relying on proprietary closed-source mainboards, the system implements a distributed two-tier computing architecture:
    1. Low-Level Real-Time Controller (ESP32): Manages hardware timing, high-frequency PWM switching, safety reflex loops, and raw sensor acquisition.
    2. High-Level Compute Engine (Raspberry Pi 3B+): Handles computationally intensive 2D LiDAR SLAM, autonomous path planning, telemetry aggregation, and web dashboard hosting over Wi-Fi
## Core System Features
  Cleaning Subsystem:
    Vacuum Turbine: High-CFM suction blower driven by an optocoupler-isolated LR7843 MOSFET with variable PWM duty cycle.
    Dual Brushes: Independent control of the horizontal roller agitator and the perimeter side broom for edge cleaning
    Mopping & Fluid Control: Peristaltic water pump metering with reservoir fluid-level monitoring.
  Safety Reflexes & Interlocks:
    Emergency Cliff Avoidance: Hardware interrupt pins detect floor elevation drops to stop drive motors instantly.
    Bumper Collision Reflex: Dual front microswitches trigger an immediate reverse-and-pivot maneuver.
    Dustbin Interlock: A Hall-effect magnetic sensor (PD31_Hall V1.3) disables the suction turbine if the dustbin is absent
  Navigation & Localization:
    360° LiDAR Telemetry: Serial point-cloud capture forwarded via UDP to the compute host for real-time 2D mapping
    Differential Drive: Dual DC gearmotors driven by a TB6612FNG H-bridge with bulk decoupling protection.
    Autonomous Docking & Manual Override: Remote touchscreen joystick interface with waypoint navigation back to the charging base coordinate.

## Communication & Software Stack
  Mobile Node (ESP32):
    Built using ESP32 Arduino Core with FreeRTOS
    Core 0 executes network tasks, transmitting LiDAR frames and telemetry packets over Wi-Fi UDP.
    Core 1 executes deterministic motor PID control, sensor polling, and instant hardware safety cuts.
  Stationary / Compute Node (Raspberry Pi 3B+)
    Headless 64-bit Linux running a Python-based UDP communications hub and web app server
    Generates 2D occupancy grid maps using SLAM algorithms and feeds velocity vectors back to the robot over the local network
## materials and hardware components used across this autonomous vacuum build

  1. Compute & Controllers
       ESP32 DevKit Board (38-Pin): On-board low-latency microcontroller handling real-time sensor polling, safety reflex interrupts, motor PWM generation, and UDP communication.
       Raspberry Pi 3 (Stationary or Onboard): High-level host computer running the web app interface, remote manual controls, and SLAM/navigation planning over Wi-F
     
  3. Power & Protection
       14.4V Lithium-ion Battery Pack: Primary high-current power supply for all motors and logic.
       XL4015 DC-DC Buck Converter: Steps down the unregulated 14.4V battery rail to a stable 5.00V DC for the ESP32 VIN and LiDAR logic.
       Inline Fuse Holder & 10A Blade Fuse: Installed on the positive battery lead to prevent fires or board damage during motor stalls or short circuits.
       Main Power Switch: Physical toggle switch to isolate the battery from the entire circuit.
       Bulk Electrolytic Capacitor ($470\text{ }\mu\text{F} - 1000\text{ }\mu\text{F}$, 25V/35V): Decoupling capacitor across the TB6612 VM and GND pins to filter back-EMF spikes during motor direction changes.
       4x 1N5819 (or 1N4007) Diodes: Flyback snubber diodes wired across inductive motor loads (blower, brushes, pump) to clamp voltage spikes.
       10A Schottky Diode (e.g., SS34/SS54): Reverse-blocking diode placed on the charging contact plates to prevent the battery from backfeeding exposed dock pads.
     
  5. Motor Drivers & Power Switches
       TB6612FNG Dual H-Bridge Motor Driver: Drives the left and right differential drive wheels forward and reverse with speed control.
       4x LR7843 Optocoupler-Isolated MOSFET Modules: High-current low-side electronic switches for:
         1. 4x LR7843 Optocoupler-Isolated MOSFET Modules: High-current low-side electronic switches for:
         2. Side broom sweeper motor
         3.Main roller agitator brush motor
         4.Mopping fluid dispenser pump
     
  4. Resistors & Passive Signal Conditioning
       2x 100kiloohm Resistors: High-side legs of the voltage dividers for:
         14.4V battery pack voltage monitoring (GPIO 34)
         Charging dock contact plate voltage sensing (GPIO 35)
       2x 22kiloohm Resistors: Low-side legs of the voltage dividers (scaling ~16.8V max down to ~~ 3.03V}$ for ESP32 3.3V ADC inputs).
       10kilo ohm Resistor: Pull-up resistor for the water reservoir level switch/probe circuit.
       1kilo ohm and 2kilo ohm Resistors (Optional): Voltage divider logic level shifter if the LiDAR UART TX pin outputs 5V TTL.
  5. Chassis Motors & Actuators (Salvaged Chassis)
       Left & Right Wheel Drive Gearboxes: Dual brushed DC gearmotors for differential chassis drive.
       Vacuum Suction Turbine: Central centrifugal blower fan inside the scroll housing.
       Side Broom Motor: Gearmotor powering the perimeter edge-cleaning brush.
       Main Roller Brush Motor: Motor driving the center horizontal beater bar.
       Mopping Pump / Solenoid: Fluid pump dispensing water onto the mopping pad.
       LiDAR Spin Motor: Small brushed DC motor driving the rotating laser turret pulley.
  6. Sensors & User Interface
       360° LiDAR Turret Assembly: Optical distance sensor communicating via UART (Serial2 on GPIO 16/17).
       PD31_Hall V1.3 Magnetic Sensor Board: Hall-effect sensor detecting the presence of the dustbin magnet.
       Front Bumper Microswitches: Left and right spring-loaded collision limit switches.
       Cliff Detection IR Sensors: Downward-pointing infrared reflectance sensors to detect drop-offs and stairs.
       Water Tank Level Float / Probes: Fluid contact sensor to verify water tank availability before pumping.
       Chassis Charging Pickup Plates: Spring-loaded metal contact pads on the base of the robot.
  7. Prototyping & Assembly Hardware
       Perfboard / Stripboard (Protoboard): Baseboard for soldering permanent, vibration-resistant power buses and sockets.
       Female Pin Header Strips: Sockets for plugging in the ESP32 and TB6612 driver without soldering them directly to the board.
       18 AWG Silicone Wire (Red & Black): Flexible, high-current wiring for battery leads, power buses, and motor drivers.
       Dupont / JST-XH Connectors & Pigtails: Wire harnesses for clean connections to factory chassis plugs.
       M2.5 / M3 Nylon Standoffs & Screws: Spacers to rigidly mount all electronics inside the chassis cavity without short-circuit risks.


     
    
    
