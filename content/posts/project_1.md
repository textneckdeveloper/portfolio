---
title: 'Arduino : Created a Radio Controlled Car'
date: 2022-12-05
description: Setup table of content in Hugo blog awesome theme
---

![rc-car](https://textneckdeveloper.github.io/portfolio/images/car.jpg)

캡스톤 디자인 사물 인터넷 프로젝트로 아두이노를 이용한 무선 조종 자동차를 제작했습니다.

* 개발 기간 : 2022.11.25 ~ 2022.12.05

* 개발 인원 : 4명

* 언어 : C++

* 주요 기능

    1. 블루투스를 이용한 원격 제어가 가능합니다.
    2. 모터 제어 : 두 개의 DC 모터를 사용해 자동차의 움직임을 제어합니다.
    3. 장애물 감지 및 회피 : 초음파 센서를 통해 장애물 까지의 거다.
    4. 회전 각도 조절 : 서보 모터가 좌우로 회전하므로 여러 각도로 장애물
    5. 장애물이 감지되면 자동차가 후진하고, 서보 모터의 각도에 따라 방향

* 재료

    1. L293D Motor Shield
    2. Chassis
    3. DC Motor 2개
    4. Servo Motor 1개  
    5. 초음파 센서 1개
    6. 외부 전원 2개
    7. 바퀴 4개
    8. 아두이노 우노

## Arduino Code

```cpp
#include <AFMotor.h>
#include <Servo.h> 
#include <NewPing.h>

AF_DCMotor motor1(3);
AF_DCMotor motor2(4);

Servo servo;
const int servoPin = 10;
const int TRIG = A4;
const int ECHO = A5;
const int MAX_DISTANCE = 100;
NewPing sonar(TRIG, ECHO, MAX_DISTANCE);

int speed = 200;
int state = 10;
int angle = 90;

void setup() {
  motor1.setSpeed(speed);
  motor2.setSpeed(speed);
  motor2.run(RELEASE);
  motor2.run(RELEASE);

  servo.attach(servoPin);
  servo.write(angle);
  delay(1000);

  // Start
  motor1.run(FORWARD);
  motor2.run(FORWARD);  
}

void loop() {
  // 초음파 센서 회전
  servo.write(angle);
  delay(50);
  
  int distance = sonar.ping_cm();

  // 장애물 감지
  if (distance > 0 && distance < 15) {
    movePattern(); 
    motor1.run(FORWARD);
    motor2.run(FORWARD);  
  }   

  // 회전 각도
  if (angle == 140) state = -10;    
  else if (angle == 40) state = 10; 
  angle += state;
}

void movePattern() {
  motor1.run(BACKWARD);
  motor2.run(BACKWARD);
  delay(500);   

  if (angle >= 90) {
    motor1.run(FORWARD);
    motor2.run(BACKWARD);
    delay(500);   
  } else {
    motor1.run(BACKWARD);
    motor2.run(FORWARD);
    delay(500);   
  }
  
  angle = 90;
  servo.write(angle);
  delay(100);
}
```