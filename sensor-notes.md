# Sensor Notes
This file contains experimental notes, wiring information, and test results
for sensors used during the Embedded Systems & Sensor Integration Internship
It includes hardware setup, software code, test results, and references.  
Multiple microcontrollers can be recorded here (Arduino, Raspberry Pi, etc

---
# Sensor 1: KY-001 Temperature Sensor (DS18B20)

### Basic Information
- Sensor name: KY-001 Temperature Sensor Module (DS18B20)
- Purpose: Measure temperature with high precision and use in one-wire networks
- Interface: 1-Wire digital communication
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-001]

### How it works
- The KY-001 module uses a **DS18B20 digital temperature sensor**
- It communicates using the **1-Wire bus protocol**
- Only one data line plus ground and power is needed for communication
- The sensor can operate in “parasite power” mode (drawing power from the data line) or with external supply
- Each DS18B20 has a **unique 64-bit serial code**, allowing multiple sensors on the same bus
- Measurement range: **-55°C to +125°C**, accuracy ±0.5°C in -10°C to +85°C range

---

### Hardware Setup

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → Digital Pin 4

  <img width="252" height="238" alt="image" src="https://github.com/user-attachments/assets/9d8d80b6-f391-4890-b764-e31fa7b176f0" />


---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- **Libraries required:**
  - OneWire (by Paul Stoffregen) — for 1-Wire protocol
  - DallasTemperature (by Miles Burton) — for DS18B20 temperature functions
- Libraries can be installed via Arduino IDE Library Manager

#### Arduino Sketch

```cpp

// Include required libraries for 1-Wire and temperature control
#include <OneWire.h>               // 1-Wire communication
#include <DallasTemperature.h>     // DS18B20 sensor support

// digital pin connected to the DS18B20 data line
#define KY001_Signal_PIN 4         // Connect signal to Arduino pin 4

// Setup OneWire instance for communication
OneWire oneWire(KY001_Signal_PIN);

// Pass OneWire reference to DallasTemperature library
DallasTemperature sensors(&oneWire);

void setup() {
  // Start serial communication for monitoring
  Serial.begin(9600);              
  Serial.println("KY-001 Temperature measurement");

  // Initialize the temperature sensor
  sensors.begin();                 
}

void loop() {
  // Request temperature measurements from DS18B20
  sensors.requestTemperatures();   

  // Read the temperature in Celsius from the first sensor on the bus
  float tempC = sensors.getTempCByIndex(0);

  // Print the temperature value
  Serial.print("Temperature: ");
  Serial.print(tempC);
  Serial.println(" °C");

  // Wait 1 second before next reading
  delay(1000);
}

// Expected behavior:
// - Serial Monitor should print temperature in °C every second.
// - Temperature reflects ambient conditions.
// - Temperature range capability: -55°C to +125°C.
```
---
# Sensor 2: KY-002 Vibration Switch Module

### Basic Information
- Sensor name: KY-002 Vibration Switch Module
- Purpose: Detect shocks and vibrations
- Interface: Digital output (switch)
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-002]

### How it works
- The KY-002 module detects mechanical vibrations and shocks
- It consists of an outer conductive casing and an internal spring
- When a shock or vibration occurs, the spring makes contact with the casing
- This closes an internal switch and generates a **digital signal** change
- Useful for security systems (door/window vibration), machine vibration monitoring, and shock detection projects

---

### Hardware Setup 

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in digitalRead())

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → Pin 10

  <img width="234" height="225" alt="image" src="https://github.com/user-attachments/assets/79420e7f-ff6b-402c-85bc-9a5fc5a8d8f8" />


#### Arduino Sketch

```cpp

// Declare the digital pin connected to the KY-002 signal
int vib = 10; // KY-002 signal pin connected to Arduino pin 10

// Variable to store current sensor value
int value;

void setup() {
  // Set the vibration sensor pin as input
  pinMode(vib, INPUT);

  // Enable internal pull-up resistor to avoid floating state
  digitalWrite(vib, HIGH);

  // Start serial communication with the computer
  Serial.begin(9600);

  // Print startup message
  Serial.println("KY-002 Vibration detection");
}

void loop() {
  // Read the current signal from the vibration sensor
  value = digitalRead(vib);

  // If a vibration is detected (switch closes),
  // the value becomes LOW
  if (value == LOW) {
    // Print detection message
    Serial.println("Vibration detected");

    // Wait 100 ms to avoid rapid repeated prints
    delay(100);
  }
}

// Expected behavior:
// - No vibration → Output remains HIGH (no message printed)
// - Vibration detected → Output becomes LOW → "Vibration detected" printed
```
---

