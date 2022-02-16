# อธิบายการเขียนโปรแกรมการทดลองที่1-6
## การทดลองที่ 1 เรื่อง การเขียนโปรแกรมเพื่อรันบนไมโครคอนโทรลเลอร์
โปรแกรมที่มีคำสั่งงให้นับเลขไปเรื่อยๆ โดยมีคำสั่งให้delay ทุกๆ1000 ms(1 วินาที) โดยมีวิธี resetคือให้นับใหม่โดยการกดปุ่ม

ตัวอย่างการRUN Program 

#include <Arduino.h>

int cnt = 0;

void setup() { Serial.begin(115200); }

void loop() { cnt++; Serial.printf("PATTANI :%d\n",cnt); int s = cnt % 5 + 1; int d = s * 1000; delay(d); 

## การทดลองที่ 2 เรื่อง การเขียนโปรแกรมค้นหาไวไฟ
โปรแกรมนี้เป็นโปรแกรมค้นหาไวไฟโดยซึ่งจะแบ่งการทำงานออกเป็น 2 ส่วน คือ set upและคำสั่งวนลูปเพื่อให้ค้นหาไวไฟที่อยู่รอบๆใหม่ จะมีคำสั่งreset ให้ใช้งานโดยการกดปุ่ม

ตัวอย่างการRUN Program

#include <Arduino.h>
#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	WiFi.mode(WIFI_STA);
	WiFi.disconnect();
	delay(100);
	Serial.println("\n\n\n");
}

void loop()
{
	Serial.println("========== เริ่มต้นแสกนหา Wifi ===========");
	int n = WiFi.scanNetworks();
	if(n == 0) {
		Serial.println("NO NETWORK FOUND");
	} else {
		for(int i=0; i<n; i++) {
			Serial.print(i + 1);
			Serial.print(": ");
			Serial.print(WiFi.SSID(i));
			Serial.print(" (");
			Serial.print(WiFi.RSSI(i));
			Serial.println(")");
			Serial.print(WiFi.channel(i));
			delay(10);
		}
	}
	Serial.println("\n\n");
	delay(10 * 1000);
}

## การทดลองที่ 3 เรื่อง การเขียนโปรแกรมเอ้าท์พุทสัญญาณดิจิทัล
โปรแกรมนี้เป็นคำสั่งในการเปิดปิดไฟโดยใช้คำสั่งให้นับเลขไปเรื่อยๆ เมื่อครบ delay 500msหรือ 0.5วินาทีจะเพิ่มในความจำขึ้นเรื่อยๆ เมื่อตรวจสอบแล้วว่าเป็นเลขคู่จะทำให้ไปปิดและเมื่อเป็นเลขคี่ไฟจะเปิด
ตัวอย่างการRUN Program

#include <Arduino.h>
#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	pinMode(0, OUTPUT);
	Serial.println("\n\n\n");
}

void loop()
{
	cnt++;
	if(cnt % 2) {
		Serial.println("========== ON ===========");
		digitalWrite(0, HIGH);
	} else {
		Serial.println("========== OFF ===========");
		digitalWrite(0, LOW);
	}
	delay(500);
}
## การทดลองที่ 4 เรื่อง การเขียนโปรแกรมอินพุทสัญญาณดิจิทัล
โปรแกรมนี้มีคำสั่งคล้ายๆการทดลองที่ 3 แต่เป็นการรับค่าinput ที่port 0 และoutputที่ port 2 โดยถ้ารับค่าได้ 1 จะแสดงค่าเป็นoff และ ถ้า0เป็นOn

ตัวอย่างการRUN Program

#include <ESP8266WiFi.h>

int cnt = 0;

void setup()
{
	Serial.begin(115200);
	pinMode(0, INPUT);
	pinMode(2, OUTPUT);
	Serial.printIn("\n\n\n");
}

void loop()
{
	int val = digitalRead(0);
	Serial.printf("======= read %d\n", val);
	if(val==1) {
		digitalWrite(2, LOW);
	} else {
	    digitalWrite(2, LOW);
	}
	delay(100);
}
## การทดลองที่ 5 เรื่อง การเขียนโปรแกรมเชื่อมต่อไวไฟและเว็บเซอร์เวอร์
โปรแกรมนี้มีการกำหนด UserและPassword ให้เป็นการเชื่อมต่อกับสัญญาณที่กำหนดไว้จากนั้นทำการเชื่อมต่อกับserverเพื่อแสดงผล โดยนำMicrocontrollerมาใช้งาน

ตัวอย่างการRUN Program

#include <ESP8266WiFi.h>
//#include <WiFiClient.h>
#include <ESP8266WebServer.h>

const char* ssid = "HI_BMFWIFI_2.4G";
const char* password = "0819110933";

ESP8266WebServer server(80);

int cnt = 0;

void setup(void){
	Serial.begin(115200);

	WiFi.mode(WIFI_STA);
	WiFi.begin(ssid, password);
	while (WiFi.status() != WL_CONNECTED) {
		delay(500);
		Serial.print(".");
	}
	Serial.print("\n\nIP address: ");
	Serial.println(WiFi.localIP());

	server.onNotFound([]() {
		server.send(404, "text/plain", "Path Not Found");
	});

	/// http://192.0.0.1/ = Hello cnt: ???
	server.on("/", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});
	/// http://192.0.0.1/on = Hello cnt: ???
	server.on("/on", []() {
		cnt++;
		String msg = "Switch on ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});
	/// http://192.0.0.1/off = Switch off: ???
	server.on("/off", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});

	server.begin();
	Serial.println("HTTP server started");
}

void loop(void){
  server.handleClient();
}
## การทดลองที่ 6 เรื่อง การเขียนโปรแกรมสร้างไวไฟแอคเซสพอยต์
โปรแกรมนี้จะสร้าง Wifi AP โดยต้องมีกำหนด UserและPassword จากนั้นESP-01จะบอก WifiAddresssและข้อมูลอื่นๆ และจัดเตรียมserver ไว้เพื่อให้อุปกรณ์อื่นเข้ามาเชื่อมสัญญาณได้

ตัวอย่างการRUN Program

#include <ESP8266WiFi.h>
//#include <WiFiClient.h>
#include <ESP8266WebServer.h>

const char* ssid = "MY-ESP8266";
const char* password = "choompol";

IPAddress local_ip(192, 168, 1, 1);
IPAddress gateway(192, 168, 1, 1);
IPAddress subnet(255, 255, 255, 0);

ESP8266WebServer server(80);

int cnt = 0;

void setup(void){
	Serial.begin(115200);

	WiFi.softAP(ssid, password);
	WiFi.softAPConfig(local_ip, gateway, subnet);
	delay(100);

	server.onNotFound([]() {
		server.send(404, "text/plain", "Path Not Found");
	});

	server.on("/", []() {
		cnt++;
		String msg = "Hello cnt: ";
		msg += cnt;
		server.send(200, "text/plain", msg);
	});

	server.begin();
	Serial.println("HTTP server started");
}

void loop(void){
  server.handleClient();
}
