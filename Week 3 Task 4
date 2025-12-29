#include <LiquidCrystal.h>
#include <SoftwareSerial.h>

// ------------------ LCD ------------------
LiquidCrystal lcd(8, 9, 4, 5, 6, 7);

// ------------------ Motors ------------------
const int IN1 = 2;
const int IN2 = A2;
const int IN3 = A5;
const int IN4 = A4;
const int ENA = 3;
const int ENB = 11;

// ------------------ Bluetooth ------------------
// SoftwareSerial on pins 10 (RX) and 11 (TX)
SoftwareSerial BT(12, 13); // RX, TX
char BTdata = 0;

// ------------------ Motor Functions ------------------
void forward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 150);
  analogWrite(ENB, 150);
}

void backward() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  analogWrite(ENA, 150);
  analogWrite(ENB, 150);
}

void left() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
}

void right() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
}

void stopCar() {
  analogWrite(ENA, 0);
  analogWrite(ENB, 0);
}

// ------------------ Setup ------------------
void setup() {
  // Motor pins
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENA, OUTPUT);
  pinMode(ENB, OUTPUT);

  // LCD
  lcd.begin(16, 2);
  lcd.setCursor(0, 0);
  lcd.print("Bluetooth Ready");

  // Initialize SoftwareSerial for HC-05
  BT.begin(9600); // Default baud rate for HC-05
}

// ------------------ Loop ------------------
void loop() {

  // Check if Bluetooth sent anything
  if (BT.available()) {
    BTdata = BT.read();

    // Display on LCD
    lcd.setCursor(0, 1);
    lcd.print("Received: ");
    lcd.print(BTdata);
    lcd.print("  "); // Clear leftover chars

    // Motor control
    if (BTdata == 'F') forward();
    else if (BTdata == 'B') backward();
    else if (BTdata == 'L') left();
    else if (BTdata == 'R') right();
    else if (BTdata == '0') stopCar();
  }
}