# Sensor 3: KY-003 Hall Magnetic Field Sensor

### Basic Information
- Sensor name: KY-003 Hall Magnetic Field Sensor
- Purpose: Detect magnetic fields (on/off)
- Interface: Digital output (Hall effect switch)
- Operating voltage: 5V (due to integrated LED and sensor design)
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-003]
---

### How it works
- The KY-003 module includes a Hall-effect switch based on the **A3144 chipset**  
- Hall-effect switches change output state when exposed to a magnetic field  
- When a magnetic field is present near the sensor:  
  - The internal transistor switches  
  - The **digital output is pulled LOW**  
  - The integrated status LED lights (active-low behavior)  
- When no magnetic field is present:  
  - The output stays HIGH (open collector with pull-up)  
- Useful for detecting magnets, proximity switches, wheel rotation sensors, or as a simple digital magnetic switch.

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → Pin 10

---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in `digitalRead()`)

#### Arduino Sketch

```cpp

// Declare the digital pin connected to the KY-003 signal
int hallPin = 10; // Hall sensor signal connected to Arduino pin 10

// Storage for sensor reading
int hallValue;

void setup() {
  // Initialize the sensor pin as input
  // Internal pull-up resistor ensures a stable HIGH when no magnet is present
  pinMode(hallPin, INPUT);
  digitalWrite(hallPin, HIGH);

  // Start serial communication
  Serial.begin(9600);  // Must match Serial Monitor baud rate

  // Initial message
  Serial.println("KY-003 Magnetic field detection");
}

void loop() {
  // Read the digital state from the Hall sensor
  hallValue = digitalRead(hallPin);

  // When a magnetic field is near, the module output goes LOW
  if (hallValue == LOW) {
    Serial.println("Magnetic field detected");
    delay(100); // Short pause
  }
}

// Expected behavior:
// - No magnetic field → Output = HIGH → no print
// - Magnetic field present → Output = LOW → "Magnetic field detected"
// - Red LED on the sensor blinks when magnetic field is deteected

```
---

# Sensor 4: KY-004 Taster Module

### Basic Information
- Sensor name: KY-004 Taster Module
- Purpose: Detect button press (on/off state)
- Interface: Digital output
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-004]

### How it works
- KY-004 contains a push button with a built-in pull-down resistor
- When the button is not pressed → output is LOW (0V)
- When the button is pressed → output is HIGH (~5V)
- Useful for user input, simple on/off control, or triggering events in Arduino projects

---

### Hardware Setup 

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in digitalRead())

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → Pin 10

#### Arduino Sketch

```cpp

// Declare the digital pin number connected to the KY-004 signal pin
int button = 10;   // KY-004 signal pin is connected to Arduino pin 10

// Variable to store the current button state (HIGH or LOW)
int value;

void setup() {
  // Set the button pin as INPUT (we will read a signal from the sensor)
  pinMode(button, INPUT);

  // Enable Arduino's internal pull-up resistor on this pin
  // This keeps the signal HIGH when the button is not pressed
  digitalWrite(button, HIGH);

  // Start serial communication with the computer
  // 9600 must match the baud rate in the Serial Monitor
  Serial.begin(9600);

  // Print a test message once when the program starts
  Serial.println("KY-004 Button test");
}

void loop() {
  // Read the current signal from the button pin
  // HIGH = not pressed, LOW = pressed
  value = digitalRead(button);

  // Check if the button is pressed
  if (value == LOW) {
    // Button is pressed, so print a message
    Serial.println("Button pressed");
  } else {
    // Button is not pressed, so print a different message
    Serial.println("Button not pressed");
  }

  // Wait 300 milliseconds before the next reading
  // This makes the output easier to read
  delay(300);
}

```
---
# Sensor 5: KY-005 Infrared Transmitter Module

### Basic Information
- Sensor name: KY-005 Infrared Transmitter Module
- Purpose: Emit infrared light for remote control or data transmission
- Interface: Digital output (IR LED)
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-005]

---

### How it works
- The KY-005 module contains an infrared LED that emits invisible infrared light
- Infrared light can be used to send signals to receivers such as receiver sensors or remote controlled devices
- The LED emits at around 940 nm wavelength, not visible to the human eye
- To protect the LED and avoid damage, a series resistor must be used depending on input voltage
- Useful for projects where remote control or IR communication is needed (paired with an IR receiver like KY-022)

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → Pin 3 (digital output for IR LED)

