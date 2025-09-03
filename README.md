# smartdoor
This code is an Arduino code created to develop an automatic door kit that can turn existing doors into automatic doors.
#include <Servo.h>    // 서보모터 헤더파일
#include <LiquidCrystal_I2C.h>  // LCD I2C 헤더파일
#include <Wire.h>
#include <DHT.h>
#include <SoftwareSerial.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);
DHT dht(DTHPIN, DTHTYPE);
Servo myservo;    // 서보모터 변수

#define Trig 4    // 초음파- 트리거핀 4번
#define Echo 3    // 초음파- 에코핀 3번

#define red 2     // led 빨강핀(정지) 2번
#define green 1   // led 초록핀(지나가도됨) 1번
#define yellow 0  // led 노랑핀(열림/닫힘 중...) 0번

#define DTHPIN A2
#define DTHTYPE DHT00    // (모델 버전 확인해야함)

// HC-06 블루투스 모듈 핀 (RX, TX)
SoftwareSerial BT(2, 3); // RX=2, TX=3

int FSRsensor = A0;  // 압력 센서핀 A0번
int value = 0;       // 압력 값 저장할 변수
int deg;             // 서보모터 각도 변수
int del = 50;             // delay 변수 (기본값 50)

void setup()
{
   lcd.init();
   lcd.backlight();
   dht.begin();
   
   myservo.attach(13);  // 서보모터 조정을 13번 핀이 한다.
   
   pinMode(Trig, OUTPUT);
   pinMode(Echo, INPUT);
   
   pinMode(red, OUTPUT);
   pinMode(green, OUTPUT);
   pinMode(yellow, OUTPUT);
   
   lcd.setCursor(0,0);
   lcd.print("CLOSED");  // 기본상태로 문은 닫힘.
   
   digitalWrite(red, HIGH);   // 정지를 뜻하는 빨간 led만 HIGH
   digitalWrite(green, LOW);
   digitalWrite(yellow, LOW);
   
   Serial.begin(9600);
   BT.begin(9600);       // 블루투스 통신
  myServo.attach(9);    // SG90 서보모터 핀
  myServo.write(0);     // 초기화
  Serial.println("Ready. Input a,b,c,z,o via BT or Serial");
}

void turn_motor(int angleChar){
  myServo.write(angleChar);
  delay(500); // 안정적인 이동 시간
}

void loop()
{
   //압력센서
   value = analogRead(FSRsensor));
   Serial.println(value);
   value = ma장
      angleChar = 180;
      turn_motor(angleChar);
      Serial.println("Saved Angle: 180");
    }
    else if (cmd == 'z') { //z가 입력되었을 때 모터 각도를 0도로 이동
      turn_motor(0);
      Serial.println("Moved to 0 (Door Closed)");
    }
    else if (cmd == 'o') { //o가 입력되었을 때 저장된 각도로 모터가 이동
      turn_motor(angleChar);
      Serial.print("Moved to Saved Angle: ");
      Serial.println(angleChar);
    }
  }
   }
