# esp8266_ir_blaster

This repository contains all software neccessary to control devices that
are controlled with an infrared remote via WiFi.

The ESP8266 programm runs a webserver providing a service to flash an IR LED
with given timings. This is often called an IR Blaster.
It also provides a call to analyze the signal emitted by a remote control of a device
by recording the signal from a IR sensor.

See a demo: [https://youtu.be/YuApbm1sk8Q]

# Installation

Setup up your environment to compile programs for the esp8266 with the Arduino IDE:
[https://github.com/espressif/arduino-esp32]

In the Arduino IDE, install the library *IRremoteESP8266* (Menu: Sketch -> Manage Libraries).

Copy the `config.h.example` file and name this copy `config.h`.
You must at least specify *SSID* and *Password* of your WiFi in the `config.h` to be 
able to connect to the Webserver.

The Arduino Sketch should now be able to be compiled and uploaded.

Connect the sensor and the (transistor amplified) IR LED to the pins specified in the
`config.h`.

If everything worked and you run the chip connected to the Arduino IDE you should be displayed the IP-address the server is running on in the serial monitor.

# Usage

The Webserver expects a `POST` Http-Request to the `/play` Route with a parameter `timings`. This parameter is supposed to contain a commaseparated string of integers specifying the signal to send with the IR LED.

This way the singals are not actually stored in the program, which has the advantage of not having to recompile each time we want to add a signal.

Via the `/record`-Route the server will give the timings for a signal from a remote control pointed at the sensor.

The most convienent way to make Http-Request is to use the Browser.
The `ir.html` file produces a simple and convient to make those requests.
But a few alterations are neccessary to adapt the setup to your environment.

Open the file in a text editor and search for a line looking like this:

```
 var host = '192.168.2.121';
```

That's the line telling the browser on how to reach the web server.
Change the value between the ticks to match the address the web server printed
on the serial monitor after starting.

After that you can open the file in a browser by navigating to it on your disk through the 'File'-Menu of your browser.

### Recording

1. Press the Record Button
2. Point the remote control at the sensor and press the desired button on the remote

After that under the Record button a long list of numbers should occur.
Those are the timings of the signal for that button you pressed on the remote.

### Setting up Commands

Now we want a way to send a signal with those timings from the chip itself.
For that we have to add the timings we just recorded to the list of known signals.
Copy the values (including the square brackets) that the recording process gave
 you and insert a new line with a name and the values in the `ir.html` file after the line looking like this:

```
  var signals = {
    "pgr1 btn": [1,2,3],
```
Replace the `1,2,3` with the recorded values and dont forget the comma after the surrounding brackets.

To actually add a button we also have to add a `command`. A command can consist of multiple *signals* since we may have to 'press' multiple buttons on the remote, i.e. *1* and *2* to reach channel 12 on the TV.
This also allows to control multiple device with one command.

Look for the line looking like this

```
  "Sound on/off": ["mute"],
```
Copy that line and change the text `"Sound on/off" to match what you want to display on the new button.
Change the name `"mute"` to the name of the `signal` you added. So the result could be:

```
  "HBO": ["prg 1 btn"],
```

If you want to execute multiple signals add them like this:

```
  "HBO": ["prg 1 btn", "prg 2 btn"],
```




