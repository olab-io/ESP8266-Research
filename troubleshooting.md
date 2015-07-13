# Troubleshooting

The ESP8266 can be a difficult device to work with. For that reason I have created a few notes explaining common issues I have had while working with them. Other refererences that may be more helpful than this document is the [ESP8266 community forum](http://esp8266.com) and the [ESP8266 Arduino Issues](https://github.com/esp8266/Arduino/issues) page. When developing with the ESP8266 chip it is often a good idea to have multiple chips in case one fails. 
## `warning: espcomm_sync failed` or `error: espcomm_open failed` (Arduino)

Your Arduino IDE can't establish a connection with you ESP device. This is quite normal and often occurs even if your setup is correct. Try cycling the power (turn the ESP8266 off by disconnecting GND or VCC) and then uploading again. I've regularly had to do this 3-4 times per upload (ugh). If that doesn't seem to work make sure that:

### Arduino Settings

- You are using the `esptool` programmer: `Tools->Programmer->esptool`
- You are using the `Generic ESP8266 Board`: `Tools->Board->Generic ESP8266 Board`
- You have the correct serial port selected in `Tools->Port`

### Hardware Connections
- The ESP8266 VCC pin is connected to 3.3v of power and that power source can provide at least 215mA of current (Note: Arduino's DC 3.3v pin can only provide 200mA and cannot be used reliably).
- GPI0 1 is connected to GND when attempting to upload the program from the Arduino IDE.
- CH_PD is connected to 3.3v power source.
- RST pin is floating (not connected to GND or VCC).

### If using an Arduino board as a serial interface

- The USB from your computer is attatched to the USB port on your Arduino.
- There is a connection between Arduino GND and Arduino RESET pins (this is used to put the arduino into "serial forward mode").
- Arduino GND is connected to the ESP8266's GND as well as the GND of the power source.
- Arduino TX is connected to ESP8266's TX (not RX)
- Arduino RX is connected to ESP8266's RX (not TX)

## I can upload to my ESP8266 but the code doesn't seem to be running correctly/constantly resets (Arduino)

Runtime errors seem to cause the ESP8266 to reset and output no information as to what went wrong. This can make debugging very challenging as a runtime error will not be caught at compile time or when you verify your code from the Arduino IDE. In order to check if your ESP8266 is resetting because of an error you must:

1. Upload code to your ESP8266 via the ESP8266 Arduino IDE
2. Power your ESP8266 off
3. Remove `GPIO 1` from GND
4. Power your ESP8266 on
5. Open the serial monitor on your Arduino IDE with a baud setting of 115200 (use this setting even if you specified a serial connection with a different speed in your Arduino code because this is the default bitrate that the ESP8266 communicates its default messages at).

## `error: cannot access /dev/ttyACM0` (Linux)

First make sure that your USB cable is attatched to your computer ;)

I'm not sure if this is a linux only issue, but if you recieve the error message `cannot access /dev/ttyXXXX` then your user doesn't have the proper permissions to access your USB device. Open a terminal an run the following command:

```
sudo usermod -a -G dialout <username>
```

replacing `<username>` with your username. You will be prompted for your password to continue. Once the command has been run successfully you must restart (or perhaps a simple logout would do) your computer for the changes to take affect.