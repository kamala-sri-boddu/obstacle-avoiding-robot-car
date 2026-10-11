# Obstacle-avoiding-robot-car
#include <Servo.h>

//PIN DEFINITIONS
#define ENA 5
#define IN1 6
#define IN2 7
#define ENB 9
#define IN3 10
#define IN4 11

#define TRIG 2
#define ECHO 3


#define IR_LEFT A0
#define IR_RIGHT A1

// GLOBAL VARIABLES
Servo scanServo;
long duration;
int frontDist, leftDist, rightDist;

// MOTOR FUNCTIONS
void moveForward() {
  analogWrite(ENA, 150);  // left motor PWM
  analogWrite(ENB, 165);  // right motor PWM (slightly faster)
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
}

void moveBackward() {
  analogWrite(ENA, 140);
  analogWrite(ENB, 140);
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}

void stopMotors() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
}

void turnLeft() {
  analogWrite(ENA, 0);    // stop left wheel
  analogWrite(ENB, 120);  // right wheel forward
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  delay(600);
}

void turnRight() {
  analogWrite(ENA, 120);  // left wheel forward
  analogWrite(ENB, 0);    // stop right wheel
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  delay(600);
}

// ULTRASONIC FUNCTION
int getDistance() {
  digitalWrite(TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG, LOW);

  duration = pulseIn(ECHO, HIGH, 30000); // 30 ms timeout
  if (duration == 0) return 100; // no object detected
  return duration * 0.034 / 2;
}

// SETUP
void setup() {
  // Motor pins
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);

  // Ultrasonic pins
  pinMode(TRIG, OUTPUT);
  pinMode(ECHO, INPUT);

  // IR sensors
  pinMode(IR_LEFT, INPUT);
  pinMode(IR_RIGHT, INPUT);
  scanServo.attach(4); // use the pin connected to servo
  scanServo.write(90);

  Serial.begin(9600);
}

// MAIN LOOP
void loop() {

  // Read IR sensors for black line detection
  int leftIR = digitalRead(IR_LEFT);
  int rightIR = digitalRead(IR_RIGHT);

  if (leftIR == HIGH && rightIR == HIGH) { // black line detected
    stopMotors();
    Serial.println("Black line detected. Stopped.");
    while (1); // stop permanently
  }

  // Read front distance
  frontDist = getDistance();
  Serial.print("Front distance: ");
  Serial.println(frontDist);

  if (frontDist < 18) { // obstacle detected
    stopMotors();
    delay(200);

    // Move backward to create space
    moveBackward();
    delay(300);
    stopMotors();
    delay(150);

    // Scan left
    scanServo.write(150);
    delay(350);
    leftDist = getDistance();

    // Scan right
    scanServo.write(30);
    delay(350);
    rightDist = getDistance();

    // Center servo
    scanServo.write(90);
    delay(200);

    Serial.print("Left: "); Serial.print(leftDist);
    Serial.print(" Right: "); Serial.println(rightDist);

    // Turn to the side with more space
    if (leftDist > rightDist) {
      turnLeft();
    } else {
      turnRight();
    }
  }
  else {
    moveForward();
  }
}
