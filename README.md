# Elevate Your Tuya Device Monitoring Experience
[versione italiana](https://github.com/msillano/TuyaUIweb/blob/main/LEGGIMI.md)

This complete and dynamic solution organizes all your devices, rooms, and homes in an intuitive tree structure within a web browser. Each node in the tree has an informative tooltip with real-time updated values of the device's properties, giving you a complete and immediate overview of your Tuya ecosystem.

To ensure maximum security, **TuyaUIweb** operates exclusively in read-only mode, without making any changes to your data on Tuya Cloud.

Developed using `vis-network` and the `TuyaCloud v2` API, **TuyaUIweb** represents a significant step forward in monitoring and managing your Tuya devices.

### Features:
 **At-a-glance overview of the entire situation.** <br>
In the initial phase, all necessary data is read from Tuya Cloud and a local structure is built containing ALL the information. The visualization tree graph is built based on this information.

**Real-time updates.** <br>
Data updates are polled: they are read from the `TuyaCloud` with a frequency that can be set by the user, from 30 seconds to a few minutes, and the popups are updated immediately.

**Logging and data export.** <br>
It is possible to export some data to a file: the user must specify `home`, `device`, and `status` (properties) to identify the data of interest and these are saved at regular intervals (minimum 1 minute) in an internal buffer (max 5000 records = 80h  @1rec/min), then automatically exported to a file or on user command.

**Customizable look and feel.**
The program is OpenSource, in HTML+Javascript, is fairly well documented and modular. Therefore, any intervention is possible, allowing you to adapt the program to individual needs.

![](https://github.com/msillano/TuyaUIweb/blob/main/pics/UIlook01.png?raw=true)


### Implementation notes

- TuyaUIweb derives from a similar interface designed for [TuyaDAEMON](https://github.com/msillano/tuyaDEAMON-applications/tree/main/daemon.visUI.widget).
- The choice of the visualization library fell on [Vis-Network](https://visjs.github.io/vis-network/docs/network/) for its good flexibility combined with ease of use.
- In the tooltips, by default, all the properties included in the 'status' of the device are presented, with the names and values used by Tuya Cloud. Some values can be encoded.
- The first problem is the CORS security protocol, implemented by modern browsers. An application (even in js, node-red, etc) does not have this problem, but an APP that runs in a browser does. It is necessary to disable CORS when launching the browser - tested on Chrome Version 125.0.6422.61 (64 bit):<br>
   `chrome.exe --user-data-dir="C://Chrome dev session" --disable-web-security`<br>
 (see `goTuyaUI.bat` file). It applies only to this instance, the others will remain protected.

- Tuya places limits on the frequency of cloud access. **TuyaUIweb** takes this into account, and the initial phase (when it reads all the data from the Cloud) is blocking and not very short. As also in SmartLife.
- To overcome the impossibility of creating files directly from an HTML page, again for security reasons, to export the data I used a file logging library [debugout.js](https://github.com/ inorganicik/debugout.js). For this reason, the control over the generated files is not complete, and small manual interventions are necessary.
- The files are saved in the `download` dir, with the fixed name `tuyalog.cvs|json`, _make sure the OS. do not overwrite files with the same name!_
- Operation continues normally even with the browser window iconized.

### Safety NOTE

_**This APP is totally open, without any protection, and contains your credentials in clear text in the files!**_ <br>
_DO NOT make it accessible from the outside or by third parties, otherwise, all your data, including Tuya credentials, are exposed!_
<hr>

## Installation
1) Download and unzip the `TuyaUIweb.1.x.zip` file to a directory (with the permissions required by the operating system). 
2) Perform the configuration operations.
3) The main file is `tuyaui.html`. A WEB server is not required: as the code is all in JavaScript, TuyaUIweb is executed by the browser. To launch it, see the `goTuyaUI.bat` file (for Windows). For other operating systems, create a similar script.<br>
note: Ignore the Chrome message: "You are using an unsupported command line flag: - disable-web-security...": unsupported but working.
4) During installation and setup, the console is useful (in the browser - developer tools -, or menu 'inspect element') because the information and error messages of TuyaUIweb go there.
On the left startup OK (Chrome, CORS disabled) on the right CORS error case (Opera):


   <img src="https://github.com/msillano/TuyaUIweb/blob/main/pics/console_use01.png?raw=true" alt="normal start" width="300" />
   <img src="https://github.com/msillano/TuyaUIweb/blob/main/pics/CORS_err01.png?raw=true" alt="CORS error" width="300" align="right" />

<hr>

### Configuration
This APP is for experienced users, therefore it is acceptable that the configuration is done directly by editing a file (`config.js`).

 - The INDISPENSABLE data to be entered are your own Tuya credentials (you should already have them, and for new users, there are many guides on the web. [This one](https://github.com/iRayanKhan/homebridge-tuya/wiki/Get-Local-Keys-for-your-devices) is one of the clearest, others are [listed here](https://github.com/msillano/tuyaDAEMON/wiki/50.-Howto:-add-a-new-device-to-tuyaDAEMON#1-preconditions).
 - Other options concern: timing (Cloud and log) and log configuration: the format, autosave, the required values, or the look & feel, such as the presence of pan/zoom buttons.
 - Update the goTuyaUI.bat launcher file with the paths of the host system.

### Customizations
Two areas have been highlighted and related functions are placed in a separate file for user editing simplicity, with detailed instructions and examples:

**Custom icons**<br>
Tuya no longer allows you to change the icons, due to a questionable interpretation of its current copyright laws by its legal advisors. In TuyaUIweb I have chosen the 'awesome4' icons, with a wide choice and free to use. To customize them, the user must provide a selection criterion and an indication of the icon to use.
_By default, for example, they have special icons: Thermometers (a device with the name 'Temp...'), Thermostatic Valves (a device with the name 'Termo...'), and Gateways (a device with 'Gateway' in the name) - see images._

**Customization of tooltip content**<br>
Depending on the device, not all properties in Tuya status are useful. Some values are encrypted: you can choose not to show them, in other cases you need to divide by 10 or 100 to have the value in SI units, etc. If you want, you can add new information, e.g. by deriving it from the device's data (e.g. temperature in °C and also in °F).
<hr>

### CSV format
This is an example of a log file in CVS format:
```
[date, time, ROMA.TF_frigo.va_temperature, ROMA.Temperatura studio.va_temperature]
[2024-05-17, 06:35:28, 71, 22]
[2024-05-17, 06:37:28, 71, 22]
... more ...
```
The first row contains the column headers, the following rows contain the data.
The operations to do are the following (in an ASCII editor, for example, Notepad++, with 'global find&replace'):
1) Remove the square bracket '[' at the beginning of each line.
2) Replace the final square bracket with a semicolon ';'.
   
The correct CVS result is the following, which can be imported into many DBs and spreadsheets:
```
date, time, ROMA.TF_frigo.va_temperature, ROMA.Temperatura studio.va_temperature;
2024-05-17, 06:35:28, 71, 22;
2024-05-17, 06:37:28, 71, 22;
... more ...
```
### JSON format
This is an example of a log file in JSON format:
```
[{"home":"ROMA","device":"TF_frigo","status":"va_temperature","result":70,"day":"2024-05-17","time":"19:37:51"},
 {"home":"ROMA","device":"Temperatura studio","status":"va_temperature","result":25,"day":"2024-05-17","time":"19:37:51"}],
[{"home":"ROMA","device":"TF_frigo","status":"va_temperature","result":70,"day":"2024-05-17","time":"19:39:51"},
 {"home":"ROMA","device":"Temperatura studio","status":"va_temperature","result":25,"day":"2024-05-17","time":"19:39:51"}],
```
Note that all identifying data is added to each measurement, resulting in a more verbose result than the CVS case.
The operations to do are the following (in an ASCII editor, for example Notepad++):
1) Add a pair of square brackets '[]' to enclose all content.
   
