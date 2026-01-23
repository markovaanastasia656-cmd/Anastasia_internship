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
- When the button is **not pressed** → output is LOW (0V)
- When the button is **pressed** → output is HIGH (~5V)
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



# Sensor 2: KY-018 Photoresistor Module

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


# Sensor 3: KY-028 Temperature Sensor Module

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
