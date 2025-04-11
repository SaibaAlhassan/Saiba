# Saiba

const int LDR_PIN = A0;  // LDR connected to analog pin A0
const int LED_PIN = 9;  // LED connected to digital pin 9 (PWM pin)
const int TRIG_PIN = 12; // Trigger pin for ultrasonic sensor
const int ECHO_PIN = 13; // Echo pin for ultrasonic sensor

long duration;
int distance;
int ldrValue;
int ledBrightness;
const int ldrThreshold = 600;  // Adjust based on your environment
const int maxBrightness = 254; // Maximum LED brightness

void setup() {
  Serial.begin(9600);  // Initialize serial communication
  pinMode(LED_PIN, OUTPUT);  // Set LED pin as output
  pinMode(TRIG_PIN, OUTPUT); // Set trigger pin as output
  pinMode(ECHO_PIN, INPUT);  // Set echo pin as input
}

void loop() {
  // Read the value from the LDR (0 to 1023)
  ldrValue = analogRead(LDR_PIN);
  
  Serial.print("LDR Value: ");
  Serial.println(ldrValue);
  
  // If daytime (LDR detects high brightness), turn off LED completely
  if (ldrValue > ldrThreshold) {
    analogWrite(LED_PIN, 0);
    Serial.println("Daytime detected, LED turned OFF");
  } 
  else {  // Nighttime scenario
    Serial.println("Nighttime detected, adjusting LED brightness...");
    
    // Trigger the ultrasonic sensor
    digitalWrite(TRIG_PIN, LOW);
    delayMicroseconds(2);
    digitalWrite(TRIG_PIN, HIGH);
    delayMicroseconds(10);
    digitalWrite(TRIG_PIN, LOW);
  
    // Measure distance
    duration = pulseIn(ECHO_PIN, HIGH);
    distance = duration * 0.034 / 2;
  
    Serial.print("Distance: ");
    Serial.println(distance);
    
    // Adjust LED brightness based on distance (with a minimum brightness of 20)
    if (distance <= 100) {  // If vehicle is within 100 cm
      ledBrightness = map(distance, 100, 0, maxBrightness, 20);  // Map distance to brightness
    } else {
      ledBrightness = maxBrightness;  // If further than 100cm, LED at max brightness
    }
    
    // Apply the brightness to LED
    analogWrite(LED_PIN, ledBrightness);
    
    // Print LED brightness for feedback
    Serial.print("LED Brightness: ");
    Serial.println(ledBrightness);
  }

  delay(500);  // Small delay to prevent rapid changes and allow time for measurements
}
