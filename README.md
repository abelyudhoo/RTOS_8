# STM32 LED Control with FreeRTOS

This project demonstrates an STM32 microcontroller application that uses FreeRTOS to manage multiple tasks controlling LEDs with different behaviors. It includes semaphore synchronization to manage shared resources safely.

---


https://github.com/user-attachments/assets/8b9c18cd-f5ee-43aa-9461-0e6ebb3f7187


## Features

1. **Multitasking with FreeRTOS**:
   - **Green LED Task**: Blinks the green LED and accesses shared data with semaphore protection.
   - **Red LED Task**: Blinks the red LED and also accesses the shared resource, synchronized using a semaphore.
   - **Orange LED Task**: Toggles the orange LED at a fast rate without accessing shared resources.

2. **Semaphore for Critical Section**:
   - A semaphore ensures that only one task at a time accesses the shared data.

3. **Customizable Parameters**:
   - `WaitTimeMilliseconds` controls the semaphore timeout period.

4. **Error Handling**:
   - Includes a dedicated `Error_Handler` function for debugging.

---

## Folder Structure

```
.
├── main.c          # Main program file
├── LICENSE         # Licensing information
└── README.md       # Project documentation
```

---

## Dependencies

### Software
- STM32CubeIDE (or compatible STM32 development environment).
- HAL Library for STM32.
- FreeRTOS (included in STM32CubeMX).

### Hardware
- STM32 Microcontroller (e.g., STM32F4, STM32F1, etc.).
- LEDs connected to GPIO pins (`GPIO_PIN_0`, `GPIO_PIN_1`, `GPIO_PIN_3`).

---

## GPIO Pin Mapping

| Pin         | LED Color |
|-------------|-----------|
| GPIO_PIN_0  | Green     |
| GPIO_PIN_1  | Red       |
| GPIO_PIN_3  | Orange    |

---

## How to Run

1. **Setup the Project**:
   - Open the project in STM32CubeIDE.
   - Ensure that FreeRTOS is enabled in STM32CubeMX.

2. **Build and Flash**:
   - Build the project and flash it to the STM32 microcontroller using a debugger (e.g., ST-LINK).

3. **Observe Behavior**:
   - The green and red LEDs will blink with synchronized access to shared data.
   - The orange LED will toggle independently.

---

## Code Highlights

### Semaphore Creation
```c
osSemaphoreDef(CriticalResourceSemaphore);
CriticalResourceSemaphoreHandle = osSemaphoreCreate(osSemaphore(CriticalResourceSemaphore), 1);
```

### Shared Data Access
```c
void accessSharedData(void)
{
    if (startFlag == 1)
    {
        startFlag = 0;
    }
    else
    {
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_2, GPIO_PIN_SET);
    }
    HAL_Delay(1000);
    startFlag = 1;
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_2, GPIO_PIN_RESET);
}
```

### Green LED Task
```c
void GreenLedTask(void const * argument)
{
    for(;;)
    {
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_0, GPIO_PIN_SET);
        osSemaphoreWait(CriticalResourceSemaphoreHandle, WaitTimeMilliseconds);
        accessSharedData();
        osSemaphoreRelease(CriticalResourceSemaphoreHandle);
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_0, GPIO_PIN_RESET);
        osDelay(200);
    }
}
```

---

## Customization

- Adjust `WaitTimeMilliseconds` to modify semaphore wait times.
- Modify `HAL_Delay` in `accessSharedData` to alter the shared resource behavior.
