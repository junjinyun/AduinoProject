# AduinoProject
아두이노 프로젝트 업로드 (기한 6/12 제출)
> 주제 : 블루투스를 이용한 모바일 아두이노 간 시리얼 통신을 통한 원격 조종
> 기능 설명
#include <SoftwareSerial.h>

SoftwareSerial btSerial(10, 11); // 블루투스를 이용한 데이터 송, 수신

const int ledPin = 3;             // PWM - 조명 밝기 제어
const int motorPin = 5;           // PWM - 선풍기 속도 제어
const int submotorPin = 6;        // PWM - 자동문 개폐 장치
const int buzzerPin = 9;          // PWM - 경보기

// 센서 전원 공급 핀
const int sensorPowerPin = A4;    // 센서에 전원 공급

// 센서 제어 핀 (디지털 핀)
const int lightSensorControlPin = 4;  // 조도 센서 제어
const int tempSensorControlPin = 7;   // 온습도 센서 제어
const int flameSensorControlPin = 8;  // 화염 센서 제어

// 센서 입력 핀 (아날로그 입력)
const int flameSensorInputPin = A1;
const int tempSensorInputPin = A2;
const int lightSensorInputPin = A3;


센서
조도 : 현제 조도를 측정하여 일정 수치 아래 일 경우 조명 자동 활성화
온습도 : 현제 온도 측정하여 일정수치 아래 일 경우 선풍기 작동
화염 : 화염 감지 시에 경보기 작동

모듈
led = 조명(1,2,3,4,5 로 입력받아 값으로 매핑해 밝기를 구현)
모터 = 선풍기(1,2,3,4,5 로 입력받아 단계를 실제 값으로 매핑)
서브모터 = 자동 문 개폐장치
부저 = 경보기

