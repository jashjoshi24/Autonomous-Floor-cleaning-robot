#include <Servo.h>

// Pin definitions
#define trigPin A1        // Trig pin of the Ultrasonic Sensor
#define echoPin A2        // Echo pin of the Ultrasonic Sensor
#define servoPin 10      // Servo motor pin
#define motor1A 2        // Motor 1 input A
#define motor1B 3        // Motor 1 input B
#define motor2A 4        // Motor 2 input A
#define motor2B 5        // Motor 2 input B

Servo myServo;

void setup() {
    Serial.begin(9600);
    
    // Initialize ultrasonic sensor pins
    pinMode(trigPin, OUTPUT);
    pinMode(echoPin, INPUT);
    
    // Initialize motor pins
    pinMode(motor1A, OUTPUT);
    pinMode(motor1B, OUTPUT);
    pinMode(motor2A, OUTPUT);
    pinMode(motor2B, OUTPUT);
    
    // Attach the servo
    myServo.attach(servoPin);
}

void loop() {
    int distance = getDistance();  // Measure distance
    
    if (distance <= 15) {  // If obstacle is closer than 15 cm
        stopMotors();
        delay(500);  // Short pause
        avoidObstacle();  // Call obstacle avoidance function
    } else {
        moveForward();
    }
}

// Function to measure distance with ultrasonic sensor
int getDistance() {
    digitalWrite(trigPin, LOW);
    delayMicroseconds(2);
    digitalWrite(trigPin, HIGH);
    delayMicroseconds(10);
    digitalWrite(trigPin, LOW);

    long duration = pulseIn(echoPin, HIGH);
    int distance = duration * 0.034 / 2;  // Convert to cm
    return distance;
}

// Function to move forward
void moveForward() {
    digitalWrite(motor1A, HIGH);  // Left motor forward
    digitalWrite(motor1B, LOW);
    digitalWrite(motor2A, HIGH);  // Right motor forward
    digitalWrite(motor2B, LOW);
}

// Function to stop motors
void stopMotors() {
    digitalWrite(motor1A, LOW);
    digitalWrite(motor1B, LOW);
    digitalWrite(motor2A, LOW);
    digitalWrite(motor2B, LOW);
}

// Function to turn right
void turnRight() {
    digitalWrite(motor1A, LOW);  // Left motor stops
    digitalWrite(motor1B, LOW);
    digitalWrite(motor2A, HIGH);  // Right motor forward
    digitalWrite(motor2B, LOW);
}

// Function to turn left
void turnLeft() {
    digitalWrite(motor1A, HIGH);  // Left motor forward
    digitalWrite(motor1B, LOW);
    digitalWrite(motor2A, LOW);  // Right motor stops
    digitalWrite(motor2B, LOW);
}

// Function to avoid obstacle
void avoidObstacle() {
    myServo.write(0);       // Turn servo to the left
    delay(500);
    int leftDistance = getDistance();
    delay(500);

    myServo.write(180);     // Turn servo to the right
    delay(500);
    int rightDistance = getDistance();
    delay(500);

    myServo.write(90);      // Center the servo
    delay(500);

    if (leftDistance > rightDistance) {
        turnLeft();
    } else {
        turnRight();
    }
    delay(1000);
    stopMotors();
}



