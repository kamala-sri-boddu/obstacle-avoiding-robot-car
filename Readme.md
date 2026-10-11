Obstacle-Avoiding Autonomous Robot Car 

An Arduino-based robot car that drives autonomously, detects obstacles using an ultrasonic sensor, scans left/right with a servo to find the clearer path, and stops automatically when it detects a black line (line-stop safety feature) using IR sensors.

[Robot car build](./media/robot_car_build_photo.jpg)
[Robot car in action](./media/robot_car_video_frame.jpg)

(Pictured: the assembled chassis with ultrasonic + servo mount, L298N motor driver, Arduino Uno, IR line sensors, and battery pack.)

 How It Works

1. Driving — The car moves forward continuously using a dual-motor L298N-style driver (PWM speed control on `ENA`/`ENB`, direction control on `IN1`–`IN4`).
2. Obstacle Detection** — An HC-SR04 ultrasonic sensor (`TRIG`/`ECHO`) continuously measures the distance to objects in front of the car.
3. Avoidance Logic** — When an obstacle is detected within 18 cm:
   - The car stops and reverses slightly to create space.
   - A servo motor sweeps the ultrasonic sensor left (150°) and right (30°) to measure open space on each side.
   - The car turns toward whichever side has more clearance.
4. Line-Stop Safety** — Two IR sensors (`IR_LEFT`, `IR_RIGHT`) detect a black line on the ground; if both trigger, the car stops permanently (useful as a boundary/finish-line safety cutoff).

 Hardware Used

| Component | Purpose |
|---|---|
| Arduino Uno/Nano | Main controller |
| L298N Motor Driver | Dual DC motor control |
| 2x DC Geared Motors | Drive wheels |
| HC-SR04 Ultrasonic Sensor | Front obstacle distance measurement |
| SG90 Servo Motor | Sweeps the ultrasonic sensor left/right |
| 2x IR Sensors | Black-line / boundary detection |

Pin Configuration

Motor A: ENA=5, IN1=6, IN2=7
Motor B: ENB=9, IN3=10, IN4=11
Ultrasonic: TRIG=2, ECHO=3
Servo: Pin 4
IR Sensors: IR_LEFT=A0, IR_RIGHT=A1

Skills Demonstrated

- Embedded C/C++ programming (Arduino)
- Real-time sensor integration (ultrasonic + IR + servo)
- PWM-based motor speed control
- Autonomous decision-making logic (obstacle avoidance algorithm)
- Hardware-software debugging via Serial Monitor

File

- [`obstacle_avoiding_robot_car.ino`](./obstacle_avoiding_robot_car.ino) — full Arduino sketch


Project by Boddu Kamala Sri — [LinkedIn](https://linkedin.com/in/kamala-sri-boddu-57507a20b)