<img width="250" height="279" alt="image" src="https://github.com/user-attachments/assets/7a484251-de23-43b5-9556-6c120c2a0f13" />


> Notes:  
> - A series resistor is recommended when driving the IR LED directly  
> - For 5V operation, use a ~220 Ω resistor in series with the Signal pin before the LED

---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required for simple blink (digitalWrite)

#### Arduino Sketch

```cpp

// Declare the digital pin connected to the KY-005 signal
int irPin = 3; // KY-005 emitter pin connected to Arduino pin 3

void setup() {
  // Set the IR transmitter pin as OUTPUT
  pinMode(irPin, OUTPUT);

  // Start serial communication for debugging
  Serial.begin(9600);

  // Print startup message
  Serial.println("KY-005 IR Transmitter Test");
}

void loop() {
  // Turn the IR LED ON
  digitalWrite(irPin, HIGH); // Start emitting IR
  Serial.println("IR ON"); // Debug message
  delay(500); // Keep IR on for 500 ms

  // Turn the IR LED OFF
  digitalWrite(irPin, LOW); // Stop emitting IR
  Serial.println("IR OFF"); // Debug message
  delay(500); // Keep IR off for 500 ms

  // This blinking pattern makes the IR LED emit pulses
  // The receiver (e.g., KY-022) will see this pattern
}

// Expected behavior:
// - “IR ON” and “IR OFF” messages alternate every 500 ms
// - IR LED emits pulses that can be detected by an IR receiver module
// - When using KY-022 paired with this transmitter, reception code can detect IR signal
```
### Experiment: KY-005 (IR Transmitter) + KY-022 (IR Receiver)
- Send infrared signals using KY-005
- Receive and detect the signal using KY-022
- Verify IR transmission using two modules on one Arduino

---

### Hardware Setup

#### Arduino + Breadboard + Sensors
- **KY-005 (IR Transmitter)**
  - VCC → 5V 
  - GND → GND 
  - S (Signal) → Arduino Pin 3 via 220Ω resistor (resistor in series)

- **KY-022 (IR Receiver)**
  - VCC → 5V (red rail)
  - GND → GND (blue rail)
  - OUT → Arduino Pin 11

- **220Ω resistor for KY-005 S pin**
  - One leg → same hole as KY-005 S (e.g., A3)
  - Other leg → Arduino Pin 3 (different column, e.g., B3)

---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: `IRremote` library recommended for 38kHz signal generation

---

### Arduino Sketch (Basic Digital Test)

```cpp
#include <IRremote.h>  // Recommended for accurate 38kHz signal

int irSendPin = 3;     // KY-005 S pin through 220Ω
int irReceivePin = 11; // KY-022 OUT pin

void setup() {
  pinMode(irSendPin, OUTPUT);   // Send IR signal
  pinMode(irReceivePin, INPUT); // Read IR signal
  Serial.begin(9600);
  Serial.println("IR transmitter/receiver test start");
}

void loop() {
  // Send a short IR pulse (for basic digital test)
  digitalWrite(irSendPin, HIGH); // Turn IR LED ON
  delay(10);                     // 10ms pulse
  digitalWrite(irSendPin, LOW);  // Turn IR LED OFF

  // Read receiver
  int signal = digitalRead(irReceivePin);

  // Print result
  if (signal == HIGH) {
    Serial.println("IR signal detected");
  } else {
    Serial.println("No IR signal");
  }

  delay(500); // Wait 0.5s before next pulse
}
```
---


# Sensor 6: KY-006 Passive Piezo-Buzzer Module

### Basic Information
- Sensor name: KY-006 Passive Piezo-Buzzer Module
- Purpose: Generate simple tones or alarm sounds under program control
- Interface: Digital output (PWM or square wave control)
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-006]

---

### How it works
- The KY-006 is a **passive piezoelectric buzzer module** that does **not** contain an internal oscillator.  
- Instead, it requires a **PWM (Pulse Width Modulated) or square wave output** to produce sound.  
- The frequency of the PWM signal (i.e., how fast the signal toggles) determines the tone of the buzzer.  
- Typical usable tone range achievable is approximately **1.5 kHz to 2.5 kHz**.  
- Works with Arduino and other microcontrollers by outputting a square wave from a digital pin.

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V  
- GND → GND  
- Signal → Pin 8

