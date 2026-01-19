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

int button = 10; // KY-004 signal
int value;

void setup() {
  pinMode(button, INPUT);
  digitalWrite(button, HIGH); // internal pull-up
  Serial.begin(9600);
  Serial.println("KY-004 Button test");
}

void loop() {
  value = digitalRead(button);
  if (value == LOW) { // pressed
    Serial.println("Button pressed");
  } else {
    Serial.println("Button not pressed");
  }
  delay(300);
}



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


---


