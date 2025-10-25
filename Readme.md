# **IoT-Based Visitor Counter using ESP32 and Blynk**

This repository contains the code and documentation for an IoT-Based Visitor Counter project. The system uses an ESP32, two ultrasonic sensors, an OLED display, and a relay to count people entering and leaving a room, display the count locally, and log the data to the Blynk cloud platform. It also activates a light via a relay for a short duration when someone passes through.

## **Features**

* **Bidirectional Counting:** Accurately counts people entering and leaving a room using two ultrasonic sensors.  
* **Real-time Local Display:** Shows the current count of people inside (IN), total entered (IN-Total), and total exited (OUT-Total) on a 0.96" OLED display.  
* **Cloud Integration:** Connects to the Blynk server via Wi-Fi to send and store visitor data.  
* **Remote Monitoring:** Allows users to check the visitor count from anywhere using the Blynk mobile app or web browser.  
* **Automatic Lighting:** Activates a relay to turn on a light for a few seconds each time a person is successfully detected passing through the doorway.

## **How It Works**

The system is designed for a single doorway. Two ultrasonic sensors (Sensor 1 and Sensor 2\) are placed on opposite sides of the door frame, facing each other but offset, so a person passing through triggers them sequentially.

1. **Entering:** When a person starts to enter, they first trigger **Sensor 1**. The ESP32 records this event. If, within a short, predefined time, the same person triggers **Sensor 2**, the system registers one person as **"IN"**.  
2. **Exiting:** Conversely, if a person first triggers **Sensor 2** and then **Sensor 1** within the time threshold, the system registers one person as **"OUT"**.  
3. **Data Handling:**  
   * The ESP32 maintains a running total for "IN" and "OUT".  
   * The net count (IN \- OUT) is calculated to determine how many people are currently inside the room.  
   * This data (Current IN, Total IN, Total OUT) is continuously updated on the OLED display.  
4. **Blynk Integration:** The ESP32 connects to your Wi-Fi network and sends the count data to designated Virtual Pins on the Blynk server. This allows for real-time monitoring from the Blynk dashboard.  
5. **Relay Activation:** After a successful "IN" or "OUT" event is registered (meaning a person has passed both sensors in the correct sequence), the ESP32 activates the relay. This turns on an external light for a few seconds, providing temporary illumination for the person passing through.

## **Hardware Required**

* **Microcontroller:** 1 x ESP32 (WEMOS D1 Mini ESP32 or similar)  
* **Sensors:** 2 x HC-SR04 Ultrasonic Sensors  
* **Display:** 1 x 0.96" I2C SSD1306 OLED Display  
* **Actuator:** 1 x 5V Single Channel Relay Module  
* **Miscellaneous:**  
  * Jumper Wires  
  * Breadboard (for prototyping)  
  * External 5V Power Supply (recommended)  
  * A light bulb and holder (for use with the relay)

## **Software & Setup**

### **1\. Arduino IDE**

1. Make sure you have the Arduino IDE installed.  
2. Install the ESP32 board definitions. You can find instructions [here](https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html).  
3. Install the required Arduino libraries through the Library Manager (Sketch \> Include Library \> Manage Libraries...):  
   * Blynk by Volodymyr Shymanskyy  
   * Adafruit SSD1306 by Adafruit  
   * Adafruit GFX Library by Adafruit  
   * NewPing by Tim Eckel (recommended for easier ultrasonic sensor management)

### **2\. Blynk Setup**

1. Create a Blynk account at [blynk.cloud](https://blynk.cloud/) or through the Blynk IoT mobile app.  
2. Create a new Project (Template).  
3. In the template, define the **Datastreams** (Virtual Pins) you will use. For example:  
   * V1: Current Count Inside (Integer)  
   * V2: Total Entered (Integer)  
   * V3: Total Exited (Integer)  
4. Create a **Device** from your template to get your unique **Blynk Auth Token**.  
5. Design your web or mobile dashboard using widgets (e.g., Gauges, Labeled Values) and link them to the datastreams you created.

### **3\. Code Configuration**

1. Open the .ino (Arduino sketch) file from this repository.  
2. At the top of the file, modify the following variables:  
   // \--- START OF CONFIGURATION \---

   // Blynk Template ID, Device Name, and Auth Token (from your Blynk device)  
   \#define BLYNK\_TEMPLATE\_ID "YOUR\_TEMPLATE\_ID"  
   \#define BLYNK\_DEVICE\_NAME "YOUR\_DEVICE\_NAME"  
   \#define BLYNK\_AUTH\_TOKEN "YOUR\_AUTH\_TOKEN"

   // Your Wi-Fi network credentials  
   char ssid\[\] \= "YOUR\_WIFI\_SSID";  
   char pass\[\] \= "YOUR\_WIFI\_PASSWORD";

   // \--- END OF CONFIGURATION \---

3. Ensure the GPIO pins defined in the code for the sensors, OLED (SDA/SCL), and relay match your physical ESP32 wiring.

### **4\. Wiring**

(Add your specific wiring diagram or a detailed description here. Example:)

* **OLED Display (I2C):**  
  * SDA \-\> GPIO 21 (ESP32)  
  * SCL \-\> GPIO 22 (ESP32)  
  * VCC \-\> 3.3V  
  * GND \-\> GND  
* **Ultrasonic Sensor 1 (IN-Sensor):**  
  * TRIG \-\> GPIO\_PIN\_2  
  * ECHO \-\> GPIO\_PIN\_4  
  * VCC \-\> 3.3V  
  * GND \-\> GND  
* **Ultrasonic Sensor 2 (OUT-Sensor):**  
  * TRIG \-\> GPIO\_PIN\_17  
  * ECHO \-\> GPIO\_PIN\_16  
  * VCC \-\> 3.3V  
  * GND \-\> GND  
* **Relay Module:**  
  * IN \-\> GPIO\_PIN\_13  
  * VCC \-\> 3.3V  
  * GND \-\> GND  
* **Relay (High Voltage Side):**  
  * Connect the COM and NO (Normally Open) terminals in series with your light bulb's AC power line.  
  * **WARNING:** Be extremely careful when working with mains voltage (110V-230V). Ensure all high-voltage connections are properly insulated. If you are unsure, please consult a qualified electrician.

### **5\. Upload and Run**

1. Connect your ESP32 to your computer.  
2. In the Arduino IDE, select the correct board (e.g., "ESP32 Dev Module") and Port.  
3. Click "Upload" to flash the code to the ESP32.  
4. Open the Serial Monitor (Baud Rate: 115200\) to check for Wi-Fi and Blynk connection status.  
5. Once connected, the OLED display should initialize, and you can test the sensors.

## **License**

This project is open-source. Feel free to modify and use the code. Please provide attribution if you share it.