<img width="253" height="254" alt="image" src="https://github.com/user-attachments/assets/18a59434-3047-450d-bab6-30776473b657" />


---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno  
- Programming language: C / Arduino  
- Library: None required (uses basic digitalWrite and PWM or tone())

#### Arduino Sketch

```cpp

// Pin connected to the KY-006 buzzer signal
int buzzer = 8;  // Set digital pin 8 as buzzer control pin

void setup() {
  // Set the buzzer pin as output
  pinMode(buzzer, OUTPUT);  // OUTPUT mode allows us to send signals

  // Print a startup message
  Serial.begin(9600);
  Serial.println("KY-006 Passive Buzzer Test");
}

void loop() {
  // First tone: Approx ~1.5 kHz
  for (int i = 0; i < 80; i++) {
    digitalWrite(buzzer, HIGH); // Turn signal high
    delay(1);                   // 1 ms HIGH
    digitalWrite(buzzer, LOW);  // Turn signal low
    delay(1);                   // 1 ms LOW
  }

  // Pause between tones
  delay(1000);  // 1 second pause

  // Second tone: Approx ~1 kHz
  for (int i = 0; i < 100; i++) {
    digitalWrite(buzzer, HIGH); // Turn signal high
    delay(2);                   // 2 ms HIGH
    digitalWrite(buzzer, LOW);  // Turn signal low
    delay(2);                   // 2 ms LOW
  }

  // Pause between loops
  delay(1000);  // 1 second pause
}

// Expected behavior:
// - Buzzer will make two different tones in each loop
// - First loop produces a higher-frequency (shorter delay) sound
// - Second loop produces a lower-frequency (longer delay) sound

```
---

# Sensor : KY-010 Light Barrier (Photoelectric Sensor)

### Basic Information
- Sensor name: KY-010 Light Barrier (Photoelectric Sensor)
- Purpose: Detect interruption of a light beam (presence/movement)
- Interface: Digital output
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-010]

---

### How it works
- The KY-010 module contains a **light barrier (photoelectric sensor)**  
- It emits a signal when the light path is **not interrupted**  
- If something **blocks the light beam**, the signal is **interrupted**  
- This makes the module useful for detecting **passage, movement, or object interruption**

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → Pin 10

  <img width="303" height="278" alt="image" src="https://github.com/user-attachments/assets/7f202f1b-ef45-40bc-bceb-d881097552e1" />


---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in digitalRead())

#### Arduino Sketch

```cpp

int barrier = 10; // KY-010 signal pin connected to Arduino pin 10
int value;        // Variable to store sensor reading

void setup() {
  // Set the sensor pin as input with pull-up resistor for stability
  pinMode(barrier, INPUT_PULLUP);

  // Start serial communication with computer
  Serial.begin(9600);

  // Print a startup message
  Serial.println("KY-010 Light Barrier Test Started");
}

void loop() {
  // Read digital state from light barrier
  value = digitalRead(barrier);

  // HIGH = light path clear, LOW = beam blocked
  if (value == HIGH) {
    Serial.println("Light path clear");  // Output when no object blocks the beam
  } else {
    Serial.println("Light blocked");     // Output when object blocks the beam
  }

  // Wait 300 milliseconds before next reading
  delay(300);
}

// Expected behavior:
// - No object in the beam → HIGH → "Light path clear" printed
// - Object interrupts beam → LOW → "Light blocked" printed
```
---

# Sensor : KY-011 2-Color 5mm LED Module

### Basic Information
- Sensor name: KY-011 2-Color 5mm LED Module
- Purpose: Visual indicator using red and green LED colors
- Interface: Digital output (LED control)
- Operating voltage: 2.0 V – 2.5 V forward voltage per LED
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-011]

---

### How it works
- The KY-011 module contains two individual LEDs (red and green) in a single package with a **common cathode** configuration.
- A common cathode means both LEDs share the **same negative (GND) connection**.
- Each LED has its own positive input pin:
  - When the red LED input receives a HIGH signal → **red light turns on**
  - When the green LED input receives a HIGH signal → **green light turns on**
- With simple ON/OFF control or PWM, you can show different visual states or mix brightness to indicate statuses.
- Used for visual feedback in projects (status indicators, alerts, simple color signals).

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → (not directly to module; see note below)
- GND → GND
- Pin 9 → Green LED input
- Pin 10 → Red LED input

  <img width="215" height="277" alt="image" src="https://github.com/user-attachments/assets/868c7165-c2af-40b2-a2ee-c3cfafa1f48a" />


