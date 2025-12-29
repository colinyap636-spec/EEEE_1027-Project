#include <LiquidCrystal.h>
#include <Adafruit_MPU6050.h>
#include <Adafruit_Sensor.h>
#include <Wire.h>

LiquidCrystal lcd(8, 9, 4, 5, 6, 7);

// Motor pins
const int IN1 = 2;
const int IN2 = A2;
const int IN3 = 12;
const int IN4 = 13;
const int ENA = 3;
const int ENB = 11;

// Climbing variables
int climb = 0;  // 0: not climbing, 1: climbing, 2: at top

// Rotation variables
float totalRotation = 0;  // Total rotation in degrees (cumulative)
unsigned long lastTime = 0; // Last time a reading was taken

Adafruit_MPU6050 mpu;

// Motor control functions
void right() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
}

void left() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
}

void forward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
}

void stopMotors() {
  analogWrite(ENA, 0);
  analogWrite(ENB, 0);
}

// Setup
void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENA, OUTPUT);
  pinMode(ENB, OUTPUT);

  lcd.begin(16, 2);
  lcd.setCursor(0, 0);
  lcd.print("Motor Test");
  delay(1000);
  lcd.clear();

  Serial.begin(115200);
  while (!Serial)
    delay(10);  // will pause Zero, Leonardo, etc until serial console opens

  Serial.println("Adafruit MPU6050 test!");

  if (!mpu.begin()) {
    Serial.println("Failed to find MPU6050 chip");
    while (1) {
      delay(10);
    }
  }
  Serial.println("MPU6050 Found!");

  mpu.setAccelerometerRange(MPU6050_RANGE_8_G);
  mpu.setGyroRange(MPU6050_RANGE_500_DEG);
  mpu.setFilterBandwidth(MPU6050_BAND_21_HZ);

  lastTime = millis();  // Initialize lastTime
  delay(100);
}

// Main loop
void loop() {
  sensors_event_t a, g, temp;
  mpu.getEvent(&a, &g, &temp);

  // Get current time and calculate the time difference (in seconds)
  unsigned long currentTime = millis();
  float deltaTime = (currentTime - lastTime) / 1000.0;  // Time in seconds

  // Calculate the change in rotation from the gyroscope Z-axis
  float deltaRotation = g.gyro.z * deltaTime;  // Gyro is in degrees per second
  totalRotation += deltaRotation;
  lastTime = currentTime;

  // Debugging: Output accelerometer and gyroscope readings to Serial Monitor
  Serial.print("Accel X: "); Serial.print(a.acceleration.x);
  Serial.print(" | Accel Y: "); Serial.print(a.acceleration.y);
  Serial.print(" | Accel Z: "); Serial.println(a.acceleration.z);
  Serial.print("Gyro Z: "); Serial.println(g.gyro.z);

  // Detect climbing using accelerometer (a.acceleration.x)
  if (-a.acceleration.x > 5.0 && climb == 0) { // Robot starts climbing (acceleration on the ramp)
    climb = 1;  // Robot has started climbing
    forward();   // Keep moving forward
    Serial.println("Climbing started...");
    delay(200);
  }else{forward();
  delay(200);}

  // Detect when the robot has reached the top of the ramp (flat surface)
  if (-a.acceleration.x < 2 && climb == 1) {  // Acceleration is low when it's flat again
    climb = 2;  // At the top of the ramp
    stopMotors();  // Stop the robot at the top
    Serial.println("At top of ramp, waiting 2 seconds...");
    lcd.setCursor(0, 0);
    lcd.print("At top, waiting...");
    delay(4000);  // Wait for 2 seconds
  }

  // After climbing and stopping, start 360-degree turn
  if (climb == 2) {
    turn360();
  }

  // Update LCD with current rotation
  lcd.setCursor(0, 1);
  lcd.print(-a.acceleration.x*(180/3.14)/10);

  delay(50);  // Short delay for readability and responsiveness
}

// Perform 360-degree turn
void turn360() {
  // Reset rotation tracking
  totalRotation = 0;

  // Rotate while the currentRotation is less than 360 degrees
  while (totalRotation < 6) {
    right();  // Turn right continuously
    sensors_event_t a, g, temp;
    mpu.getEvent(&a, &g, &temp);

    // Calculate the time elapsed since the last update
    unsigned long currentTime = millis();
    float deltaTime = (currentTime - lastTime) / 1000.0;  // Time in seconds

    // Calculate the change in rotation (degrees) from the gyroscope Z-axis
    float deltaRotation = g.gyro.z * deltaTime;  // Gyroscope is in degrees per second

    // Update the current rotation
    totalRotation += deltaRotation;

    // Update lastTime for the next iteration
    lastTime = currentTime;

    // Update the LCD with the current rotation status
    lcd.setCursor(0, 0);
    lcd.print("Rotation: ");
    lcd.print(totalRotation*(180/3.14));
    lcd.print(" deg");

    delay(50);  // Small delay to avoid flooding the serial and LCD updates
  }

  // Once 360 degrees is reached, stop the motors
  stopMotors();
  Serial.println("360 degree rotation detected!");
  while(1){};  // Pause for a moment after completing the turn
}
