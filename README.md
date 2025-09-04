# smartdoor
This code is an Arduino code created to develop an automatic door kit that can turn existing doors into automatic doors.

#include <Servo.h>      // 서보모터 헤더파일
#include <LiquidCrystal_I2C.h>  // LCD I2C 헤더파일
#include <Wire.h>
#include <SoftwareSerial.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);
Servo myservo;    // 서보모터 변수

#define Trig 4    // 초음파- 트리거핀 4번
#define Echo 3    // 초음파- 에코핀 3번

#define red 2     // led 빨강핀(정지) 2번   // led 초록핀(지나가도됨) 1번
#define yellow 7  // led 노랑핀(열림/닫힘 중...) 0번

#define BT_TXD 8
#define BT_RXD 9
SoftwareSerial bluetooth(BT_RXD, BT_TXD);

int FSRsensor = A0;  // 압력 센서핀 A0번
int value = 0;       // 압력 값 저장할 변수
int deg;             // 서보모터 각도 변수
int del = 50;             // delay 변수 (기본값 50)

void setup()
{
   lcd.init();
   lcd.backlight();
   
   myservo.attach(13);  // 서보모터 조정을 13번 핀이 한다.
   
   pinMode(Trig, OUTPUT);
   pinMode(Echo, INPUT);
   
   pinMode(red, OUTPUT);
   pinMode(yellow, OUTPUT);
   
   lcd.setCursor(0,0);
   lcd.print("CLOSED");  // 기본상태로 문은 닫힘.
   
   digitalWrite(2, HIGH);   // 정지를 뜻하는 빨간 led만 HIGH
   digitalWrite(1, LOW);
   
   Serial.begin(9600);
   bluetooth.begin(9600);
}


void loop()
{
   //블루투스 코드
   if(bluetooth.available()){
      Serial.write(bluetooth.read());
   }
   if(Serial.available()){
      bluetooth.write(Serial.read());
   }

   //압력센서
   value = analogRead(FSRsensor);
   value = map(value, 0, 1023, 0, 255); // 압력값을 0~255로 표현한다.

   //초음파 센서
   digitalWrite(Trig, HIGH);
  delayMicroseconds(10);
  digitalWrite(Trig, LOW);
   float dist = 0, length = 0;    // 초음파 센서가 측정하는 거리
   length = pulseIn(Echo, HIGH);

   dist = ((340 * length) / 10000) / 2; 

   //시리얼 모니터
   Serial.print(dist);
   Serial.print(" CM\n");
   Serial.print(value);
   Serial.print(" pres\n");
   Serial.print(deg);
   Serial.print(" deg\n");

   if(value > 100 && dist < 10){  // 거리가 10cm 미만이고 압력값이 100을 초과하면 열림
      digitalWrite(red,LOW);
      digitalWrite(yellow, HIGH);
      lcd.setCursor(0,0);
      lcd.print("OPENING...");
      for(deg=90; deg>=0; deg--){
         myservo.write(deg);
         delay(del);
      }
      delay(5000);
   }
   else if(deg == -1 && value == 0 && dist > 50){  // 열렸을때 (각도가 -1) 압력이 0 거리가 50 초과면 닫음
      digitalWrite(yellow, HIGH);
      digitalWrite(red,LOW);
      lcd.clear();
      lcd.setCursor(0,0);
      lcd.print("CLOSING...");
      for(deg = 0; deg<=90; deg++){
         myservo.write(deg);
         delay(del);
      }
      delay(100);
   }
  else //그 외 정지상태 
   {
    digitalWrite(red, HIGH);
    digitalWrite(yellow, LOW);
    lcd.setCursor(0,0);
    lcd.print("PAUSED...");
  }
}
