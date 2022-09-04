# FunkMan
 FunkMan is a python program that creates an easy-to-use interface between the DCS scripting environment and a Discord bot via a UDP socket connection.
 This allows you, *e.g.*, to send text messages from DCS to Discord channels.
 
 Furthermore, FunkMan contains special interfaces to the MOOSE classes AIRBOSS and RANGE. For the AIRBOSS class, you get embeded messages when a player receives an LSO
 grade and fancy images of the trap sheet. For the RANGE class, images of the bombing impact point are send to Discord as well as as summary of a strafing run.

## Prerequisite
Before you install FunkMan you need to know which other software is required for FunkMan to work. As FunkMan is a python program, it obviously needs python installed and some common libraries.

### Python
You can get python from [python.org](https://www.python.org/). During the installation, you should also install `pip` when asked and add the install directory to the windows `PATH` environment variable.

The additional libraries can be installed with pip. These are:
- [discord.py](https://discordpy.readthedocs.io/en/stable/): `pip install discord`
- [matplotlib](https://matplotlib.org/): `pip install matplotlib`
- [numpy](https://numpy.org/): `pip install numpy`

### MOOSE
You need the `Moose.lua` file from the MOOSE [develop branch](https://github.com/FlightControl-Master/MOOSE_INCLUDE/tree/develop/Moose_Include_Static).

### Discord Bot
Finally, you need to create a discord bot. There are plenty of youtube videos around, which explain how to do this.
Go to the http://discord.com/developers site to do this.


## Installation
There are several ways to obtain the FunkMan code. You can use `git clone` if you are familiar with git
or download the [latest release](https://github.com/funkyfranky/FunkMan/releases) as zip file and unzip
it to a location of your liking, e.g. `D:\FunkMan\`.

The FunkMan directory contains three important files:
- `FunkMan.ini`: This is the config file, where you specify important parameters of your bot and socket connection.
- `FunkMan.py`: This is the file that contains the few lines of python code needed to start FunkMan. Usually, no need to change this.
- `FunkMan.bat`: This is the file you can click to start FunkMan by running the above python file.

So after downloading the FunkMan code, you first need to adjust the `FunkMan.ini` file. The important parameters
are the Token of your Discord bot and the ID of Discord channel(s), where FunkMan will send messages and images to.

## Config File
The config file consists of multiple section each starting with square brackets.

### [DEFAULT]
This sections contains a parameter for debugging:
- `DEBUGLEVEL`: Higher numbers lead to more output. Default is 0.

### [FUNKBOT]
This section contains information about the Discord bot parmeters:
- `TOKEN`: Is the secret (!) token of the bot.
- `CHANNELID_MAIN`: The main channel ID used for all messages by default.
- `CHANNELID_RANGE`: (Optional) Channel ID where RANGE messages are send to.
- `CHANNELID_AIRBOSS`: (Optional) Channel ID where AIRBOSS messages are send to.

### [FUNKSOCK]
This section contains information about the UDP socket:
- `PORT`: UDP Port. Default is 10042.
- `HOST`: Host name. Default is 127.0.0.1.

### [FUNKPLOT]
This section contains information for plotting images.
- `IMAGEPATH`: Directory where the necessary images are stored. Default is `./funkpics/`.
By default, the image directory is supposed to be in the same directory as main `FunkMan` files.

### Example
```
[DEFAULT]
DEBUGLEVEL=0

[FUNKBOT]
TOKEN=MTAwNTAwMDA0NDc4MjU2MzQ0MQ.GmUqVd.cMRDGb6JxFMpN3cAAU-DX2Z4SymgTJLAwL-C8I
CHANNELID_MAIN=1011372894162526329
CHANNELID_RANGE=1006216842509041786
CHANNELID_AIRBOSS=1011372920968323155

[FUNKSOCK]
PORT=10042
HOST=127.0.0.1

[FUNKPLOT]
IMAGEPATH=./funkpics/
```

## Usage
Once you have configured FunkMan with the ini file, FunkMan is started by simply clicking the `FunkMan.bat` file.
If everything is setup correctly, you will obtain the following output telling you that you bot has connected:

**INSERT PICTURE HERE**

### Text Messages
Sending simple text messages from the DCS scripting environment is pretty easy.
In your lua script you need to create a new [SOCKET](https://flightcontrol-master.github.io/MOOSE_DOCS_DEVELOP/Documentation/Utilities.Socket.html) object:
```
mySocket=SOCKET:New()
mySocket:SendText("Red Helicopter in Southern Zone!")
```
This works as long as you use the default port, which is `10042`. If you changed that port in the `FunkMan.ini` file,
you need to pass that as parameter in the [:New](https://flightcontrol-master.github.io/MOOSE_DOCS_DEVELOP/Documentation/Utilities.Socket.html##(SOCKET).New) function, *e.g.* `mySocket=SOCKET:New(10081)`.

### Airboss
Sending LSO grades and trapsheets from the [AIRBOSS](https://flightcontrol-master.github.io/MOOSE_DOCS_DEVELOP/Documentation/Ops.Airboss.html) class is as simple as adding the command
`myAirboss:SetFunkManOn()`.

So for example:
```
local myAirboss=AIRBOSS:New("USS Stennis", "Stennis")
myAirboss:SetFunkManOn()
-- More Config stuff here...
myAirboss:Start()
```

Note that the default port `10042` is used here. If you want to change it, you have to pass it as parameter to the [SetFunkManOn](https://flightcontrol-master.github.io/MOOSE_DOCS_DEVELOP/Documentation/Ops.Airboss.html##(AIRBOSS).SetFunkManOn) function.

### Range
Sending bombing and strafing results from the [RANGE](https://flightcontrol-master.github.io/MOOSE_DOCS_DEVELOP/Documentation/Functional.Range.html) class is done by adding the command
`myRange:SetFunkManOn()`.

So for example:
```
local myRange=RANGE:New("Goldwater Range")
myRange:SetFunkManOn()
-- More Config stuff here...
myRange:Start()
```

Note that the default port `10042` is used here. If you want to change it, you have to pass it as parameter to the [SetFunkManOn](https://flightcontrol-master.github.io/MOOSE_DOCS_DEVELOP/Documentation/Functional.Range.html##(RANGE).SetFunkManOn) function.

## Subpackages
FunkMan contains three subpackages:
- `FunkSock`: A socket server that handles data received via an UDP connection
- `FunkBot`: An interface to Discord bots using discord.py.
- `FunkPlot`: Contains a class to make pretty figures for the `MOOSE` `RANGE` and `AIRBOSS`classes using matplotlib.

You can use each of these subpackges on its own or in combination with the `FunkMan` package.
