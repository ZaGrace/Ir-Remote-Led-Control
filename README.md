# Ir-Remote-Led-Control
This repository contains an Arduino project that uses an IR remote to control LEDs. The IR receiver decodes signals from the remote, allowing specific buttons to toggle different LEDs or turn them all off.

## Hardware Requirements
- Arduino Board (e.g., Arduino Uno)
- IR Receiver (e.g., TSOP38238)
- IR Remote
- 3 LEDs (Red, Green, Blue)
- Resistors (220Ω for each LED)
- Jumper Wires
- Breadboard

## Wiring Diagram
| Component     | Arduino Pin |
|--------------|------------|
| IR Receiver  | 7          |
| Red LED      | 9          |
| Blue LED     | 10         |
| Green LED    | 11         |

## IR Remote Button Mappings

### Old HEX Codes
| Button | HEX Code | Function |
|--------|---------|----------|
| 1      | 0xC     | Toggle Red LED On/Off |
| 2      | 0x18    | Toggle Blue LED On/Off |
| 3      | 0x5E    | Toggle Green LED On/Off |
| 4      | 0x8     | Unassigned |
| 5      | 0x1C    | Unassigned |
| 6      | 0x5A    | Unassigned |
| 7      | 0x42    | Unassigned |
| 8      | 0x52    | Unassigned |
| 9      | 0x4A    | Unassigned |
| Power  | 0x45    | Turn all LEDs OFF |
| Vol+   | 0x46    | Increase LED brightness |
| Vol-   | 0x15    | Decrease LED brightness |

### New HEX Codes
| Button | Old HEX Code | New HEX Code |
|--------|-------------|-------------|
| 1      | 0xC         | 0xF30CFF00  |
| 2      | 0x18        | 0xE718FF00  |
| 3      | 0x5E        | 0xA15EFF00  |
| 4      | 0x8         | 0xF708FF00  |
| 5      | 0x1C        | 0xE31CFF00  |
| 6      | 0x5A        | 0xA55AFF00  |
| 7      | 0x42        | 0xBD42FF00  |
| 8      | 0x52        | 0xAD52FF00  |
| 9      | 0x4A        | 0xB54AFF00  |

## Code Implementation
```cpp
#include <IRremote.h>

// Pin configuration
const int RECV_PIN = 7;  // IR receiver pin
const int redLED = 9;    // Red LED pin
const int blueLED = 10;  // Blue LED pin
const int greenLED = 11; // Green LED pin

IRrecv irrecv(RECV_PIN); // Create IR receiver object

void setup() {
  Serial.begin(9600);          // Initialize serial communication
  irrecv.enableIRIn();         // Start the receiver
  Serial.println("IR Receiver Initialized");
  pinMode(redLED, OUTPUT);     // Set LED pins as outputs
  pinMode(blueLED, OUTPUT);
  pinMode(greenLED, OUTPUT);
}

void loop() {
  if (irrecv.decode()) { // Check if data is received
    uint32_t receivedCode = irrecv.decodedIRData.decodedRawData;

    Serial.print("HEX Code: 0x");
    Serial.println(receivedCode, HEX); // Print the received HEX code

    // Process the received HEX code
    switch (receivedCode) {
      case 0xF30CFF00:  // Button 1
        digitalWrite(redLED, !digitalRead(redLED)); // Toggle red LED
        break;

      case 0xE718FF00:  // Button 2
        digitalWrite(blueLED, !digitalRead(blueLED)); // Toggle blue LED
        break;

      case 0xA15EFF00:  // Button 3
        digitalWrite(greenLED, !digitalRead(greenLED)); // Toggle green LED
        break;

      case 0xBA45FF00:  // Power On/Off button
        digitalWrite(redLED, LOW);   // Turn off all LEDs
        digitalWrite(blueLED, LOW);
        digitalWrite(greenLED, LOW);
        break;

      default:
        Serial.println("Unknown button");
        break;
    }

    irrecv.resume();  // Prepare to receive the next value
  }
}
```

## How It Works
1. The IR receiver captures signals from the remote and decodes them.
2. Each button on the remote has a unique HEX code.
3. The Arduino checks for received IR signals and maps them to actions (e.g., toggling LEDs on/off).
4. Pressing specific buttons toggles LEDs, while the power button turns all LEDs off.

## Challenges and Fixes
### 1. **Incorrect Library Usage**
   - Initially, `IrReceiver.decode()` was not recognized.
   - **Solution:** Used `irrecv.decode()` for compatibility with our setup.

### 2. **Wrong HEX Codes**
   - The first set of values retrieved were incorrect.
   - **Solution:** Used `decodedRawData` instead of `command` to get accurate results.

### 3. **No Response from Receiver**
   - The receiver initialized, but no signals were detected.
   - **Solution:** Ensured the correct IRremote library version and proper pin connection.

## Future Enhancements
- Implement LED brightness control using volume buttons.
- Assign more functions to unassigned remote buttons.
- Expand project to control additional devices such as motors or relays.

---
**Author:** Zadane  
**License:** MIT
