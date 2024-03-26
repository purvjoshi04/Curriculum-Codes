### **_Practical-8_**

<hr>

**_Description_**

#### In this practical i have create circuit using arduino, wifi module and ultrasonic distance sensor which sends the distance value in the cloud storage thingspeak.

<hr>

**_Circuit Diagram_**

![Screenshot (38)](https://github.com/purvjoshi04/Curriculum-Codes/assets/101319136/38ad148c-9fa5-44f8-aeee-cbf45c7005a0)

![Screenshot (39)](https://github.com/purvjoshi04/Curriculum-Codes/assets/101319136/2e68bb57-984d-4445-bfa7-90264e61797f)



<hr>

**_Arduino Code_**

```cpp

String ssid     = "Simulator Wifi";  // SSID to connect to
String password = ""; // Our virtual wifi has no password 
String host     = "api.thingspeak.com"; // Open Weather Map API
const int httpPort   = 80;
String url     = "/update?api_key=JZFG8SET6EBSW3RM&field1=";

int setupESP8266(void) {
  // Start our ESP8266 Serial Communication
  Serial.begin(115200);   // Serial connection over USB to computer
  Serial.println("AT");   // Serial connection on Tx / Rx port to ESP8266
  delay(10);        // Wait a little for the ESP to respond
  if (!Serial.find("OK")) return 1;
    
  // Connect to 123D Circuits Simulator Wifi
  Serial.println("AT+CWJAP=\"" + ssid + "\",\"" + password + "\"");
  delay(10);        // Wait a little for the ESP to respond
  if (!Serial.find("OK")) return 2;
  
  // Open TCP connection to the host:
  Serial.println("AT+CIPSTART=\"TCP\",\"" + host + "\"," + httpPort);
  delay(50);        // Wait a little for the ESP to respond
  if (!Serial.find("OK")) return 3;
  
  return 0;
}

void anydata(void) {
  
  int temp = map(analogRead(A0),20,358,-40,125);
  
  // Construct our HTTP call
  String httpPacket = "GET " + url + String(temp) + " HTTP/1.1\r\nHost: " + host + "\r\n\r\n";
  int length = httpPacket.length();
  
  // Send our message length
  Serial.print("AT+CIPSEND=");
  Serial.println(length);
  delay(10); // Wait a little for the ESP to respond if (!Serial.find(">")) return -1;

  // Send our http request
  Serial.print(httpPacket);
  delay(10); // Wait a little for the ESP to respond
  if (!Serial.find("SEND OK\r\n")) return; 
}
void setup() {
  setupESP8266();   
}

void loop() {
  
 anydata();
  
  delay(1000);
}

```
