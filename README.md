1.
문제 해결 능력, 디자인 사고 개발, 협업과 팀워크, 기술 및 디지털 능력, 효과적인 소통 기술이 창의공학을 통해 학생들이 학습해야 할 내용인 것 같습니다.
2.
재료 및 도구: 
(1) 아두이노 보드 
(2) 무선 통신 모듈 (예: Wi-Fi 모듈 또는 블루투스 모듈)
(3) 전원 공급 장치 (집 전력과 호환되는 것)
(4) 전등 릴레이 모듈
(5) 스마트폰
(6) 프로세싱 또는 앱 인벤터 (무선 통신 앱 개발을 위해)
과정:
(1) 아두이노 프로토타입 개발:
아두이노 보드에 무선 통신 모듈을 연결합니다. Wi-Fi 모듈을 사용하면 스마트폰과의 통신이 쉽습니다.
전등 릴레이 모듈을 아두이노에 연결합니다.
(2) 아두이노 코드 작성:
아두이노 코드를 작성하여 Wi-Fi 모듈을 설정하고 스마트폰과 통신할 수 있도록 합니다. 무선 통신 라이브러리를 사용하여 스마트폰 앱과 통신하고 전등 릴레이를 제어하는 코드를 작성합니다.
(3) 스마트폰 앱 개발:
스마트폰 앱을 개발하기 위해 프로세싱 또는 앱 인벤터와 같은 도구를 사용합니다. 앱에서는 전등을 켜고 끄는 버튼 또는 슬라이더를 추가하고, 무선 통신을 통해 아두이노와 통신할 수 있도록 앱을 구성합니다.
(4) 무선 통신 설정:
아두이노와 스마트폰 간의 무선 통신을 설정합니다. Wi-Fi 모듈을 사용하면 Wi-Fi 네트워크에 연결하거나, 블루투스 모듈을 사용하면 블루투스 연결을 설정합니다.
(5) 앱 및 아두이노 통합:
앱과 아두이노 간의 통신 프로토콜을 설정하고 데이터를 주고받을 수 있도록 프로그밍합니다. 일반적으로 TCP/IP 또는 HTTP를 사용하여 통신합니다.
(6) 시스템 테스트:
모든 구성 요소를 연결하고 스마트폰 앱을 실행하여 전등을 제어할 수 있는지 확인합니다. 스마트폰에서 전등을 켜고 끌 수 있는지 테스트합니다.
(7) 릴레이 모듈:
릴레이 모듈을 추가하여 전등을 더 안전하게 제어합니다. 릴레이는 고전압 전기를 안전하게 다루는 데 도움을 줍니다.
(8) 최종 설치 및 사용:
시스템을 집에 설치하고 스마트폰 앱을 사용하여 전등을 편리하게 제어합니다.

이러한 과정을 통해 스마트폰을 사용하여 집의 전등을 켜고 끄는 시스템을 구축할 수 있습니다. 릴레이를 추가하면 안전성과 신뢰성을 높일 수 있습니다.

3.
아두이노에서 스위치 값을 읽고 서버로 전송하며, 서버에서 해당 값을 수신하여 색을 변경하는 코드를 작성해보았습니다. 또한, 앱 인벤터를 사용하여 스마트폰 앱을 만들어 아두이노와 통신하겠습니다.

아두이노 코드 (스위치 읽기 및 서버로 전송):
#include <ESP8266WiFi.h>

const char* ssid = "YourWiFiSSID";
const char* password = "YourWiFiPassword";
const char* serverAddress = "your_server_ip_or_domain";
const int serverPort = 80; // 서버 포트

const int switchPin = 2; // 스위치 연결 핀
int switchState = 0;
int lastSwitchState = 0;
unsigned long lastSendTime = 0;
const long sendInterval = 1000; // 1초마다 서버로 전송

WiFiClient client;

void setup() {
  pinMode(switchPin, INPUT);
  Serial.begin(115200);
  delay(10);
  connectToWiFi();
}

void loop() {
  switchState = digitalRead(switchPin);

  if (switchState != lastSwitchState) {
    if (millis() - lastSendTime > sendInterval) {
      sendSwitchState(switchState);
      lastSendTime = millis();
    }
  }

  lastSwitchState = switchState;
}

void connectToWiFi() {
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
}

void sendSwitchState(int state) {
  if (client.connect(serverAddress, serverPort)) {
    Serial.println("Sending switch state to server...");
    client.print(state);
    client.stop();
  }
}

프로세싱 코드 (서버에서 수신 및 색상 변경):
import processing.net.*;

Client client;
String serverAddress = "your_server_ip_or_domain";
int serverPort = 80;

void setup() {
  size(200, 200);
  client = new Client(this, serverAddress, serverPort);
  background(0); // 초기 배경색을 검은색으로 설정
}

void draw() {
  if (client.available() > 0) {
    String message = client.readString();
    int switchState = Integer.parseInt(message.trim());

    if (switchState == 1) {
      background(0, 255, 0); // 초록색
    } else if (switchState == 2) {
      background(0); // 검은색
    }
  }
}

