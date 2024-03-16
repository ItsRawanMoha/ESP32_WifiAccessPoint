# ESP32 WiFi Access Point for Controlling LED

This task demonstrates how to create a WiFi access point using an ESP32 microcontroller and control an LED connected to it via a web interface. Users can turn the LED on or off by connecting to the ESP32's WiFi network and accessing a web page served by the ESP32.

## Introduction

The ESP32 WiFi Access Point task provides a practical example of creating a simple web server using the ESP32 microcontroller. By setting up a WiFi access point, users can connect to the ESP32's network and control an LED remotely through a web interface. This task is useful for home automation, remote control systems, and IoT applications.

## How it Works

The ESP32 microcontroller is programmed to create a WiFi access point with a specified SSID and password. It hosts a web server that serves a web page with buttons for turning the LED on and off. When a user presses one of the buttons on the web page, the ESP32 receives the corresponding HTTP request and toggles the state of the LED accordingly.

![screen-gif](https://github.com/ItsRawanMoha/ESP32_WifiAccessPoint/blob/main/D1_mini_ESP32_pinout.png)

## Getting Started

To get started with this task, you will need the following components:

- ESP32 

## Steps

Follow these steps to set up the task:

1. Connect the ESP32 to yourlaptop.
2. Connect to the WiFi network hosted by the ESP32 using a computer or mobile device.
3. Open a web browser and navigate to the IP address of the ESP32 (e.g., http://192.168.4.1).
4. Use the web interface to turn the LED on or off.

## Output

Once the task is set up and the ESP32 is powered on, you can control the LED by accessing the web interface hosted by the ESP32.

## Code

```
#include <WiFi.h>
#include <WiFiClient.h>
#include <WiFiAP.h>

#define LED_BUILTIN 2   // Set the GPIO pin where you connected your test LED or comment this line out if your dev board has a built-in LED

// Set these to your desired credentials.
const char *ssid = "RobotOffice1";
const char *password = "123456789";

WiFiServer server(80);


void setup() {
  pinMode(LED_BUILTIN, OUTPUT);

  Serial.begin(115200);
  Serial.println();
  Serial.println("Configuring access point...");

  // You can remove the password parameter if you want the AP to be open.
  // a valid password must have more than 7 characters
  if (!WiFi.softAP(ssid, password)) {
    log_e("Soft AP creation failed.");
    while(1);
  }
  IPAddress myIP = WiFi.softAPIP();
  Serial.print("AP IP address: ");
  Serial.println(myIP);
  server.begin();

  Serial.println("Server started");
}

void loop() {
  WiFiClient client = server.available();   // listen for incoming clients

  if (client) {                             // if you get a client,
    Serial.println("New Client.");           // print a message out the serial port
    String currentLine = "";                // make a String to hold incoming data from the client
    while (client.connected()) {            // loop while the client's connected
      if (client.available()) {             // if there's bytes to read from the client,
        char c = client.read();             // read a byte, then
        Serial.write(c);                    // print it out the serial monitor
        if (c == '\n') {                    // if the byte is a newline character

          // if the current line is blank, you got two newline characters in a row.
          // that's the end of the client HTTP request, so send a response:
          if (currentLine.length() == 0) {
            // HTTP headers always start with a response code (e.g. HTTP/1.1 200 OK)
            // and a content-type so the client knows what's coming, then a blank line:
            client.println("HTTP/1.1 200 OK");
            client.println("Content-type:text/html");
            client.println();

            // the content of the HTTP response follows the header:
            client.print("Click <a href=\"/H\">here</a> to turn ON the LED.<br>");
            client.print("Click <a href=\"/L\">here</a> to turn OFF the LED.<br>");

            // The HTTP response ends with another blank line:
            client.println();
            // break out of the while loop:
            break;
          } else {    // if you got a newline, then clear currentLine:
            currentLine = "";
          }
        } else if (c != '\r') {  // if you got anything else but a carriage return character,
          currentLine += c;      // add it to the end of the currentLine
        }

        // Check to see if the client request was "GET /H" or "GET /L":
        if (currentLine.endsWith("GET /H")) {
          digitalWrite(LED_BUILTIN, HIGH);               // GET /H turns the LED on
        }
        if (currentLine.endsWith("GET /L")) {
          digitalWrite(LED_BUILTIN, LOW);                // GET /L turns the LED off
        }
      }
    }
    // close the connection:
    client.stop();
    Serial.println("Client Disconnected.");
  }
}
```

## Pictures

<img src="https://github.com/ItsRawanMoha/ESP32_WifiAccessPoint/blob/main/1.jpeg" alt="Alt text" width="250" height="600"> <img src="https://github.com/ItsRawanMoha/ESP32_WifiAccessPoint/blob/main/2.jpeg" alt="Alt text" width="250" height="600"> <img src="https://github.com/ItsRawanMoha/ESP32_WifiAccessPoint/blob/main/3.jpeg" alt="Alt text" width="250" height="600">

![screen-gif](https://github.com/ItsRawanMoha/ESP32_WifiAccessPoint/blob/main/WiFiAccessPoint.gif)

## Conclusion

The ESP32 WiFi Access Point task provides a practical demonstration of using an ESP32 microcontroller to create a WiFi network and serve a web page for controlling an LED remotely. By understanding the principles behind WiFi communication and web server hosting on microcontrollers, you can explore more advanced IoT applications and tasks.
