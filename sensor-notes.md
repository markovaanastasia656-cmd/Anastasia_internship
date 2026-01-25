# Sensor Notes
This file contains experimental notes, wiring information, and test results
for sensors used during the Embedded Systems & Sensor Integration Internship
It includes hardware setup, software code, test results, and references.  
Multiple microcontrollers can be recorded here (Arduino, Raspberry Pi, etc

---


# Sensor 1: KY-004 Taster Module

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
# Sensor 2: KY-016 RGB 5mm LED Module

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


# Sensor 3: KY-017 Tilt Switch Module

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


# Sensor 4: KY-018 Photoresistor Module

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


# Sensor 5: KY-020 Tilt Switch Module

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


# Sensor 6: KY-022 Infrared Receiver Module

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



# Sensor 7: KY-028 Temperature Sensor Module

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
