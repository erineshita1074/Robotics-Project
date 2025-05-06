// Pin Definitions
const int ENA = 5;
const int IN1 = 6;
const int IN2 = 7;
const int IN3 = 8;
const int IN4 = 9;
const int ENB = 10;
const int FlameSensorPin = A0;
const int SmokeSensorPin = A5;
const int waterPump = A4;
const int ultrasonicTrigPin = 12;
const int ultrasonicEchoPin = 11;
const int buzzerPin = 3;
const int ledPin = 2;

// Variables for Ultrasonic Sensor
long duration;
int distance;

// Variables for Smoke and Flame Detection
int flameState = 0;
int smokeState = 0;

void setup() {
  // Initialize Serial Monitor
  Serial.begin(9600);

  // Set motor pins as outputs
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENB, OUTPUT);

  // Set sensor pins
  pinMode(FlameSensorPin, INPUT);
  pinMode(SmokeSensorPin, INPUT);
  pinMode(waterPump, OUTPUT);
  pinMode(ultrasonicTrigPin, OUTPUT);
  pinMode(ultrasonicEchoPin, INPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledPin, OUTPUT);
}

void loop() {
  // Read Flame and Smoke sensor states
  flameState = digitalRead(FlameSensorPin);
  smokeState = digitalRead(SmokeSensorPin);

  // Check for Fire (Flame detected)
  if (flameState == LOW) {
    Serial.println("Fire Detected, Car Motor Running Ahead");
    digitalWrite(ledPin, HIGH); // LED Blinks

    // Measure Distance with Ultrasonic Sensor
    digitalWrite(ultrasonicTrigPin, LOW);
    delayMicroseconds(2);
    digitalWrite(ultrasonicTrigPin, HIGH);
    delayMicroseconds(10);
    digitalWrite(ultrasonicTrigPin, LOW);

    duration = pulseIn(ultrasonicEchoPin, HIGH);
    distance = duration * 0.034 / 2;

    // Print distance to Serial Monitor
    Serial.print("Distance: ");
    Serial.println(distance);

    // Move the car towards the fire
    if (distance > 2) {
      moveForward();
    } else {
      stopCar();
      digitalWrite(waterPump, HIGH); // Start the water pump
      Serial.println("Water Pump Running");

      // Wait for fire to be extinguished (simulated for now)
      delay(5000); // Wait for 5 seconds (fire extinguished)
      Serial.println("No Fire, Water Pump Stop");

      // Wait 5 seconds after extinguishing the fire
      delay(5000);
      
      // Move car back 15cm
      moveBack();
      stopCar();
      Serial.println("Car Motor Go Back");
    }

  } else {
    // No Fire Detected
    Serial.println("No Fire Detected");
    digitalWrite(ledPin, LOW); // LED Off
  }

  // Check for Smoke
  if (smokeState == HIGH) {
    digitalWrite(buzzerPin, HIGH); // Turn on Buzzer
    Serial.println("Smoke Detected");
  } else {
    digitalWrite(buzzerPin, LOW); // Turn off Buzzer
    Serial.println("No Smoke Detected");
  }

  // Small delay before repeating the loop
  delay(500);
}

void moveForward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
}

void moveBack() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}

void stopCar() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
}
