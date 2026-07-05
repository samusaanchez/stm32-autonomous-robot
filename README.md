# STM32 Autonomous Robot Car — Bare-Metal C

**Register-level firmware for an autonomous robot car: PWM motor control, dual ultrasonic ranging via input/output capture, ADC speed control, and Bluetooth remote operation — no HAL, no RTOS.**

| | |
|---|---|
| **Type** | Embedded systems — bare-metal firmware + hardware integration |
| **Authors** | Roberto Mancera González, Diego Poncela Malpartida, Samuel Sánchez García (Group 61 — joint project, shared contribution) |
| **Context** | Universidad Carlos III de Madrid (UC3M), microprocessor systems course |
| **Status** | Complete — validated on hardware |

---

## System overview

Incremental four-stage build of a robot car on an STM32 board, programmed **directly at register level in C** (CMSIS-style, no HAL libraries):

1. **Motor control** — GPIO + motor driver (IN1–IN4 via PB10–PB13), direction control
2. **Ultrasonic ranging + buzzer** — front and rear HC-SR04-type sensors driven by hardware timers
3. **Speed regulation** — potentiometer read via ADC, PWM duty-cycle control, distance-based deceleration and autonomous turning
4. **Bluetooth teleoperation** — HC-06 module over USART, command reception and response, plus fully autonomous mode

## Peripheral architecture (register-level)

| Peripheral | Configuration |
|---|---|
| **TIM2 (PWM)** | CH3/CH4 output-compare PWM for motor speed; prescaler 3199 |
| **TIM3 (ultrasonics)** | CH1/CH3 as output compare (trigger generation, 40 ms interrupt cadence via CCR = 40000, PSC = 31, ARR = 0xFFFF); CH2/CH4 as input capture (echo timing → distance calculation); per-channel interrupts via DIER + NVIC |
| **TIM2 (timebase)** | 4 output-compare channels for software timing: ADC sampling (2 s), 0.5 s turn delays, intermittent buzzer (0.1 s), button debounce (0.2 s) |
| **ADC** | PA2, 10-bit resolution, single conversion, end-of-conversion interrupt (potentiometer speed setpoint) |
| **USART** | PB6/PB7 ↔ HC-06 Bluetooth module, interrupt-driven RX/TX |
| **EXTI** | User-button interrupt (SYSCFG + IMR + NVIC), debounced in software |

## Engineering lessons documented

- Incorrect timer/interrupt initialization silently disabling per-channel interrupts — diagnosed and fixed
- Rear-ultrasonic PWM configuration required reworking the speed-control logic rather than mirroring the front sensor
- Battery voltage sag degrading motor and sensor behavior in untethered operation — identified through systematic testing

## Repository contents

```
docs/    Project report (Spanish): block diagram, wiring schematic, peripheral configuration, per-stage retrospective
src/     Firmware source (to be uploaded)
```

## Skills demonstrated

`STM32` `bare-metal C` `register-level programming` `hardware timers (IC/OC/PWM)` `ADC` `USART` `interrupt-driven design (NVIC/EXTI)` `sensor integration` `hardware debugging`