> **Important note:** LEDs require a **series resistor** to limit current and avoid damage.
> - Use ~220 Ω resistors for 5 V supply.

---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in digitalWrite() or analogWrite())

#### Arduino Sketch (ON/OFF example)

```cpp

// Declare LED control pins
int ledRed = 10;    // KY-011 red LED input
int ledGreen = 9;   // KY-011 green LED input

void setup() {
  // Set both LED pins as OUTPUT
  pinMode(ledRed, OUTPUT); 
  pinMode(ledGreen, OUTPUT); 
}

void loop() {
  // Turn on red LED, turn off green
  digitalWrite(ledRed, HIGH);   // Red LED ON
  digitalWrite(ledGreen, LOW);  // Green LED OFF
  delay(3000);                  // Wait for 3 seconds

  // Turn off red, turn on green
  digitalWrite(ledRed, LOW);    // Red LED OFF
  digitalWrite(ledGreen, HIGH); // Green LED ON
  delay(3000);                  // Wait for another 3 seconds
}

// Expected behavior:
// - Red LED lights for 3 seconds
// - Green LED lights for 3 seconds
// - Cycle repeats
```
---

# Sensor : KY-012 Active Piezo-Buzzer Module

### Basic Information
- Sensor name: KY-012 Active Piezo‑Buzzer Module
- Purpose: Generate an audible sound when activated
- Interface: Digital output (driven by voltage)
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-012]
### How it works
- The KY‑012 module contains an active buzzer element  
- An **active buzzer** produces its own square‑wave signal internally and does **not** require an external PWM or tone generation signal  
- When the signal pin receives a voltage ≥ 3.3 V, the buzzer begins producing sound at around 2.5 kHz  
- It draws up to ~30 mA and produces a loud sound (~85 dB), suitable for alarms or audible feedback

---

### Hardware Setup 

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built‑in digitalWrite())

#### PIN Connection - Arduino Uno (Joy‑IT R3 DIP)
- Signal → Pin 13
- +V → 5V
- GND → GND

<img width="282" height="271" alt="image" src="https://github.com/user-attachments/assets/01b9d2bf-fafa-4a3c-9d60-9bf8a0c1b995" />


#### Arduino Sketch

```cpp

// Declare the digital pin connected to the buzzer
int buzzer = 13; // Connects KY-012 signal to Arduino pin 13

void setup() {
  // Set the buzzer pin as an output
  pinMode(buzzer, OUTPUT);
}

void loop() {
  // Turn the buzzer on (voltage applied)
  digitalWrite(buzzer, HIGH); // Buzzer starts producing sound

  // Wait for 4 seconds while the buzzer is sounding
  delay(4000);

  // Turn the buzzer off (no voltage)
  digitalWrite(buzzer, LOW); // Buzzer stops sound

  // Wait for 2 seconds before repeating
  delay(2000);
}

// Expected behavior:
// - The buzzer will emit a tone (~2.5 kHz) when the pin is HIGH
// - When the pin is LOW, the buzzer is silent
// - Useful for alarms, notifications, and audible feedback
```
---

# Sensor : KY-013 Temperature Sensor (NTC)

### Basic Information
- Sensor name: KY-013 Temperature Sensor (NTC Thermistor)
- Purpose: Measure ambient temperature by reading change in resistance
- Interface: Analog output (voltage varies with temperature)
- Operating voltage: 3.3 V – 5 V
- Measuring range: –55 °C to +125 °C
- Measurement accuracy: ±0.5 °C
- Known resistance: 10 kΩ
- Specific thermistor constant: 3950 Ω
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-013]
---

### How it works
- The KY-013 module contains an **NTC thermistor** (Negative Temperature Coefficient)
- An NTC thermistor has a resistance that **decreases as temperature increases**
- The sensor is used in a **voltage divider circuit**
  - A fixed resistor is paired with the thermistor
  - Measuring the divided voltage lets you estimate resistance
- Resistance can then be mathematically converted to temperature readings
- This module is suitable for precise temperature measurement tasks like monitoring systems and climate controls

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → A1 (analog input)

<img width="270" height="268" alt="image" src="https://github.com/user-attachments/assets/4f4f5502-34ad-493d-ac73-e222652d7350" />

  
---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in analogRead())

#### Arduino Sketch

