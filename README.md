# STM32 Superloop-Based Embedded System

A cooperative multitasking embedded system implemented on the STM32F446RE Nucleo Board using a superloop architecture.  
This project demonstrates periodic execution of LED blinking, push button monitoring, and IR sensor data acquisition using non-blocking software timers.

---

## Overview

This project implements a simple foreground-background embedded architecture using a continuous `while(1)` superloop.  
Multiple periodic tasks are executed sequentially without using an RTOS.

The application performs:

- LED blinking at fixed intervals
- Push button state monitoring
- IR sensor data acquisition using ADC

The project is developed using:

- STM32CubeMX
- STM32CubeIDE
- STM32 HAL Library

---

## Features

- Superloop-based task scheduling
- Non-blocking software timer implementation
- LED blinking using GPIO
- Push button input handling
- IR sensor interfacing using ADC
- Cooperative multitasking without RTOS

---

## Hardware Requirements

- STM32 Nucleo-F446RE Development Board
- IR Sensor Module
- Jumper Wires
- USB Type-A to Mini-B Cable

---

## Software Requirements

- STM32CubeIDE
- STM32CubeMX

---

## Peripheral Configuration

| Peripheral | Configuration |
|------------|----------------|
| PA5 | GPIO Output (LED) |
| PC13 | GPIO Input Pull-Up (User Button) |
| PA0 | ADC1_IN0 (IR Sensor Input) |
| SysTick | 1 ms Time Base |

---

## Working Principle

The system uses software counters updated through `HAL_GetTick()` to schedule multiple tasks at different time intervals.

### Task Scheduling

| Task | Interval |
|------|-----------|
| LED Blink | 500 ms |
| Button Read | 50 ms |
| IR Sensor Read | 200 ms |

Each task executes only when its corresponding counter reaches the predefined time interval.

---

## Superloop Architecture

The program continuously runs inside an infinite loop:

```c
while(1)
{
    // Update counters
    // Execute LED task
    // Execute button task
    // Execute ADC task
}
```

This approach enables cooperative multitasking without blocking delays.

---

## Source Code

```c
while (1)
{
    uint32_t now_ms = GetTimeMs();
    uint32_t dt = now_ms - last_time_ms;
    last_time_ms = now_ms;

    led_counter_ms += dt;
    button_counter_ms += dt;
    ir_counter_ms += dt;

    /* LED Task */
    if (led_counter_ms >= 500)
    {
        led_counter_ms = 0;
        HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
    }

    /* Button Task */
    if (button_counter_ms >= 50)
    {
        button_counter_ms = 0;

        GPIO_PinState raw =
            HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13);

        if (raw == GPIO_PIN_RESET)
        {
            button_pressed = 1;
            HAL_GPIO_WritePin(GPIOA,
                              GPIO_PIN_5,
                              GPIO_PIN_SET);
        }
        else
        {
            button_pressed = 0;
        }
    }

    /* IR Sensor Task */
    if (ir_counter_ms >= 200)
    {
        ir_counter_ms = 0;

        HAL_ADC_Start(&hadc1);
        HAL_ADC_PollForConversion(&hadc1, 10);

        if (HAL_ADC_GetState(&hadc1)
            & HAL_ADC_STATE_REG_EOC)
        {
            ir_value =
                (uint16_t)HAL_ADC_GetValue(&hadc1);
        }

        HAL_ADC_Stop(&hadc1);
    }
}
```

---

## Build and Run

1. Open the project in STM32CubeIDE
2. Connect the STM32 board via USB
3. Build the project
4. Flash the firmware to the board
5. Run the program
6. Observe:
   - LED blinking
   - Button press detection
   - IR sensor ADC value updates

---

## Expected Output

- LED toggles every 500 ms
- User button state is detected correctly
- IR sensor values change according to object distance

---

## Project Structure

```text
├── Core/
├── Drivers/
├── README.md
├── STM32_Superloop.ioc
└── main.c
```

---

## Learning Outcomes

- Understanding superloop architecture
- Cooperative multitasking in embedded systems
- GPIO input/output handling
- ADC interfacing on STM32
- Non-blocking task scheduling
- STM32 HAL programming

---

## Future Improvements

- Add UART debugging
- Implement interrupt-driven button handling
- Integrate FreeRTOS task scheduler
- Add sensor threshold-based actions

---

## Author

**Ayush Jangra**  
ECE Student | Chitkara University
