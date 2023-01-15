# Manual-Mattress-Mate
   A manual describing the process of lighting up a LED strip at a certain time during the day.

# Content
- Before we get started
  - What are we going to do?
  - Required hardware
  - Required software
  - Arduino libraries
  - Installing Arduino
- The code
- My installation experience
  - List of errors
- Sources

# Before we get started
# What are we going to do?
The aim of this manual is to help you get the date and time from an NTP server. For my project I also wanted LEDs (Adafruit Neopixel) to light up a certain color at a specific time in order to emulate the effect of Mattress Mate warming up and cooling down. This added step of using LEDs will also be included in this tutorial.

This is only a simulation of the intended function of my product. In my case the trigger for the mattress would be the date and time instead of the room temperature, triggering the LED strip to light up (simulating the mattress heating up).

# Required hardware

The hardware required for this tutorial is the following:
- A laptop or desktop with a stable internet connection
- A board that can connect to wi-fi (I'm using a NodeMCU ESP8266 as shown below)

<img src="/images-map-manual/esp8266.png">

- A ledstrip as shown below

<img src="/images-map-manual/ledstrip.png">

- An adapter for the board

- Entry level understanding of Arduino code

# Required software

- [Arduino software](https://www.arduino.cc/en/software)

# Arduino libraries

# Installing Arduino

Before we do get started on the actual tutorial portion of this manual, you need to install Arduino and make sure it connects to a board that can both send out and receive Wi-Fi signals. In my case this is the NodeMCU ESP8266.

You also have to make sure your board is properly installed so that it can connect to the Arduino software. Do this by selecting your specific board and port in Arduino.

<img src="/images-map-manual/connectport.png">

To ensure that you do indeed have your board installed, navigate to the Tools option and follow these steps: Tools>Board>Board Manager. Once you're there, look for "esp". select esp8266 by ESP8266

# The tutorial

In order to get the time, we’ll use the NTPClient library. In your Arduino IDE, go to Sketch > Library > Manage Libraries.

Search for NTPClient and install the library by Fabrice Weinber as shown in the following image.

<img src="/images-map-manual/ntpclient-fabrice-weinberg.png"> 

## Important
source: https://randomnerdtutorials.com/esp8266-nodemcu-date-time-ntp-client-server-arduino/

Copy the following code into Arduino. This will serve as a template to get started.

~~~ /*
  Rui Santos
  Complete project details at https://RandomNerdTutorials.com/esp8266-nodemcu-date-time-ntp-client-server-arduino/
  
  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files.
  
  The above copyright notice and this permission notice shall be included in all
  copies or substantial portions of the Software.
*/

#include <ESP8266WiFi.h>
#include <NTPClient.h>
#include <WiFiUdp.h>

// Replace with your network credentials
const char *ssid     = "REPLACE_WITH_YOUR_SSID";
const char *password = "REPLACE_WITH_YOUR_PASSWORD";

// Define NTP Client to get time
WiFiUDP ntpUDP;
NTPClient timeClient(ntpUDP, "pool.ntp.org");

//Week Days
String weekDays[7]={"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"};

//Month names
String months[12]={"January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"};

void setup() {
  // Initialize Serial Monitor
  Serial.begin(115200);
  
  // Connect to Wi-Fi
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

// Initialize a NTPClient to get time
  timeClient.begin();
  // Set offset time in seconds to adjust for your timezone, for example:
  // GMT +1 = 3600
  // GMT +8 = 28800
  // GMT -1 = -3600
  // GMT 0 = 0
  timeClient.setTimeOffset(0);
}

void loop() {
  timeClient.update();

  time_t epochTime = timeClient.getEpochTime();
  Serial.print("Epoch Time: ");
  Serial.println(epochTime);
  
  String formattedTime = timeClient.getFormattedTime();
  Serial.print("Formatted Time: ");
  Serial.println(formattedTime);  

  int currentHour = timeClient.getHours();
  Serial.print("Hour: ");
  Serial.println(currentHour);  

  int currentMinute = timeClient.getMinutes();
  Serial.print("Minutes: ");
  Serial.println(currentMinute); 
   
  int currentSecond = timeClient.getSeconds();
  Serial.print("Seconds: ");
  Serial.println(currentSecond);  

  String weekDay = weekDays[timeClient.getDay()];
  Serial.print("Week Day: ");
  Serial.println(weekDay);    

  //Get a time structure
  struct tm *ptm = gmtime ((time_t *)&epochTime); 

  int monthDay = ptm->tm_mday;
  Serial.print("Month day: ");
  Serial.println(monthDay);

  int currentMonth = ptm->tm_mon+1;
  Serial.print("Month: ");
  Serial.println(currentMonth);

  String currentMonthName = months[currentMonth-1];
  Serial.print("Month name: ");
  Serial.println(currentMonthName);

  int currentYear = ptm->tm_year+1900;
  Serial.print("Year: ");
  Serial.println(currentYear);

  //Print complete date:
  String currentDate = String(currentYear) + "-" + String(currentMonth) + "-" + String(monthDay);
  Serial.print("Current date: ");
  Serial.println(currentDate);

  Serial.println("");

  delay(2000);
} 
~~~

After you've copied the raw code into your file, change the timeClient.setTimeOffset from 0 to 7200 as shown in the image below:

<img src="/images-map-manual/timeclient7200.png"> 

After you have completed this step. You must write an if else statement like in the following image:

<img src="/images-map-manual/pipo.png"> 

At this period in time only use the serial monitor (highlighted in red below) to see if your code works.

<img src="/images-map-manual/serialmonitor.png">

Once you know the code works properly, navigate to File > Example > Adafruit NeoPixel > Simple (as shown below)

<img src="/images-map-manual/adafruit neopixel simple.png"> 

Ensure you check your serial monitor frequently in case of any sudden disconnect with your network. If you are no longer connected to your network, a row of dots will be displayed in the serial monitor. However, this is only an issue regarding connection and doesn't mean that your code is the problem.
In order to prevent this, make sure you're not connected to a 5Ghz network. (see below the dots highlighted in red)

<img src="/images-map-manual/wifi connect error.png"> 

Afterwards add the following code in order to change the color of your LED depending on the time of day.

<img src="/images-map-manual/ifelse2.png"> 

Once you've followed all the steps. You should be able to change the color of your LED depending on the time of day.

# Errors

Below is an image of the error I encountered. It wasn't anything major as it was only a misplaced bracket in the code.

<img src="/images-map-manual/bracketerror.png"> 

# Sources

https://randomnerdtutorials.com/esp8266-nodemcu-date-time-ntp-client-server-arduino/


