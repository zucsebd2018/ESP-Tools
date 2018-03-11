Tutorial
====

Prerequisite
----
Before you follow this tutorial, make sure that you have already been familiar with
* how to connect your ESP8266 to your computer,
* how to flash MicroPython to your ESP8266,
* how to use terminal to interact with your MicroPython,
* and how to set your ESP8266 as an AP,

all of which you can find corresponding instructions in my another [solution](https://github.com/zucsebd2018/ESP-Tools/issues/6#issuecomment-372091256).

Download an existing tool
----
> We are not expected to reinvent the wheel.

As the saying goes, it is not desirable to duplicate a basic method that has already previously been created or optimized by others. There has already been an existing tool, so let's feel free to use it directly.

The tool, named ***mpfshell***, is a simple file shell for MicroPython based devices like ESP8266s. With the aid of it, you can **up/down load file to your ESP8266, as well as enter the REPL to execute your statements and files written in Python**.

To start with, download or clone the [repository](https://github.com/wendlers/mpfshell) from Github. In fact, a complete guidance to asist you with the following steps already lies in its [Readme.md](https://github.com/wendlers/mpfshell/blob/master/README.md). Hence, only the necessary steps to resolve the issue are addressed here.

After downloading or cloning, enter the root directory of the local repository, and then open command line there. Using
```
pip install -r requirements.txt
```
or 
```
pip3 install -r requirements.txt
```
to ensure you have the related dependencies for mpfshell installed for Python 2 or Python 3, respectively.

The rest of the solution will mainly focus on the environment of Python 2. Things in Python 3 are similar.

Connect to your ESP8266 in mpfshell
----
In *nix, you can directly run the executable file in the root directory of *mpfshell*. 
```
sudo chmod a+x ./mpfshell (if necessary)
./mpfshell
```
After entering the shell, in order to connect to your ESP8266 via WebSocket, remember the following command.
```
open ws:[IP address of your chip],[password]
```
A successful connection looks like this
<p><img src="https://github.com/SamuelGong/Embedded-System-Experiments/raw/master/Week%201/WebREPL_tools_for_CMD-Windows/image/mpfshell5.png" width="80%"  /></p>

Commands supported in mpfshell
----
Congratulations! Now you can readily manage the file system and enter the WebREPL of MicroPython in your ESP8266 in this shell.
All the commands supported is shown as follows.

Command | Function
-|-
ls | list remote files
pwd | print the current remote directory
cd [target directory] | change current remote directory to given target
md [target directory] | create new remote directory
lls | list files in current local directory
lcd [target directory] | change current local directory to given target
lpwd | print current local directory
put [local file] ([remote file]) | upload local file. If the second parameter is given, its value is used for the remote file name. Otherwise the remote file will be the same as the local file
mput [selection regex] | upload all local files that match the given regular expression. The remote files will be named the same as the local files. p.s. It does not get directories, and it is not recursive
get [remote file] ([local file]) | download local file. If the second parameter is given, its value is used for the local file name. Otherwise the local file will be the same as the remote file
mget [selection regex] | download all remote files that match the given regular expression. The local files will be named the same as the remote files. p.s. It does not get directories, and it is not recursive
rm [remote file or directory] | delete a remote file or directory
mrm [selection regex] | delete all remote files that match the given regular expression. p.s. It does not delete directories, and it is not recursive.
cat [remote file] | print the contents of a remote file
exec [statement] | execute a Python statement on remote
repl | enter Micropython REPL
mpyc | compile a Python file into byte-code by using mpy-cross (which needs to be in the path). The compiled file has the same name as the original file but with extension '.mpy'

Details can be explored in its original [document](https://github.com/wendlers/mpfshell/blob/master/README.md).

Executing an uploaded Python script
----
Now you may appreciate the convenience of uploading to or downloading from your ESP8266 a file via this smart mpfshell. However, you can still get stuck in how to excute your uploading files, say, a Python script named *test.py* with content like this
```
a = 1
b = 2
print(a+b)
print('succeeded')
```
The solution I provide is trivial. Simply making use of the import features of Python.
```
import [your script name]
```
A complete example of uploading the Python 3 script *test.py* and executing it via mpfshell is given as follows.
```
put test.py
repl
import test
```
The result can be illustrated as follows.

<p><img src="https://github.com/SamuelGong/Embedded-System-Experiments/raw/master/Week%201/WebREPL_tools_for_CMD-Windows/image/mpfshell6.png" width="80%"  /></p>

More about WebSocket
----
The issue is mainly resolved, but I would like to introduce more about **WebSocket** to you.

To start with, the system most people refer to as 'The Internet' is actually a stack of layers of protocols, often referred to as the **TCP/IP stack** . Knowing these different layers can help us greater understand how your ESP8266 communicates with other devices on the network.

Layer | Protocols
-|-
Application | HTTP, FTP, mDNS, WebSocket ...
Transport | TCP, UDP
Network | IP
Link | Ethernet, Wi-Fi...

* **The link layer** Closest to the hardware. To connect your ESP8266 to a wireless network, you have to create a **Wi-Fi** link.
* **The network layer** After devices are physically connected, they can still not talk to each other yet, because they have no way of knowing where to send the message to. Here comes with the **Internet Protocol(IP)**. Under this protocal, every device on the network can have a unique IP address. (The uniqueness is ensured by the Dynamic Host Configuration Protocol (DHCP) server)
* **The transport layer** Now a device can deliver IP packets to another device via a IP address specified, there is a problem that a packet may get lost, since IP cannot guarantee that the packets will not be corrupted or misordered. Hence we need this layer. There are mainly two kinds of protocols, the **Transimssion Control Protocol (TCP)** and the **User Datagram Protocol**. The former can guarantee that the above requirements can be satisfied.
* **The application layer** There is still one problem if we don't have this layer - the receiver can receive the message perfectly but not recognize the information contained in it.
There are lots of protocols to deal with it, one of which is the famous **HyperText Transfer Protocpl (HTTP)** for web servers and clients to communicate with each other.

While HTTP is fit for downloading webpages, uploading photos etc, it is quite slow, since every time you send a HTTP request, you have to start a new TCP connection to the server, and then send the request, wait for the server to respond, and download the response.

Moreover, under HTTP protocol, the request can only be sent from client, which make it impossible for server to actively send something to the client. To achieve such kind of goal, client will have to poll for it.

**WebSocket** can get rid of these two disadvantages. To be exact, WebSocket resolves several issues with HTTP in general because of its following features:
* **Bi-directional**
* **Full-duplex**
* **Single TCP Connection**
* **Lean protocal**

You can see more [here](http://blog.arungupta.me/rest-vs-websocket-comparison-benchmarks/).
