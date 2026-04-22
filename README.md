# esp-idf-uart2web
This project is a wireless UART monitor using the ESP32 that can be accessed remotely via a web browser.   
![Image](https://github.com/user-attachments/assets/1cdf6361-3f1f-4f98-a433-56c7b6a257b3)   

I used [this](https://github.com/Molorius/esp32-websocket) component.   
This component can communicate directly with the browser.   
It's a great job.   
I referred to [this](https://github.com/ayushsharma82/WebSerial).   
![Web-Serial](https://user-images.githubusercontent.com/6020549/204442158-0e8e1b11-caa8-4937-b830-99d331ca3fa6.jpg)   

# Software requirements
ESP-IDF V5.0 or later.   
ESP-IDF V4.4 release branch reached EOL in July 2024.   
ESP-IDF V5.1 is required when using ESP32-C6.   

# Installation
```
git clone https://github.com/nopnop2002/esp-idf-uart2web
cd esp-idf-uart2web/
idf.py menuconfig
idf.py flash
```

# Configuration
![config-top](https://user-images.githubusercontent.com/6020549/164256546-da988299-c0ff-41e0-8c5a-45cdd11f9fe7.jpg)
![config-app](https://user-images.githubusercontent.com/6020549/164256573-1e6fc379-699a-4464-a93d-70160fe2a0b0.jpg)

## WiFi Setting
Set the information of your access point.   
![config-wifi](https://user-images.githubusercontent.com/6020549/164256660-c2def5c5-d524-483b-885a-fa8f32e9b471.jpg)

## NTP Setting
Set the information of your time zone.   
![config-ntp](https://user-images.githubusercontent.com/6020549/164256796-cf851736-2a8e-400f-b809-992aa2ff867e.jpg)

## UART Setting
Set GPIO to use with UART.   
![config-uart](https://user-images.githubusercontent.com/6020549/164256738-0f59817b-0deb-41b5-a4e5-379cbe3c2574.jpg)

# Write this sketch on Arduino Uno.   
You can use any AtMega328 microcontroller.   
```
unsigned long lastMillis = 0;

void setup() {
  Serial.begin(115200);
}

void loop() {
  while (Serial.available()) {
    String command = Serial.readStringUntil('\n');
    Serial.println(command);
  }

  if(lastMillis + 1000 <= millis()){
    Serial.print("Hello World ");
    Serial.println(millis());
    lastMillis += 1000;
  }

  delay(1);
}
```

Strings from Arduino to ESP32 are terminated with CR(0x0d)+LF(0x0a).   
This project will remove the termination character and send to Browser.   
```
I (1189799) UART-RX: 0x3ffc8458   48 65 6c 6c 6f 20 57 6f  72 6c 64 20 31 30 30 31  |Hello World 1001|
I (1189799) UART-RX: 0x3ffc8468   0d 0a
```

The Arduino sketch inputs data with LF as the terminator.   
So strings from the ESP32 to the Arduino must be terminated with LF (0x0a).   
If the string output from the ESP32 to the Arduino is not terminated with LF (0x0a), the Arduino sketch will complete the input with a timeout.   
The default input timeout for Arduino sketches is 1000 milliseconds.   
Since the string received from the Browser does not have a trailing LF, this project will add one to the end and send it to Arduino.   
The Arduino sketch will echo back the string it reads.   
```
I (1285439) UART-TX: 0x3ffc72f8   61 62 63 64 65 66 67 0a                           |abcdefg.|
I (1285459) UART-RX: 0x3ffc8458   61 62 63 64 65 66 67 0d  0a                       |abcdefg..|
```

# Wireing
Connect ESP32 and AtMega328 using wire cable   
|AtMega328||ESP32|ESP32-S2/S3|ESP32-C2/C3/C6||
|:-:|:-:|:-:|:-:|:-:|:-:|
|TX|--|GPIO16|GPIO2|GPIO1|(*1)|
|RX|--|GPIO17|GPIO1|GPIO0||
|GND|--|GND|GND|GND||

(*1) 5V to 3.3V level shifting is required.   

__You can change it to any pin using menuconfig.__   


# Launch a web browser   
Enter the following in the address bar of your web browser.   
```
http:://{IP of ESP32}/
or
http://esp32-server.local/
```

You can communicate to Arduino-UNO using browser.   
The Change button changes the number of lines displayed.   
The Copy button copies the received data to the clipboard.   

![Web-Serial](https://user-images.githubusercontent.com/6020549/204442158-0e8e1b11-caa8-4937-b830-99d331ca3fa6.jpg)

# WEB Pages
WEB Pages are stored in the html folder.   
I used [this](https://bulma.io/) open source css.   
You can change root.html as you like.   

# References

https://github.com/nopnop2002/esp-idf-uart2bt

https://github.com/nopnop2002/esp-idf-uart2udp

https://github.com/nopnop2002/esp-idf-uart2mqtt

https://github.com/nopnop2002/esp-idf-vcp2web