```cpp

// Declare the analog pin connected to the KY-013 signal
int ntcPin = A1; // KY-013 analog output connected to Arduino analog pin A1

// Variables to store readings
double rawValue;    // raw analog reading (0–1023)
double voltage;     // voltage calculated from raw value
double temperature; // calculated temperature in °C

void setup() {
  // Initialize sensor pin as input
  pinMode(ntcPin, INPUT);

  // Start serial communication at 9600 baud
  Serial.begin(9600);

  // Print an initial message once at startup
  Serial.println("KY-013 NTC Temperature Test");
}

void loop() {
  // Read raw analog value from the sensor
  rawValue = analogRead(ntcPin);

  // Convert raw value to voltage (0–5V)
  voltage = rawValue * 5.0 / 1023.0;

  // Convert measured voltage to resistance (assuming a 10k fixed resistor)
  double resistance = ((voltage / (5.0 - voltage)) * 10000.0);

  // Convert resistance to temperature using thermistor math
  // Based on Steinhart–Hart approximation with Beta coefficient
  temperature = 1.0 / ((1.0 / 298.15) + (1.0 / 3950.0) * log(resistance / 10000.0));
  temperature = temperature - 273.15; // Convert Kelvin to °C

  // Print the measured temperature
  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" °C");

  // Wait 1 second before the next measurement
  delay(1000);
}

// Expected behavior:
// - As actual ambient temperature rises → measured temperature value increases
// - As temperature falls → measured value decreases
```
---

# Sensor : KY-016 RGB 5mm LED Module

### Basic Information
- Sensor name: KY-016 RGB 5mm LED Module
- Purpose: Visual color output using red, green, and blue LEDs
- Interface: Digital output (PWM controllable)
- Operating voltage: 3.3 V – 5 V
- Forward current: 20 mA per LED
- Forward voltage:  
  - Red: ~1.8 V  
  - Green & Blue: ~2.8 V  
- Series resistors required per color  
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-016]
---

### How it works
- The KY-016 module contains three individual LEDs: red, green, and blue.  
- These LEDs share a common cathode connection.  
- By adjusting the brightness of each LED (through PWM), many different colors can be created.  
- Human eyes interpret rapid on/off switching (PWM) as changes in brightness.  
- Useful for status indicators, color effects, and visual feedback in projects.

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC (common cathode) → GND
- Red LED → Digital Pin 10 (through 180ohm resistor)
- Green LED → Digital Pin 11 (through 100ohm resistor)
- Blue LED → Digital Pin 12 (through 100ohm resistor)

---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in digitalWrite and analogWrite)

#### Arduino Sketch

```cpp

// Define output pins for the RGB LED module
int ledRed = 10;    // Red LED connected to pin 10
int ledGreen = 11;  // Green LED connected to pin 11
int ledBlue = 12;   // Blue LED connected to pin 12

void setup() {
  // Initialize the LED pins as output
  pinMode(ledRed, OUTPUT);    // Set red LED pin as output
  pinMode(ledGreen, OUTPUT);  // Set green LED pin as output
  pinMode(ledBlue, OUTPUT);   // Set blue LED pin as output

  // Start with all LEDs off
  digitalWrite(ledRed, LOW);   // Turn off red LED
  digitalWrite(ledGreen, LOW); // Turn off green LED
  digitalWrite(ledBlue, LOW);  // Turn off blue LED

  // Inform that setup is complete
  Serial.begin(9600); // Start serial output to PC
  Serial.println("KY-016 RGB LED Test");
}

void loop() {
  // Turn red LED on, other colors off
  digitalWrite(ledRed, HIGH);   // Red on
  digitalWrite(ledGreen, LOW);  // Green off
  digitalWrite(ledBlue, LOW);   // Blue off
  delay(3000);                  // Wait 3 seconds

  // Turn green LED on
  digitalWrite(ledRed, LOW);   
  digitalWrite(ledGreen, HIGH); 
  digitalWrite(ledBlue, LOW);   
  delay(3000);                  

  // Turn blue LED on
  digitalWrite(ledRed, LOW);    
  digitalWrite(ledGreen, LOW);  
  digitalWrite(ledBlue, HIGH);  
  delay(3000);                  

  // Turn all colors off
  digitalWrite(ledRed, LOW);    
  digitalWrite(ledGreen, LOW);  
  digitalWrite(ledBlue, LOW);   
  delay(1000);
}

// Expected behavior:
// - Red on for 3 seconds
// - Green on for 3 seconds
// - Blue on for 3 seconds
// - Repeat loop
// You can add PWM via analogWrite() for mixed color effects.

```
---