The correct JSON result is the following, which can be used with JSON parsers to recreate the objects:
```
[
[{"home":"ROMA","device":"TF_frigo","status":"va_temperature","result":70,"day":"2024-05-17","time":"19:37:51"},
 {"home":"ROMA","device":"Temperatura studio","status":"va_temperature","result":25,"day":"2024-05-17","time":"19:37:51"}],
[{"home":"ROMA","device":"TF_frigo","status":"va_temperature","result":70,"day":"2024-05-17","time":"19:39:51"},
 {"home":"ROMA","device":"Temperatura studio","status":"va_temperature","result":25,"day":"2024-05-17","time":"19:39:51"}],
]
```
It is an array of arrays containing the individual measurements (objects).

<hr>
MIT License

_This project is a work-in-progress: it is provided "as is", without warranties of any kind, express or implied._

- _If you develop any interesting extension or application with TuyaUIweb let me know: we can insert it here._
- _For problems regarding the code and operation of TuyaUIweb, open an 'issue' here ([githun](https://github.com/msillano/TuyaUIweb/issues))._
- _For more general questions regarding Tuya, SmartLife (Tuya smart), and TuyaUIweb, which may also interest other users, please post in the group [Tuya and Smart Life Italia](https://www.facebook.com/groups/tuyaitalia)_

Thank you for your interest in TuyaUIweb <br>
m.s.

<hr>

### Acknowledgments

 - https://visjs.github.io/vis-network/docs/network
 - https://fontawesome.com/v4/icons/
 - https://code.google.com/archive/p/crypto-js/
 - https://github.com/inorganik/debugout.js 





