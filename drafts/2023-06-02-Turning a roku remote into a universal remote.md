---
layout: post
title:  "Turning a roku remote into a universal remote"
date:   2023-06-02 09:44:16 -0400
author: Snapwhiz914
tags: remote ESP-01 arduino
---

# Background

As the first post of this blog, I want to start with a smaller project. I've been wanting to do this for a while, since my entertainment system's current universal remote system needs improvement.

## Our current universal remote system

Currently, a [Pro24.r Plus Remote](https://procontrol.com/product/pro24-r-remote/) works with a [Pro24.r Plus Processor](https://procontrol.com/product/prolink-r-processor/) to control a [Roku Ultra](https://www.roku.com/products/roku-ultra) and a [Yamaha RX-A780 AV reciever](https://usa.yamaha.com/products/audio_visual/av_receivers_amps/rx-a780_u/index.html) that connects to a 5.1 surround sound system. The [Roku Voice Remote](https://www.roku.com/products/accessories/roku-voice-remote) that came with it does still work. The universal remote, however, isn't well suited to the roku. It doesn't have a microphone, so no voice search, the touchscreen at the top is too sensitive and the buttons on it are often accidentaly pressed. It can't handle inputs properly on some roku channels. For example, on Netflix, every one button press moves the selector over twice, making it almost impossible to navigate.

## The goal

I want the roku remote to be able to control both the TV's power (the TV is an older Samsung flatscreen, not a smart TV), and the reciever's volume control and power.

## Hardware

I will have an [ESP-01](https://www.amazon.com/MakerFocus-Wireless-Transceiver-DC3-0-3-6V-Compatible/dp/B01EA3UJJ4) board connected to an [IR reciever](https://pdf1.alldatasheet.com/datasheet-pdf/view/252392/VISHAY/TSOP39238.html) that sits underneath the TV in the main room that recivers the IR commands from the roku remote, since it has a built in function that can send TV IR codes.

In the room where the Roku Ultra and AV receiver are, I will use another ESP-01 to communicate with the other one over wifi, and it will have an IR emmitter that faces the AV reciever to send volume commands to it.

# Main room reciever (ESP-01)

## Finding the IR codes

First, I need to figure out the IR codes that the roku remote sends. I need the volume up and down, and the power button, since I will also need to turn the reciver on and off. Luckily, there is already a library designed for this: IRremoteESP8266. I used the [IRrecvDemo](https://github.com/crankyoldgit/IRremoteESP8266/blob/master/examples/IRrecvDemo/IRrecvDemo.ino) example to find the IR codes. The only thing I needed to change was the reciever pin number to 2.

If you've worked with an ESP-01, you'll know that programming them is no easy task, relative to the simple USB ports on larger development boards. I'm using a [USB to TTL cable](https://www.adafruit.com/product/954) to connect to the following circuit (note that this includes the IR receiver as well, since they can be connected at the same time):

![ESP-01](/assets/LR-esp01.png)

Careful - the ESP-01 runs on 3.3 volts, not 5 volts!

For uploading the code to the ESP-01, this procedure worked for me:
1. Press upload on the Arduino IDE.
2. Wait for the code to compile
3. When the bottom says "Uploading...", and in the terminal it says "Connecting...", start holding down the button on the right.
4. Press and release the button on the left. You should see the blue comm light on the ESP-01 flash a little bit right after doing this. On the arduino IDE terminal, you should see that it has connected and "Writing..." messages
5. Release the right button once it is done uploading.
6. Press the left button once, to reset it out of programming mode.

Looking at the serial monitor and pressing the buttons on the roku remote, I got these codes:

| Volume up | Volume down | Power toggle |
| --------- | ----------- | ------------ |
| E0E0E01F  | E0E0D02F    | E0E040BF     |

## Writing the send code

The code will constantly listen for IR inputs from the reciever and will send an HTTP POST request to the other ESP-01's IP, which will be listening for these HTTP requests.

```c++
#include <IRrecv.h>
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <WiFiClient.h>
#include <IRutils.h>

const char* ssid = "WIFI_SSID";
const char* password = "WIFI_PASSPHRASE";

String ip = "http://ESP01_SERVER_ADDR:PORT/";

int pin = 2;

IRrecv irrecv(pin);
decode_results results;

void setup() {
  Serial.begin(115200);
  // while (!Serial) {
  //   delay(100);
  // }
  irrecv.enableIRIn();
  Serial.println("Started IR reciever");
  WiFi.begin(ssid, password);
  Serial.print("Connecting");
  while(WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }
  Serial.println("");
  Serial.print("Connected to WiFi network with IP Address: ");
  Serial.println(WiFi.localIP());
  WiFi.hostname("IRTS_LR_ESP-01");
}

void loop() {
  if (irrecv.decode(&results)) {
    // print() & println() can't handle printing long longs. (uint64_t)
    serialPrintUint64(results.value, HEX);
    if (results.value == 3772833823) { //E0E0E01F
      //volup
      reportButton("volup");
    }
    if (results.value == 3772829743) { //E0E0D02F
      //voldn
      reportButton("voldn");      
    }
    if (results.value == 3772793023) { //E0E040BF
      //power
      reportButton("power");
    }
    Serial.println();
    irrecv.resume();  // Receive the next value
  }
  delay(50);
}

void reportButton(String code) {
  WiFiClient client;
  HTTPClient http;
  http.begin(client, ip);
  http.addHeader("Content-Type", "application/x-www-form-urlencoded");
  int httpResponseCode = http.POST("button_code=" + code);
  Serial.print("HTTP Response code: ");
  Serial.println(httpResponseCode);   
  // Free resources
  http.end();  
}
```

For testing purposes, I changed the IP and port to my development machine on the same wifi as the ESP-01 running

```bash
netcat -l port
```

to see if it all works. After pressing the volume up button:

```bash
$ netcat -l PORT
POST / HTTP/1.1
Host: IP:PORT
User-Agent: ESP8266HTTPClient
Accept-Encoding: identity;q=1,chunked;q=0.1,*;q=0
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 17

button_code=volup
```

Perfect!

# Closet room sender (ESP-01)

## Finding the IR codes

Using the IR reciever demo, I got the IR codes of the AV reciever's volume control and power toggle:

| Volume up | Volume down | Power toggle |
| --------- | ----------- | ------------ |
| 5EA158A7  | 5EA1D827    | 7E8154AB     |

The circuit design is quite similiar to the other ESP-01, but with a transistor and an IR sender instead:

![ESP-01](/assets/esp01-program.png)