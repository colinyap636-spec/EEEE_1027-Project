// ------------------ Motors ------------------
const int IN1 = 2;
const int IN2 = A2;
const int IN3 = A5;
const int IN4 = A4;
const int ENA = 3;
const int ENB = 11;
const int trigPin = 13;
const int echoPin = 12;

float duration_pulse, distance_cm;

void forward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 150);
  analogWrite(ENB, 150);
}

void right() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
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
  
  //ultrasonic sensor pins
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Serial.begin(9600);
}

void loop() {
    // generate 10-microsecond pulse to TRIG pin
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // measure duration of pulse from ECHO pin
  duration_pulse = pulseIn(echoPin, HIGH);

  // calculate the distance
  distance_cm = 0.017 * duration_pulse;
  //reroutes if objhect is detected, or else go forward
  if (distance_cm <= 30.00){
    right(); 
  }
  else{
    forward();
  }
}
