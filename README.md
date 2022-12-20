# Gelocation-using-ESP3266
CONTENT



ABSTRACT

The development board is based on the ESP8266 Wi-Fi chip – a low-cost, low-power system-on-a-chip with integrated Wi-Fi and low-power modes. The project will involve utilizing its onboard Wi-Fi module to detect the presence of nearby Access points and then report its location to the user via their device or PC.

This paper presents a location tracker using an ESP3266 development board for Parcel. This solution offers a cost-effective and reliable way of tracking parcel items. The ESP3266 development board is used to connect to a local area network, allowing for secure and accurate real-time tracking. Additionally, the development board is programmed to send the data of nearby Wi-Fi access points and send it to a google geolocation API for processing.
The server crawls through its database to determine the location of device based on the MAC addresses reported by module.


INTRODUCTION
This project involves the design and implementation of a tracking tool using the ESP32-based development board. The main purpose of the project is to create a data collection and monitoring system, which will allow for the tracking of parcels in transit and provide notifications when certain conditions have been met. The main goals of the project are to create an easy-to-use interface for users, provide accurate monitoring of multiple parcels in transit, and to be able to connect to the internet for data transfer and real-time notifications. Additionally, the project will aim to offer a secure system for data collection and transfer, as well as provide scalability to accommodate future upgrades.
The Geolocation API returns a location and accuracy radius based on information about cell towers and Wi-Fi nodes that the mobile client can detect. This document describes the protocol used to send this data to the server and to return a response to the client.
Since, we have a Wi-Fi module so we will send only the data of nearby Wi-Fi nodes. Hence reducing the cost and power required for detecting cell tower signals saving energy and power. 

 

Aims:-
 
AIMS AND OBJECTIVE
 
The basic aim is to design a Wi-Fi based location tracker which is cost efficient and easy to operate.

Objectives:-
1.	To design a location tracker for non-technical Person.
2.	To track important packages cost effectively.
3.	To implement location tracker without using high power consuming GPS.
4.	To increase the accuracy of tracking using Wi-Fi based location tracking.
5.	Save energy by tracking just the Wi-Fi Access points in range.

 
COMPONENTS USED:-


1.	NodeMCU ESP8266
NodeMCU ESP8266 is an IoT Module based on the ESP8266 Wifi Module. It has two switches, one is the reset and the other is the flash button. Flash button is used for downloading the code and is used while upgrading the firmware. Reset button is used for resetting.

2.	Micro USB Cable
Micro USB Cable for ESP3266 is the emerging standard for charging and transferring data. The data transfer bandwidth along with power output could be great for the raspberry pi as well. It could be used to not only power the pi but to display it as well in one port.
 
Implementation
To avoid the setup complexity of project all the functionality is prog a single development board i.e. ESP3266. Now we will upload the code to the microcontroller with the help of ‘Arduino IDE software’. In this the code is compiled and uploaded to the microcontroller.
Libraries used
ArduinoJson 
Deduplicates strings in the JSON document.
When you have several identical keys or values, the Json Document only stores one of each.
In practice, this feature reduces memory consumption by 20-30%.
ArduinoJson doesn’t depend on any library; in fact, it doesn’t even depend on Arduino!
It means you can use ArduinoJson anywhere you can compile C++ code.
The best example is your test suite: you can comfortably test your program on your computer without touching a microcontroller.
ArduinoJson is open-source and uses one of the most permissive licenses so you can use it on any project.

✔️️ Commercial use                                ✔️️ Modification
✔️️ Distribution                                       ✔️️ Private use

ESP8266WiFi library
ESP8266 is all about Wi-Fi. If you are eager to connect your new ESP8266 module to a Wi-Fi network to start sending and receiving data, this is a good place to start. 

Devices that connect to Wi-Fi networks are called stations (STA). Connection to Wi-Fi is provided by an access point (AP), that acts as a hub for one or more stations. The access point on the other end is connected to a wired network. An access point is usually integrated with a router to provide access from a Wi-Fi network to the internet. Each access point is recognized by a SSID (Service Set IDentifier), that essentially is the name of network you select when connecting a device (station) to the Wi-Fi.
The ESP8266WiFi library is broken up into several classes. In most of cases, when writing the code, the user is not concerned with this classification. We are using it to break up description of this library into more manageable pieces.
 


Code:
#include <ESP8266HTTPClient.h>
#include <ArduinoJson.h>
#include "ESP8266WiFi.h"

char myssid[] = "IOTConnect";         // your network SSID (name)
char mypass[] = "11111111";          // your network password

//Credentials for Google GeoLocation API...
const char* Host = "www.googleapis.com";
String thisPage = "/geolocation/v1/geolocate?key=";
String key = "AIzaSyB7kPaqDbLkSYW8Km2h_ZJVTiIt3VOmLec";

int status = WL_IDLE_STATUS;
String jsonString = "{\n";

double latitude    = 0.0;
double longitude   = 0.0;
double accuracy    = 0.0;
int more_text = 1; // set to 1 for more debug output



void setup()   {
  Serial.begin(9600);

  Serial.println("Start");
  // Set WiFi to station mode and disconnect from an AP if it was previously connected
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
  delay(100);
  Serial.println("Setup done");
  // We start by connecting to a WiFi network
  Serial.print("Connecting to ");
  Serial.println(myssid);
  WiFi.begin(myssid, mypass);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println(".");

}

