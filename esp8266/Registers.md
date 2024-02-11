> By using some abstraction

```cpp
// Define the LED pin
const int ledPin = 4;  // D2 on NodeMCU

void setup() {
  // Set the LED pin as an output using register manipulation
  pinModeRegister(ledPin, OUTPUT);

  // Start serial communication
  Serial.begin(115200);
}

void loop() {
  // Turn on the LED using register manipulation
  setPinRegister(ledPin, HIGH);

  // Print "Hello, World!" to the serial monitor
  Serial.println("Hello, World!");

  delay(1000);  // 1-second delay

  // Turn off the LED using register manipulation
  setPinRegister(ledPin, LOW);

  delay(1000);  // 1-second delay
}

// Helper function to set a pin mode using register manipulation
void pinModeRegister(int pin, int mode) {
  volatile uint32_t *reg = portModeRegister(digitalPinToPort(pin));
  uint32_t mask = digitalPinToBitMask(pin);

  if (mode == OUTPUT) {
    *reg |= mask;
  } else {
    *reg &= ~mask;
  }
}

// Helper function to set a pin state using register manipulation
void setPinRegister(int pin, int value) {
  volatile uint32_t *out_reg = portOutputRegister(digitalPinToPort(pin));
  uint32_t mask = digitalPinToBitMask(pin);

  if (value == HIGH) {
    *out_reg |= mask;
  } else {
    *out_reg &= ~mask;
  }
}

```


> Directly

```cpp
// Define the LED pin
const int ledPin = 2;  // D2 on NodeMCU

void setup() {
  // Set the LED pin as an output using register manipulation
  pinMode(ledPin, OUTPUT);
  
  // Start serial communication
  Serial.begin(115200);
}

void loop() {
  // Turn on the LED using register manipulation
  setPinRegister(ledPin, HIGH);

  // Print "Hello, World!" to the serial monitor
  Serial.println("Hello, World!");

  delay(1000);  // 1-second delay

  // Turn off the LED using register manipulation
  setPinRegister(ledPin, LOW);

  delay(1000);  // 1-second delay
}

// Helper function to set a pin state using register manipulation
void setPinRegister(int pin, int value) {
  if (value == HIGH) {
    GPIO_REG_WRITE(GPIO_OUT_W1TS_ADDRESS, 1 << pin);
  } else {
    GPIO_REG_WRITE(GPIO_OUT_W1TC_ADDRESS, 1 << pin);
  }
}

```

>Direct register address

```cpp
void setup() {
  // Set D2 (GPIO 4) as an output
  pinMode(4, OUTPUT);

  // No setup required
}

void loop() {
  // Turn on the LED on D2 (GPIO 4)
  *(volatile uint32_t *)0x60000308 |= (1 << 4);  // Set bit to HIGH

  // Delay for 1 second
  delayMicroseconds(1000000);

  // Turn off the LED on D2 (GPIO 4)
  *(volatile uint32_t *)0x6000030C |= (1 << 4);  // Clear bit to LOW

  // Delay for 1 second
  delayMicroseconds(1000000);
}

```