앱 인벤터를 사용하여 스마트폰 앱을 만들어 아두이노와 통신하고 스위치 상태를 서버로 전송하도록 할 수 있습니다. 이 코드는 스위치 값을 읽고 서버로 전송하며, 서버는 해당 값을 수신하여 색상을 변경하는 기능을 수행합니다.

4.
스마트폰에서 서버로부터 수신한 값에 따라 화면의 배경색을 변경하기 위해서는 앱 인벤터를 사용하여 앱을 개발해야 합니다. 아래는 해당 앱의 컴퍼넌트 및 내용을 설명하는 코드입니다.

앱 인벤터 컴퍼넌트:

WebViewer: 웹 뷰어 컴포넌트를 사용하여 서버와의 통신을 처리합니다.
앱 인벤터 블록 코드:
(1) 앱을 시작할 때, WebViewer의 홈페이지 URL을 서버의 주소로 설정합니다.
(2) WebViewer의 GotText 이벤트를 사용하여 서버에서 받은 텍스트를 처리합니다. 이때, 받은 값에 따라 배경색을 변경합니다.
(3) WebViewer의 LoadHTMLString 블록을 사용하여 화면에 배경색을 변경하는 HTML 코드를 실행합니다.

HTML 코드 (서버에서 전송하는 내용에 따라 배경색 변경):
서버에서 1을 보내면 푸른색, 0을 보내면 붉은색으로 화면 배경색을 변경하도록 HTML 코드를 작성합니다.
<!DOCTYPE html>
<html>
<head>
    <style>
        body {
            margin: 0;
            padding: 0;
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
        }
    </style>
</head>
<body id="body">
    <script>
        var body = document.getElementById("body");
        window.onload = function () {
            window.parent.postMessage("Ready", "*");
        };

        window.addEventListener("message", function (event) {
            if (event.data === "1") {
                body.style.backgroundColor = "blue"; // 1을 받으면 푸른색으로 변경
            } else if (event.data === "0") {
                body.style.backgroundColor = "red"; // 0을 받으면 붉은색으로 변경
            }
        });
    </script>
</body>
</html>

이 코드를 사용하여 서버에서 스마트폰으로 1 또는 0을 보내면 스마트폰의 화면 배경색이 푸른색 또는 붉은색으로 변경됩니다. 앱 인벤터와 HTML을 조합하여 이러한 동작을 구현할 수 있습니다.

5.
스마트폰에서 서버로 명령을 보내고, 아두이노에서 해당 명령을 받아 7번 핀의 LED를 제어하며, 또 다른 명령을 받아 4번 핀의 피에조 스피커를 도, 레, 미, 파, 솔, 라, 시, 도로 연주하려면 아두이노와 스마트폰 간의 통신 및 제어를 구현해야 합니다. 아래는 이를 위한 기본적인 코드와 설명입니다.

아두이노 코드 (LED 제어 및 피에조 스피커 연주):
#include <WiFi.h>
#include <Servo.h>

const char* ssid = "YourWiFiSSID";
const char* password = "YourWiFiPassword";
const int serverPort = 80;

WiFiServer server(serverPort);

const int ledPin = 7; // LED 연결 핀
const int speakerPin = 4; // 피에조 스피커 연결 핀

Servo myservo;
int pos = 0;

void setup() {
  Serial.begin(115200);
  pinMode(ledPin, OUTPUT);
  myservo.attach(speakerPin);
  connectToWiFi();
  server.begin();
}

void loop() {
  WiFiClient client = server.available();
  
  if (client) {
    while (client.connected()) {
      if (client.available()) {
        String request = client.readStringUntil('\r');
        if (request.indexOf("GET /on") != -1) {
          digitalWrite(ledPin, HIGH); // LED 켜기
        } else if (request.indexOf("GET /off") != -1) {
          digitalWrite(ledPin, LOW); // LED 끄기
        } else if (request.indexOf("GET /play") != -1) {
          playMelody();
        }
        
        client.flush();
      }
    }
    client.stop();
  }
}

void connectToWiFi() {
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
}

void playMelody() {
  for (pos = 0; pos <= 180; pos += 45) {
    myservo.write(pos);
    delay(500);
    myservo.write(0);
    delay(500);
  }
}

앱 인벤터 컴퍼넌트 및 블록:
(1) 'ButtonOn': "ON" 버튼
(2) 'ButtonOff':"OFF" 버튼
(3) 'ButtonSend': "Send" 버튼
(4) 'TextBox': 숫자를 입력할 텍스트 박스

앱 인벤터 블록:
(1) "ON" 버튼 클릭 시, 서버로 "on" 요청을 보냅니다.
(2) "OFF" 버튼 클릭 시, 서버로 "off" 요청을 보냅니다.
(3) "Send" 버튼 클릭 시, 텍스트 박스에 입력한 숫자를 서버로 보내며, 서버에서는 해당 숫자에 따라 "play" 요청을 아두이노로 보냅니다.


앱 인벤터를 사용하여 스마트폰에서 서버로 요청을 보내고, 서버에서 아두이노로 명령을 전달하여 LED를 제어하고 피에조 스피커를 연주할 수 있습니다. 

읽어주셔서 감사합니다.

