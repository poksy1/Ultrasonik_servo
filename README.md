# Ultrasonik_servo
#define BLYNK_TEMPLATE_ID "TMPLxxxx"
#define BLYNK_TEMPLATE_NAME "Posttest Ultrasonik Servo"
#define BLYNK_AUTH_TOKEN "IsiAuthToken"

#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <Servo.h>

char ssid[] = "NamaWifi";
char pass[] = "PasswordWifi";

#define TRIG_PIN D5
#define ECHO_PIN D6
#define SERVO_PIN D7

Servo myServo;
BlynkTimer timer;

void sendSensor() {

  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);

  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);

  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH);

  float distance = duration * 0.034 / 2;

  Serial.print("Jarak: ");
  Serial.print(distance);
  Serial.println(" cm");

  Blynk.virtualWrite(V0, distance);

  if (distance < 10) {

    myServo.write(90);

    Blynk.virtualWrite(V1, "Servo Membuka");

    Serial.println("Servo Membuka");
  }

  else {

    myServo.write(0);

    Blynk.virtualWrite(V1, "Servo Menutup");

    Serial.println("Servo Menutup");
  }
}

void setup() {

  Serial.begin(115200);

  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  myServo.attach(SERVO_PIN);

  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);

  timer.setInterval(1000L, sendSensor);
}

void loop() {

  Blynk.run();
  timer.run();
}
