#include <LiquidCrystal.h>

LiquidCrystal lcd(8, 9, 4, 5, 6, 7);

const int IN1 = A5;
const int IN2 = A2;
const int IN3 = 12;
const int IN4 = 13;
float  RPM_RAW;
unsigned long lastPulseTime = 0;
int lastEncoderState = LOW;
unsigned long pulseCount = 0;
float totalDistance = 0;
const int PPR = 20;
const float WHEEL_CIRCUMFERENCE = 0.065; 
int duration;
// PWM pins
const int ENA = 3;
const int ENB = 11;
  int stopped = 0;


const int IR1 = A3;   // Left sensor
const int IR2 = A4;   // Right sensor
const int IR3 = A1;   // Middle sensor

const int SLOW = 0;
const int FAST = 255;
const int WALK = 100;
int distance;

const int Encoder = 2;

volatile unsigned long lastMicros = 0;
volatile unsigned long T;

int r = 0;
int l = 0;
int RPM;

unsigned long timetaken = 0;
bool onstraight = false;

// =========================
// ORIGINAL MOTOR LOGIC
// =========================

void right() {
   
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);

  analogWrite(ENA, FAST);
  analogWrite(ENB, FAST);

  r = 1;
  l = 0;
}

void left() {
  
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);

  analogWrite(ENA, FAST);
  analogWrite(ENB, FAST);

  l = 1;
  r = 0;
}

void forward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);

  analogWrite(ENA, WALK);
  analogWrite(ENB, WALK);
}

// =========================
// ORIGINAL HARD TURN
// =========================
void hardturn() {
  if (l == 1) {
    digitalWrite(IN1, LOW);
    digitalWrite(IN2, HIGH);
    digitalWrite(IN3, HIGH);
    digitalWrite(IN4, LOW);

    analogWrite(ENA, FAST);
    analogWrite(ENB, FAST);

    while (digitalRead(IR1) == 1 && digitalRead(IR2) == 1) {}
  }
  else if (r == 1) {
    digitalWrite(IN1, HIGH);
    digitalWrite(IN2, LOW);
    digitalWrite(IN3, LOW);
    digitalWrite(IN4, HIGH);

    analogWrite(ENA, FAST);
    analogWrite(ENB, FAST);

    while (digitalRead(IR1) == 1 && digitalRead(IR2) == 1) {}
  }
}


// =========================
// RESTORED LINE FOLLOWING
// =========================
void drive() {

  int leftIR = digitalRead(IR1);   // 0 = white, 1 = black
  int rightIR = digitalRead(IR2);
  // BOTH BLACK → forward
  if (leftIR == 1 && rightIR == 1) {

    forward();
    if (!onstraight) { timetaken = millis(); onstraight = true; } if (millis() - timetaken >= 250) { hardturn(); onstraight = false; }
  

  
  }

  // LEFT BLACK → turn right
  else if (leftIR == 0 && rightIR == 1){ 
    right();
    onstraight = false;
    r=1;
    l=0;
   

  }

  // RIGHT BLACK → turn left
  else if (leftIR == 1 && rightIR == 0) {
    left();
    onstraight = false;
    l=1;
    r=0;
  }

  // BOTH WHITE → stop
  else if (leftIR == 0 && rightIR == 0 ) {
    analogWrite(ENA, 0);
    analogWrite(ENB, 0);
    onstraight = false;
  } 
  
  
}


// =========================
// ENCODER ISR (unchanged)
// =========================
void RPMdetect() {
  unsigned long now = micros();
  unsigned long dt = now - lastMicros;

  if (dt > 800) { 
    T = dt;
    lastMicros = now;
    lastPulseTime = micros();
  }
}

// =========================
// DISTANCE UPDATE (unchanged)
// =========================


// =========================
// SETUP & LOOP
// =========================
void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENA, OUTPUT);
  pinMode(ENB, OUTPUT);
  

  pinMode(IR3, INPUT);
  pinMode(IR1, INPUT);
  pinMode(IR2, INPUT);
  pinMode(Encoder, INPUT);

  lcd.begin(16, 2);
  attachInterrupt(digitalPinToInterrupt(Encoder), RPMdetect, RISING);
  Serial.begin(9600);
}

void loop() {
    int currentState = digitalRead(Encoder);
  // Detect rising edge (LOW to HIGH transition)
  if (lastEncoderState == LOW && currentState == HIGH) {
    pulseCount++;
  }
  lastEncoderState = currentState;
  
  // Update distance every 100ms
  if (millis() - lastPulseTime >= 100) {
    float revolutions = (float)pulseCount / PPR;
    float distanceThisInterval = revolutions * WHEEL_CIRCUMFERENCE;  
    int Distancefinal;
    totalDistance += distanceThisInterval;
    Distancefinal = totalDistance*4.5;
    pulseCount = 0;
    lastPulseTime = millis();
    // Update display
    lcd.setCursor(0, 0);
    lcd.print("Dist: ");
    lcd.print(Distancefinal, 3);
    lcd.print(" m  ");
    lcd.setCursor(0,1);
    lcd.print("Time: ");
    lcd.print(millis()/1000);
    while(!Serial);
    Serial.print("totalDistance: ");
    Serial.println(Distancefinal);
     if(Distancefinal>=4.100&&stopped ==0){
      analogWrite(ENA, 0);
      analogWrite(ENB, 0);
      delay(2000);
      stopped =1;
      drive();
      
    }else{
      drive();
    }

  }
    
  
   

}
