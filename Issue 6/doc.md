Tutorial
====
Familiar with your chips
----
You may observe that there are two little silver bags in one of your tool box, each with a chip naming ***ESP-WROOM-32*** (ESP 32) and ***ESP8266MOD*** (ESP8266), respectively.

There are a few differences between them, according to our wise T.A. , but we can just ignore them in this issue. Hence, the rest of the tutorial will mainly focus on the issue of ESP8266, while the counterparts of ESP32 are intentionally left blank for your pleasure of exploration.

<p><img src="https://github.com/SamuelGong/Embedded-System-Experiments/raw/master/Week%201/flash_microPython-Windows/image/ESP8266.jpg" width="70%"  /></p>

The **ESP8266** is a notable microcontroller with Wi-Fi. It is produced by a Chinese manufacture, Espressif Systems. Documentation of ESPB8266 including its features, datasheet and design guideline is available [here](https://www.espressif.com/en/products/hardware/esp8266ex/overview).

Connect your chip to your computer
----

There are many fancy hardware resources on your ESP8266, but at the moment you only need to know that it has a **USB-UART port**, which allows for data transmittion between your computer's USB and the UART chip on your ESP8266.


**UART**, short for **Universal Asynchronous Receiver/Transimitter**, is a chip on which serial ports (also named 
communication(COM) ports) rely to transforms serial data to parallel one and back. More about UART and serial ports can be obtained [here](https://computer.howstuffworks.com/serial-port1.htm).

All you need to know is that you can communicate with your chip by a USB serial cable. You can simply find one like a **Micro USB to USB cable** of some mobile phones. Note that the one lying in your tool box doesn't work, for it is only for WRTnode development (quoted from our T.A. ).

After you connect your chip and your computer, you will see nothing especial except for the bule light on the chip twinkling for some time.
To examine whether your computer can recognize your chip, use the following command
```
ls /dev/
```
to see if there exists a device name 'ttyUSB0' or something like that. If not,
you will probably find that you need to install a **CP210x USB to UART Bridge Virtual COM Port(VCP) Driver**. Feel free to download it [here](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers).

After that, if you are not the root user of your Ubuntu, use this to change the access mode of your port.
```
sudo chmod 777 /dev/ttyUSB0
```


Flash MicroPython to your chip
----
There exists a Python-based, open source, platform independent, utility to communicate with the ROM bootloader in Espressif ESP8266 and ESP32 chips, named ***esptool***.
Simply use the following command to complete the installation.
```
sudo pip install esptool
```
Documentations about esptool can be found [here](https://github.com/espressif/esptool/). In brief, If you want to erase the entire flash chip (all data replaced with 0xFF bytes), you should use
```
esptool.py --port [your port name] erase_flash
```
and if you want to flash a .bin file to your chip, use
```
esptool.py --port [your port name] --baud 115200 write_flash --flash_size=detect 0 [relative path of your bin file]
```
.bin file for MicroPython can be found in its official [website](http://micropython.org/download#esp8266).
The following screenshot is taking my own trial as an example.
<p><img src="https://github.com/SamuelGong/Embedded-System-Experiments/raw/master/Week%201/flash_microPython-Windows/image/esptool1.png" width="70%"  /></p>

By the way, if you receive a error message like 'Command not found' when executing script *esptool.py*, you might have to modify the access mode of your *esptool.py*. If that is the case, use
```
pydoc esptool
```
to determine the file path of your *esptool.py*, and then use
```
sudo chmod a+x [file path of your esptool.py]
```
to add the access right of executing your *esptool.py*.

Use tools to interact with your MicroPython
----
After flashing a MicroPython to your ESP8266 successfully, you may wonder how you can use the embbedded Python interpreter. Many tools can help, like `screen, picocom, minicom` and so on. Taking ***screen*** as an example, you can use
```
sudo apt install screen
```
if you have not got an installed one. After installation, you can use
```
sudo screen /dev/ttyUSB0 115200
```
to interact with your on-chip MicroPython. It may appears like
<p><img src="https://github.com/SamuelGong/Embedded-System-Experiments/raw/master/Week%201/flash_microPython-Windows/image/screen1.png" width="70%"  /></p>

Set your chip as an AP
----

Now you are transfering data between your PC and ESP8266 microcontroller using serial ports. But remember that **WiFi** is supported on our fancy chip? Things can be better if you try to connect them in a wireless way.

Since that we have a Python interpreter with a subset of standard libraries, we can achieve this goal easily. Simple key in these code line by line.

```
>>> import network
>>> ap_if = network.WLAN(network.AP_IF)
>>> ap_if.active(True)
``` 

After you execute the above code, nothing explicitly happens. But if you check the near WiFi signal list, you may find a new WiFi hotspot named **MicroPython-08b0af** or something like that. You can surely connect your computer to that hotspot, using the password **micropythoN**.

For more to delve in, surf our T.A.'s [blog](http://zjubank.com/2017/05/16/ESP8266-MicroPython/).

Test your MicroPython using WebREPL
----
Now you can execute our Python commands using wireless data transfer method, namely without any use of screen. Before you take steps further, you should figure out why we can do that. Only because we can connect to its network?

Obviously not. In fact, all the convenience is owing to the **WebREPL** environment that MicroPython is equipped. A **REPL** , standing for **Real-Eval-Print-Loop**, is a interative computer programming environment that takes the incomming commands or expressions and exectues them piecewise. You may think of a command line shell. That's exact a common example for REPL.

Moreover, **WebREPL**  is for accessing MicroPython REPL over **WebSockets**. More information about WebREPL can be read [here](https://github.com/micropython/webrepl).

In order to use WebREPL, we still need the help of screen or other terminals. Typing the following code and press enter.
```
import webrepl_setup
```

Then you will be guided to set your password, which you will find its use later, so remember exactly what you have set. After that, WebPERL will be set as auto-start every time the microcontroller is booted. Finally toggle the *RST* switch on the chip to reset it.

Upload your python files
----

It is very convenient to use WebREPL, for there is a hosted online version at [http://micropython.org/webrepl/](http://micropython.org/webrepl/). If your computer has connected to your chip, simply click *Connect* and then you can use the online terminal as before.

<p><img src="https://github.com/SamuelGong/Embedded-System-Experiments/raw/master/Week%201/flash_microPython-Windows/image/webrepl.png" width="50%"  /></p>

Note that you can upload some files to your ESP8266 using the button at the bottom. Explore the detail yourself!
