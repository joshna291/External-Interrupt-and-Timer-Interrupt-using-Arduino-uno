# EXTERNAL INTERRUPT AND TIMER INTERRUPT USING ARDUINO UNO

### Aim
To implement External Interrupt and Timer Interrupt using an Arduino UNO and observe interrupt-driven execution.

# Hardware / Software Tools Required

- Arduino UNO Board
- USB Cable
- PC/Laptop with Arduino IDE Installed
- Breadboard
- Push Button
- LED
- Blynk IOT

# Circuit Diagram

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/02630f8d-4ddb-444b-9a3c-efb86fd38160" />


# Procedure

Step 1: Assemble the Setup
Place the Arduino UNO R4 WiFi on the workbench.
Connect the Arduino UNO R4 WiFi to the computer using a USB cable.
Use the built-in LED (L) of the Arduino board; no external LED, resistor, push button, or jumper wires are required.

Step 2: Configure Blynk IoT
Open the Blynk IoT application on the mobile phone.
Create/login to the Blynk account.
Create the required Blynk template and device.
Add a Button widget and assign it to Virtual Pin V0.
Configure the button as an ON/OFF switch.

Step 3: Configure the Timer Interrupt
Use the timer of the Arduino UNO R4 WiFi to generate a periodic interrupt.
Configure the timer using the FspTimer library.
Define the timer interrupt callback function.
Program the timer to periodically toggle the built-in LED.

Step 4: Open Arduino IDE
Open Arduino IDE.
Select Tools → Board → Arduino UNO R4 WiFi.
Select the correct COM Port.
Install the required Blynk library if it is not already installed.

Step 5: Write and Upload the Program
Enter the program for Blynk control and timer interrupt.
Enter the Blynk authentication token, Wi-Fi name, and password.
Verify/compile the program.
Upload the program to the Arduino UNO R4 WiFi using the USB cable.

Step 6: Execute the Program
Connect the Arduino UNO R4 WiFi to the configured Wi-Fi network.
Open the Blynk IoT application.
Initially, the built-in LED blinks continuously due to the timer interrupt.
Press ON in the Blynk application. The built-in LED becomes continuously ON.
Press OFF in the Blynk application. The LED returns to continuous blinking.

Step 7: Verify the Output
Confirm that the built-in LED blinks periodically due to the timer interrupt.
Confirm that pressing ON in Blynk makes the LED glow continuously.
Confirm that pressing OFF in Blynk returns the LED to its blinking state.
Observe the corresponding interrupt messages in the Serial Monitor, if included.
Record the observations.

# Program
```c
#define BLYNK_TEMPLATE_ID "TMPL3FC5OeIaF"
#define BLYNK_TEMPLATE_NAME "LED Control"
#define BLYNK_AUTH_TOKEN "YOUR_NEW_AUTH_TOKEN"

#define BLYNK_PRINT Serial

#include <WiFiS3.h>
#include <BlynkSimpleWifi.h>
#include "FspTimer.h"

char ssid[] = "YOUR_WIFI_NAME";
char pass[] = "YOUR_WIFI_PASSWORD";

FspTimer timer;

volatile bool timerFlag = false;
volatile bool forceON = false;

void timerISR(timer_callback_args_t *arg)
{
  timerFlag = true;
}

BLYNK_WRITE(V0)
{
  forceON = param.asInt();

  if (forceON)
  {
    digitalWrite(LED_BUILTIN, HIGH);
    Serial.println("Blynk ON - LED continuously ON");
  }
  else
  {
    Serial.println("Blynk OFF - LED blinking");
  }
}

void setup()
{
  Serial.begin(115200);

  pinMode(LED_BUILTIN, OUTPUT);
  digitalWrite(LED_BUILTIN, LOW);

  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);

  timer.begin(TIMER_MODE_PERIODIC, GPT_TIMER, 6, 1.0, 0.0, timerISR);
  timer.setup_overflow_irq();
  timer.open();
  timer.start();

  Serial.println("Timer Interrupt Started");
}

void loop()
{
  Blynk.run();

  if (timerFlag)
  {
    timerFlag = false;

    if (!forceON)
    {
      digitalWrite(LED_BUILTIN, !digitalRead(LED_BUILTIN));
      Serial.println("Timer Interrupt - LED Blink");
    }
  }
}
```
# Output
<img width="1448" height="1086" alt="image" src="https://github.com/user-attachments/assets/d7aeb9fd-0439-447d-823a-7354b8ae6df5" />


# Result

The External Interrupt and Timer Interrupt were successfully implemented using the Arduino UNO. The external interrupt responded immediately to the push button event, while the timer interrupt executed periodically, demonstrating efficient interrupt-driven programming without continuous polling.
