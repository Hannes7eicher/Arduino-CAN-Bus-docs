# Arduino CAN Library

The Arduino_CAN library provides an easy-to-use interface for communicating over the Controller Area Network (CAN) protocol with Arduino boards. It enables users to read and write CAN messages with different bit rates, making it suitable for a variety of applications.

This library currently works with the [Arduino UNO R4 Minima](https://docs.arduino.cc/hardware/uno-r4-minima), [UNO R4 WiFi](https://docs.arduino.cc/hardware/uno-r4-wifi), and the [Portenta C33](https://docs.arduino.cc/hardware/portenta-c33). Please note that the UNO boards only expose 1 CAN interface, that is CAN.

The Portenta C33 has 2 CAN interfaces, CAN and CAN1.
The library serves all boards and it therefore has two examples for both CAN and CAN1.

## Features

- Support for multiple Arduino boards:

    - [UNO R4 Minima](https://docs.arduino.cc/hardware/uno-r4-minima): 1 CAN interface (CAN)

    - [UNO R4 WiFi](https://docs.arduino.cc/hardware/uno-r4-wifi): 1 CAN interface (CAN)

    - [Portenta C33](https://docs.arduino.cc/hardware/portenta-c33): 2 CAN interfaces (CAN and CAN1)

- Ability to initialize and configure the CAN hardware with different bit rates.

- Read and write CAN messages on the CAN bus.

- Support for internal loopback mode for testing.

- Error handling and error code reporting.

## Usage

### Reading CAN Messages

To read CAN messages from the CAN bus, use the following steps:

1. Include the Arduino_CAN library in your sketch:

```cpp
#include <Arduino_CAN.h>
```

2. Initialize the CAN hardware with the desired bit rate:

```cpp
void setup()
{
  Serial.begin(115200);
  while (!Serial) { }

  if (!CAN.begin(CanBitRate::BR_250k))
  {
    Serial.println("CAN.begin(...) failed.");
    for (;;) {}
  }
}
```

3. In the loop() function, check for available CAN messages and read them:

```cpp
void loop()
{
  if (CAN.available())
  {
    CanMsg const msg = CAN.read();
    Serial.println(msg);
  }
}
```

### Writing CAN Messages

To write CAN messages on the CAN bus, follow these steps:

1. Include the Arduino_CAN library in your sketch:

```cpp
#include <Arduino_CAN.h>
```

2. Initialize the CAN hardware with the desired bit rate:

```cpp
void setup()
{
  Serial.begin(115200);
  while (!Serial) { }

  if (!CAN.begin(CanBitRate::BR_250k))
  {
    Serial.println("CAN.begin(...) failed.");
    for (;;) {}
  }
}
```

3. Define the CAN message to be transmitted:

```cpp
static uint32_t const CAN_ID = 0x20;
static uint32_t msg_cnt = 0;

void loop()
{
  uint8_t const msg_data[] = {0xCA, 0xFE, 0, 0, 0, 0, 0, 0};
  memcpy((void *)(msg_data + 4), &msg_cnt, sizeof(msg_cnt));
  CanMsg msg(CAN_ID, sizeof(msg_data), msg_data);

  if (int const rc = CAN.write(msg); rc < 0)
  {
    Serial.print("CAN.write(...) failed with error code ");
    Serial.println(rc);
    for (;;) { }
  }

  msg_cnt++;
  delay(1000);
}
```

## Examples

The Arduino_CAN library comes with example sketches to demonstrate reading and writing CAN messages. You can find them in the Arduino IDE under **File** > **Examples** > **Arduino_CAN**.

## API Reference

The API reference for the Arduino_CAN library is as follows:

### bool CAN.begin(CanBitRate can_bitrate)

Initializes the CAN hardware with the specified bit rate.

**Parameters:**

- `can_bitrate`: The desired bit rate for the CAN communication. It should be one of the values from the `CanBitRate` enum.

**Returns:**

- `true` if initialization is successful, `false` otherwise.

### int CAN.write(CanMsg const & msg)

Transmits a CAN message on the CAN bus.

**Parameters:**

- `msg`: The `CanMsg` object representing the CAN message to be transmitted.

**Returns:**

- The number of bytes written on success, or a negative error code on failure.

### size_t CAN.available()

Checks if there is any CAN message available to read.

**Returns:**

- The number of CAN messages available to read.

### CanMsg CAN.read()

Reads a CAN message from the CAN bus.

**Returns:**

- The `CanMsg` object representing the read CAN message.

### bool CAN.enableInternalLoopback()

Enables the internal loopback mode for testing. In this mode, transmitted messages are received internally.

**Returns:**

- `true` if the internal loopback mode is enabled successfully, `false` otherwise.

### bool CAN.disableInternalLoopback()

Disables the internal loopback mode.

**Returns:**

- `true` if the internal loopback mode is disabled successfully, `false` otherwise.

### bool CAN.isError(int & err_code)

Checks if an error occurred during CAN communication.

**Parameters:**

- `err_code`: A reference to an integer that will store the error code if an error occurred.

**Returns:**

- `true` if an error occurred, `false` otherwise.

### void CAN.clearError()

Clears the error flag and error code.

## License
This library is released under the GNU Lesser General Public License version 2.1.