void loop() {

  char bssid[6];
 DynamicJsonDocument doc(1024);
  Serial.println("scan start");
  // WiFi.scanNetworks will return the number of networks found
  int n = WiFi.scanNetworks();
  Serial.println("scan done");
  if (n == 0)
    Serial.println("no networks found");
  else
  {
    Serial.print(n);
    Serial.println(" networks found...");

    if (more_text) {
      // Print out the formatted json...
      Serial.println("{");
      Serial.println("\"homeMobileCountryCode\": 234,");  // this is a real UK MCC
      Serial.println("\"homeMobileNetworkCode\": 27,");   // and a real UK MNC
      Serial.println("\"radioType\": \"gsm\",");          // for gsm
      Serial.println("\"carrier\": \"Vodafone\",");       // associated with Vodafone
      //Serial.println("\"cellTowers\": [");                // I'm not reporting any cell towers
      //Serial.println("],");
      Serial.println("\"wifiAccessPoints\": [");
      for (int i = 0; i < n; ++i)
      {
        Serial.println("{");
        Serial.print("\"macAddress\" : \"");
        Serial.print(WiFi.BSSIDstr(i));
        Serial.println("\",");
        Serial.print("\"signalStrength\": ");
        Serial.println(WiFi.RSSI(i));
        if (i < n - 1)
        {
          Serial.println("},");
        }
        else
        {
          Serial.println("}");
        }
      }
      Serial.println("]");
      Serial.println("}");
    }
    Serial.println(" ");
  }
  // now build the jsonString...
  jsonString = "{\n";
  jsonString += "\"homeMobileCountryCode\": 234,\n"; // this is a real UK MCC
  jsonString += "\"homeMobileNetworkCode\": 27,\n";  // and a real UK MNC
  jsonString += "\"radioType\": \"gsm\",\n";         // for gsm
  jsonString += "\"carrier\": \"Vodafone\",\n";      // associated with Vodafone
  jsonString += "\"wifiAccessPoints\": [\n";
  for (int j = 0; j < n; ++j)
  {
    jsonString += "{\n";
    jsonString += "\"macAddress\" : \"";
    jsonString += (WiFi.BSSIDstr(j));
    jsonString += "\",\n";
    jsonString += "\"signalStrength\": ";
    jsonString += WiFi.RSSI(j);
    jsonString += "\n";
    if (j < n - 1)
    {
      jsonString += "},\n";
    }
    else
    {
      jsonString += "}\n";
    }
  }
  jsonString += ("]\n");
  jsonString += ("}\n");
  //--------------------------------------------------------------------

  Serial.println("");

  WiFiClientSecure client;
  client.setInsecure();

  //Connect to the client and make the api call
  Serial.print("Requesting URL: ");
  Serial.println("https://" + (String)Host + thisPage + "AIzaSyCYNXIYINPmTNIdusMjJloS4_BXSOff1_g");
  Serial.println(" ");
  if (client.connect(Host, 443)) {
    Serial.println("Connected");
    client.println("POST " + thisPage + key + " HTTP/1.1");
    client.println("Host: " + (String)Host);
    client.println("Connection: close");
    client.println("Content-Type: application/json");
    client.println("User-Agent: Arduino/1.0");
    client.print("Content-Length: ");
    client.println(jsonString.length());
    client.println();
    client.print(jsonString);
    delay(800);
  }

  //Read and parse all the lines of the reply from server
  while (client.available()) {
    String line = client.readStringUntil('\r');
    if (more_text) {
      Serial.print(line);
    }
          DeserializationError error = deserializeJson(doc,line);
           
     if (error) {
      
     }
     latitude    = doc["location"]["lat"];
      longitude   = doc["location"]["lng"];
      accuracy   = doc["accuracy"];
  }

  Serial.println("closing connection");
  Serial.println();
  client.stop();
  delay(1000);

  // Serial.print("Latitude = ");
  // Serial.println(latitude, 6);
  // Serial.print("Longitude = ");
  // Serial.println(longitude, 6);
  // Serial.print("Accuracy = ");
  // Serial.println(accuracy);
  // Serial.print("Link to access the location- "); 

}


CHALLENGES AND LIMITATIONS
•	The Wi-Fi modules functionality completely depends on the response time of API.

•	Availability of Wi-Fi networks is important as the module is completely dependent on the connectivity provided by Wi-Fi access points .


•	Since only Wi-Fi is being used for location tracking the accuracy might not be as accurate as GPS . 

•	The reduction in power requirement leads to trade off with accuracy of module .

 
CONCLUSION

Reduction in cost and complexity of project leads to simplicity and easy implementation of project. It can be deployed very easily but the reduction in cost and complexity leads to a tradeoff of accuracy.
The project is suitable for applications that require a rough estimate of location and accuracy of a few meters is acceptable. The possible use cases involve tracking a parcel unit or a vehicle. 
The Geolocation API returns a location and accuracy radius based on information Wi-Fi nodes that the mobile client can detect. The ESP3266 module sends  data to the server and  returns a response to the client with latitude longitude and ac
