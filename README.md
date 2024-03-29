# Manual-Mattress-Mate
   A manual describing the process of lighting up a LED strip at a certain time during the day.

# Content
- Before we get started
  - What are we going to do?
  - Required hardware
  - Required software
  - Arduino libraries
  - Installing Arduino
  - Port connection error
- The tutorial
  - Network connection error
  - Code misplacement error
  - Final result
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

The library I will be using for this tutorial is the NTPClient library by Fabrice Weinber.

# Installing Arduino

Before we do get started on the actual tutorial portion of this manual, you need to install Arduino and make sure it connects to a board that can both send out and receive Wi-Fi signals. In my case this is the NodeMCU ESP8266.

You also have to make sure your board is properly installed so that it can connect to the Arduino software. Do this by selecting your specific board and port in Arduino.

<img src="/images-map-manual/rightconnectport.png">

# Port connection error

Failing to select the right port will give you problems later down the line, as you won't be able to connect to your board and start the tutorial.
(this will result in the following port connection errors as shown below)

<img src="/images-map-manual/wrongportconnection.png">

<img src="/images-map-manual/portuploaderror.png">

If you've successfully connected to the right port, you should get a message similar to the following:

<img src="/images-map-manual/rightport.png">

To ensure that you do indeed have your board installed, navigate to the Tools option and follow these steps: Tools>Board>Board Manager. Once you're there, look for "esp". select esp8266 by ESP8266

# The tutorial

In order to get the time data, we’ll use the NTPClient library. In your Arduino IDE, go to Sketch > Library > Manage Libraries.

Search for NTPClient and install the library by Fabrice Weinber as shown in the following image.

<img src="/images-map-manual/ntpclient-fabrice-weinberg.png"> 

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

<img src="/images-map-manual/7200timeclient.png"> 

After you have completed this step. You must write an "if else" statement like in the following image:

<img src="/images-map-manual/pipo.png"> 

At this period in time only use the serial monitor (highlighted in red in the image below) to see if your code works.

<img src="/images-map-manual/serialmonitor.png">

Once you know the code works properly, navigate to File > Example > Adafruit NeoPixel > Simple (as shown below)

<img src="/images-map-manual/adafruit neopixel simple.png"> 

# Network connection error

https://siytek.com/esp8266-cant-connect-to-wifi-its-probably-one-of-these/

Check your serial monitor frequently to ensure you are connected to the (right) network. If you see a row of dots displayed in your serial monitor it means you are either no longer connected to a network, or you ARE connected to a 5Ghz network however, this is not compatible (see link above). Please bear in mind, this is only an issue regarding connection and doesn't mean that your code is the problem. In order to prevent this, make sure you're connected to any network that doesn't run on 5Ghz. (see below the dots highlighted in red)

<img src="/images-map-manual/wifi connect error.png"> 

Afterwards add the following code in order to change the color of your LED depending on the time of day.

<img src="/images-map-manual/ifelse2.png"> 

Once you've followed all the steps. You should be able to change the color of your LED depending on the time of day.

# Code misplacement error

If you encounter any illogical errors, start off by looking for any misplaced characters within your code, such as: }, ;, :, etc.
Sometimes finding these will resolve the error and save you time looking for a problem that might not be there.

Below is an image of a similar error I encountered. It wasn't anything major as it was only a misplaced bracket in the code.
Once I removed the "}" at line 165, it resolved the issue.  

<img src="/images-map-manual/bracketerror.png"> 

# Final result

This is the [final result](https://youtu.be/utAy1oouMkE)

# Sources

https://randomnerdtutorials.com/esp8266-nodemcu-date-time-ntp-client-server-arduino/

https://siytek.com/esp8266-cant-connect-to-wifi-its-probably-one-of-these/

