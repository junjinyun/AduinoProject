# AduinoProject
아두이노 프로젝트 업로드 (기한 6/12 제출)
> 주제 : 블루투스를 이용한 모바일 아두이노 간 시리얼 통신을 통한 원격 조종
> 코드
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

/*
led = 조명(1,2,3,4,5 로 입력받아 값으로 매핑해 밝기를 구현)
모터 = 선풍기(1,2,3,4,5 로 입력받아 단계를 실제 값으로 매핑)
서브모터 = 자동 문 개폐장치
부저 = 경보기
-----
센서
조도 : 현제 조도를 측정하여 일정 수치 아래 일 경우 조명 자동 활성화
온습도 : 현제 온도 측정하여 일정수치 아래 일 경우 선풍기 작동
화염 : 화염 감지 시에 경보기 작동
*/
void setup() {
  Serial.begin(9600);
  btSerial.begin(9600);

  pinMode(ledPin, OUTPUT);
  pinMode(motorPin, OUTPUT);

  pinMode(senserControl1Pin, OUTPUT);
  pinMode(senserControl2Pin, OUTPUT);
  digitalWrite(senserControl1Pin, LOW);
  digitalWrite(senserControl2Pin, LOW);

  Serial.println("시리얼 모니터 또는 블루투스로 명령어를 입력할 수 있습니다.");
  delay(5000);
  Serial.println("준비 완료: 대상, 행동, 추가 지정사항의 형식으로 입력하세요.");
}

void loop() {
  String input = "";

  if (btSerial.available()) {
    input = btSerial.readStringUntil('\n');
  }
  else if (Serial.available()) {
    input = Serial.readStringUntil('\n');
  }
  else {
    return;
  }

  input.trim();
  if (input.length() == 0) return;

  Serial.println("입력된 명령: " + input);

  String target = "";
  String action = "";
  String param = "";

  int firstComma = input.indexOf(',');
  int secondComma = input.indexOf(',', firstComma + 1);

  if (firstComma == -1) {
    Serial.println("잘못된 형식: 최소한 '대상, 행동'이 필요합니다.");
    return;
  }

  target = input.substring(0, firstComma);
  if (secondComma == -1) {
    action = input.substring(firstComma + 1);
    param = "";  // param 생략 가능
  } else {
    action = input.substring(firstComma + 1, secondComma);
    param = input.substring(secondComma + 1);
  }

  target.trim();
  action.trim();
  param.trim();

  target.toLowerCase();
  action.toLowerCase();
  param.toLowerCase();

  handleCommand(target, action, param);

  Serial.println("실행된 명령: " + target + ", " + action + ", " + param);
}

void handleCommand(String target, String action, String param) {
  if (target == "led") {
    if (action == "켜기") {
      digitalWrite(ledPin, HIGH);
      Serial.println("digitalWrite(ledPin, HIGH);");
      Serial.println("LED ON");
    } else if (action == "끄기") {
      digitalWrite(ledPin, LOW);
      Serial.println("digitalWrite(ledPin, LOW);");
      Serial.println("LED OFF");
    } else {
      Serial.println("알 수 없는 LED 동작: " + action);
    }
  }
  else if (target == "모터") {
    if (action == "설정") {
      if (param != "") {
        int pwmValue = param.toInt();
        pwmValue = constrain(pwmValue, 0, 255);
        analogWrite(motorPin, pwmValue);
        Serial.print("analogWrite(motorPin, ");
        Serial.print(pwmValue);
        Serial.println(");");
        Serial.println("모터 속도 설정: " + String(pwmValue));
      } else {
        Serial.println("모터 설정값이 필요합니다.");
      }
    }
    else if (action == "정지") {
      analogWrite(motorPin, 0);
      Serial.println("analogWrite(motorPin, 0);");
      Serial.println("모터 정지");
    }
    else {
      Serial.println("알 수 없는 모터 동작: " + action);
    }
  }
  else if (target == "센서") {
    if (param != "조도" && param != "온습도") {
      Serial.println("알 수 없는 센서 종류: " + param);
      return;
    }

    if (action == "on") {
      if (param == "조도") {
        digitalWrite(senserControl1Pin, HIGH);
        Serial.println("digitalWrite(senserControl1Pin, HIGH);");
        Serial.println("조도 센서 ON");
      } else if (param == "온습도") {
        digitalWrite(senserControl2Pin, HIGH);
        Serial.println("digitalWrite(senserControl2Pin, HIGH);");
        Serial.println("온습도 센서 ON");
      }
    }
    else if (action == "off") {
      if (param == "조도") {
        digitalWrite(senserControl1Pin, LOW);
        Serial.println("digitalWrite(senserControl1Pin, LOW);");
        Serial.println("조도 센서 OFF");
      } else if (param == "온습도") {
        digitalWrite(senserControl2Pin, LOW);
        Serial.println("digitalWrite(senserControl2Pin, LOW);");
        Serial.println("온습도 센서 OFF");
      }
    }
    else {
      Serial.println("알 수 없는 센서 동작: " + action);
    }
  }
  else {
    Serial.println("알 수 없는 대상: " + target);
  }
}