# Sensor : KY-017 Tilt Switch Module

### Basic Information
- Sensor name: KY-017 Tilt Switch Module
- Purpose: Detect tilt or change in orientation
- Interface: Digital output
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-017]

---

### How it works
- The KY-017 module contains a simple tilt switch (ball/mercury switch)
- Inside the module, a small ball closes the internal contacts when tilted
- Depending on the orientation:
  - Upright position → ball closes the circuit → digital output changes
  - Tilted position → ball opens the circuit → digital output changes
- A potentiometer allows adjustment of the sensitivity / trigger point
- The module includes an LM393 comparator
- Useful for tilt detection, movement alarms, or orientation sensing

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → Pin 10

---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in digitalRead())

#### Arduino Sketch

```cpp
int tiltPin = 10;  // KY-017 signal pin is connected to Arduino pin 10

// Variable to store the current switch state
int tiltValue;

void setup() {
  // Set the tilt sensor pin as input
  pinMode(tiltPin, INPUT);

  // Start serial communication
  Serial.begin(9600);  // must match Serial Monitor baud rate

  // Print a startup message
  Serial.println("KY-017 Tilt test");
}

void loop() {
  // Read the current digital value from the tilt sensor
  tiltValue = digitalRead(tiltPin);

  // Check the sensor state
  if (tiltValue == LOW) {
    // Sensor is tilted (switch closed)
    Serial.println("Tilt detected");
  } else {
    // Sensor is not tilted (switch open)
    Serial.println("No tilt");
  }

  // Wait before next reading to make output readable
  delay(300);
}
// Expected behavior:
// - No tilt  -> Output = HIGH  -> "No tilt" is printed
// - Tilt     -> Output = LOW   -> "Tilt detected" is printed
```


---


# Sensor : KY-018 Photoresistor Module

### Basic Information
- Sensor name: KY-018 Photoresistor Module
- Purpose: Measure ambient light intensity
- Interface: Analog output
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-018]

- ### How it works
- The KY-018 module contains a photoresistor (LDR)
- Light-dependent resistance:
  - Bright light → resistance decreases → output voltage decreases 
  - Darkness → resistance increases → output voltage increases　
- Useful for brightness detection, automatic light control, and basic environmental sensing

---

### Hardware Setup 

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in analogRead())

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal  → A5

#### Arduino Sketch

```cpp
void setup() {
  
  Serial.begin(9600);  // Start serial communication for monitoring
}

void loop() {
  
  int lightValue = analogRead(A0);  // Read analog value from KY-018
  
  Serial.print("Light (analog): "); // Print description
  
  Serial.println(lightValue);       // Print measured value
  
  delay(500);                       // Wait 500ms before next reading
}

// Expected values: ~0 (dark) to ~1023 (bright light)

```


---


# Sensor : KY-020 Tilt Switch Module

### Basic Information
- Sensor name: KY-020 Tilt Switch Module
- Purpose: Detect tilt or orientation changes
- Interface: Digital output
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-020]
---

### How it works
-- The KY-020 module contains a simple tilt switch (ball inside)
- When the module is tilted, the ball closes the internal contacts
- Depending on orientation:
  - Flat / not tilted → switch open → digital output HIGH
  - Tilted → switch closed → digital output LOW
- No potentiometer or comparator included
- Useful for tilt detection, simple alarms, or orientation sensing

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → Pin 10

---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: None required (uses built-in digitalRead())

#### Arduino Sketch

```cpp

int tilt_switch = 10;  // KY-020 signal pin

// Variable to store the sensor value
int value;

void setup () {
  // Use internal pull-up resistor
  // This prevents the input pin from floating
  pinMode(tilt_switch, INPUT_PULLUP);

  // Initialize the serial monitor
  Serial.begin(9600);

  // Startup message
  Serial.println("KY-020 Inclination test");
}

void loop () {
  // Read the current signal from the sensor
  value = digitalRead(tilt_switch);

  // LOW means the switch is closed (tilted)
  if (value == LOW) {
    Serial.println("Tilted");
  } else {
    // HIGH means the switch is open (not tilted)
    Serial.println("Not tilted");
  }

  // 1 sec Delay for readable output
  delay(1000);
}

// Expected behavior:
// - No tilt → Output = HIGH → "No tilt" printed
// - Tilt   → Output = LOW  → "Tilt detected" printed

```


