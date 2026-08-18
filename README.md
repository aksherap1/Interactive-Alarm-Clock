# Interactive Robotic Alarm Clock

<img width="568" height="579" alt="Screenshot 2026-08-17 at 5 17 38 PM" src="https://github.com/user-attachments/assets/95f4bc26-9cca-40cb-80bf-57e447e5a552" />


An expressive 6-DOF robotic alarm clock designed in Onshape, engineered for rapid FDM/SLA prototyping, and powered by an ESP32-S3 custom PCB. The character wakes users up using biomimetic kinematic motion and personality states prior to audio alarms.

**CAD Model:** [View Onshape 3D Assembly](https://cad.onshape.com) *(Insert Onshape Public Link)*  
**Author:** Akshera Paladhi  
**Date:** August 15, 2026  

---

## Overview & Emotional States

The robot uses physical gestures and lighting to communicate status before audio triggers:

- **Sleeping:** Head slouches forward/down, ears droop, and ambient LEDs slowly breathe with a dim, soft light.
- **Waking Up:** Head lifts smoothly, pans to face the user, and ears perk up.
- **Alarm Active:** Head aligns directly with the user, ears flap straight back, ambient LEDs pulse, and I2S audio begins.
- **Snooze:** Hitting the top snooze button causes the head to drop back into a slouch while the ears relax into rest mode.

---

## Kinematics & Motion (6-DOF)

The mechanical assembly uses daisy-chained **FeeTech SCS0009** serial micro-servos to minimize wiring harnesses across moving joints:

| Articulation Stage | DOFs | Range of Motion | Mechanical Function |
| :--- | :---: | :--- | :--- |
| **Head Base (Yaw)** | 1 | 330° | Panning rotation to scan and face user |
| **Head Pivot (Pitch/Roll)** | 1 | 180° | Nodding and expressive tilting |
| **Left Ear (Yaw + Pitch)** | 2 | 330° Yaw, 180° Pitch | Independent perking, swivel, and drooping |
| **Right Ear (Yaw + Pitch)** | 2 | 330° Yaw, 180° Pitch | Independent perking, swivel, and drooping |

---

## Design for Manufacturing & Assembly (DFM / DFA)

The enclosure splits into 9 modular parts designed for supportless 3D printing and tool standardization:
- **Modular Breakdown (9 Printed Parts):**
  1. `Clock Main Base`: 6.50" x 4.63" x 0.50" octagon with filleted corners to eliminate warping; built-in pass-through slots for wiring.
  2. `Clock Cover Base`: Flat front bezel with 3.0" circular display cutout; prints flat with zero support material.
  3. `Clear Case (Screen Lens)`: Drop-in transparent PETG protective lens.
  4. `Battery Pack Cover`: Clamping plate securing a 2x 18650 sled with two accessible fasteners.
  5. `Ears (x2)`: Dual Mickey-style ear paddles with direct servo horn mounting brackets.
  6. `Box Servos (x2)`: Keyed cutouts holding the vertical yaw servo bodies.
  7. `Three Box (x3)`: Structural rectangular housings linking the pitch/yaw servos.
  8. `Bottom Chassis`: Wide footprint housing the main vertical yaw servo to keep the center of mass low.
  9. `Bottom Chassis Cover`: Underside access plate for servicing batteries and the base servo.
- **Hardware Standardization:** Standardized on M2 machine screws (0.177" clearance pockets) paired with M2 brass heat-set inserts melted into 5.0 mm deep standoffs. The entire assembly requires only a single 1.5 mm hex driver.

---

## Electronics & Custom 2-Layer PCB

The internal board separates user-facing interfaces from high-current power stages:

### Front Side (User-Facing)
- 3.0-inch circular SPI/RGB display module
- 3x Tactile push buttons (Snooze, Alarm Set, Reset/Boot)
- WS2812B addressable RGB LEDs for diffused ambient lighting

### Back Side (Power & Logic)
- **MCU:** ESP32-S3 dual-core microcontroller
- **Power Management:** AMS1117 3.3V LDO regulator + TP4056 lithium battery charger (2x 18650 Li-ion cells)
- **Audio Stage:** MAX98357A I2S audio amplifier driving a mini speaker
- **Connectivity:** JST-PH connectors for daisy-chained servos and battery power
- **Signal Integrity:** Solid copper ground plane on Layer 2 to isolate motor noise; 4.7kΩ pull-up resistors on active-low tactile lines

---

## Engineering Calculations & Validation

### 1. Neck Tilt Servo Torque Margin
```text
Torque Equation:
  τ_req = (m * g * r) * S

Variables:
  m = 0.165 kg (Head assembly: housing, LCD, PCB, hardware)
  g = 9.81 m/s²
  r = 0.048 m (Distance to Center of Mass)
  S = 1.5 (Safety Factor)

Calculation:
  τ_req = (0.165 kg * 9.81 m/s² * 0.048 m) * 1.5
  τ_req = 0.116 N·m ≈ 1.19 kg·cm

Validation:
  FeeTech SCS0009 stall torque @ 6V = 2.30 kg·cm
  Factor of Safety (FoS) = 2.30 / 1.19 = 1.93 (Stall-free continuous operation)
Stress Equation:
  σ = (M * c) / I

Parameters:
  Applied Load = 30 N (~6.7 lbf downward impact)
  Max Bending Stress (σ) = 14.2 MPa
  PETG Yield Strength (σ_y) = 45.0 MPa

Validation:
  Factor of Safety (FoS) = 45.0 / 14.2 = 3.17 (Safe against fatigue impact)
[ Weeks 1-4: EVT / Proto ] ──> [ Weeks 5-12: EVT / Validation ] ──> [ Weeks 13-20: DVT ] ──> [ Weeks 21-26: PVT / Launch ]
  • CAD kinematic check         • Mold draft angles & bosses        • Tooling & steel molds     • First-article inspection
  • 3D print load testbench     • 1m drop testing on wood           • 100k-cycle joint life     • Work instructions & SOPs
  • 2-layer PCB layout          • Wire harness fatigue cycling      • Acoustic dampening tune   • Final packaging & release