---


# Sensor : KY-022 Infrared Receiver Module

### Basic Information
- Sensor name: KY-022 Infrared Receiver Module
- Purpose: Receive and decode infrared (IR) remote control signals
- Interface: Digital output
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-022]

---

### How it works
- The KY-022 contains an IR receiver tuned for ~38 kHz carrier signals.
- It converts incoming IR remote light into a digital signal on its output pin.  
- When IR light modulated at 38 kHz is received, the module’s output toggles according to the encoded signal. 
- A small LED on the module flashes briefly to indicate reception.  
- This digital output needs to be decoded via software/library (e.g., IRremote) to interpret the actual data.

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Signal → Pin 2

---

### Software Setup (Arduino IDE)
- Platform: Arduino Uno
- Programming language: C / Arduino
- Library: IRremote (required for decoding IR signals)

---

#### Arduino Sketch

```cpp

#include <IRremote.h>     // Include IRremote library

int receiverPin = 2;      // KY-022 signal pin connected to Arduino pin 2
IRrecv irrecv(receiverPin);  // Create IR receiver object
decode_results results;      // Storage for decoded IR results

void setup() {
  // Initialize serial communication
  Serial.begin(9600);            // Must match Serial Monitor baud rate

  // Start the IR receiver
  irrecv.enableIRIn();           // Enable the receiver

  // Print startup message
  Serial.println("KY-022 IR Receiver Test");
}

void loop() {
  // Check if an IR code was received
  if (irrecv.decode(&results)) {
    // Print the raw value received in hexadecimal
    Serial.print("IR code received: 0x");
    Serial.println(results.value, HEX);

    // Prepare to receive the next code
    irrecv.resume();
  }
}

// Expected behavior:
// - Press a button on an IR remote
// - A hexadecimal code (e.g., 0xFFA25D) prints in Serial Monitor
// - Same button usually sends same code repeatedly
```

---



# Sensor : KY-028 Temperature Sensor Module

### Basic Information
- Sensor name: KY-028 Temperature Sensor Module (Thermistor)
- Purpose: Measure ambient temperature and detect temperature threshold
- Interface: Analog output + Digital output
- Operating voltage: 3.3 V – 5 V
- Source / Reference: [https://sensorkit.joy-it.net/en/sensors/ky-028]

---

### How it works
- The KY-028 module contains an NTC thermistor
  - NTC = Negative Temperature Coefficient
  - Temperature increases → resistance decreases
  - Temperature decreases → resistance increases
- The module provides two different outputs:

#### Analog Output
- Outputs a voltage that changes continuously with temperature
- Used to measure relative temperature values
- Read using Arduino `analogRead()`

#### Digital Output
- The module includes an LM393 comparator
- A small potentiometer sets a temperature threshold
- If temperature exceeds the threshold:
  - Digital output becomes HIGH
- Useful for simple ON/OFF temperature alerts

---

### Hardware Setup 

#### PIN Connection - Arduino Uno (Joy-IT R3 DIP)
- VCC → 5V
- GND → GND
- Pin 3 → Digital Signal 
- Pin A0 → Analog Signal (temperature value)


#### Arduino Sketch

```cpp

int analog_input = A0; // Analog output of the sensor
int digital_input = 3; // Digital output of the sensor

void setup() {
  // Set both sensor pins as input
  pinMode(analog_input, INPUT);
  pinMode(digital_input, INPUT);

  // Start serial communication with the computer
  Serial.begin(9600);

  // Print startup message
  Serial.println("KY-028 Temperature threshold measurement");
}

void loop() {

  // Variables to store current sensor values
  float analog_value;   // Voltage value from analog pin
  int digital_value;    // Threshold state (HIGH or LOW)

  // Read analog value and convert it to voltage (0–5V)
  analog_value = analogRead(analog_input) * (5.0 / 1023.0);

  // Read digital threshold output
  digital_value = digitalRead(digital_input);

  // Print analog voltage value
  Serial.print("Analog voltage value: ");
  Serial.print(analog_value, 4);
  Serial.print(" V, \t Threshold value: ");

  // Check if the threshold is reached
  if (digital_value == HIGH) {
    Serial.println("reached");
  } else {
    Serial.println("not yet reached");
  }

  // Print a separator line for readability
  Serial.println("----------------------------------------------------------------");

  // Wait 2 seconds before next measurement
  delay(2000);
}

```


---
