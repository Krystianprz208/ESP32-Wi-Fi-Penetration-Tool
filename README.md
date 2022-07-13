# ESP32 Wi-Fi Penetration Tool

This project introduces a universal tool for the ESP32 platform for implementing various Wi-Fi attacks. It provides some common functionality that is commonly used in Wi-Fi attacks and makes implementing new attacks a bit simpler. It also includes Wi-Fi attacks itself like capturing PMKIDs from handshakes, or handshakes themselves by different methods like starting rogue duplicated AP or sending deauthentication frames directly, etc...

Obviously cracking is not part of this project, as the ESP32 is not sufficient to crack hashes in effective way. The rest can be done on a small, cheap, low-power SoC.

<p align="center">
    <img src="doc/images/logo.png" alt="Logo">
</p>

## Features
- **PMKID capture**
- **WPA/WPA2 handshake capture** and parsing
- **Deauthentication attacks** using various methods
- **Denial of Service attacks**
- Formatting captured traffic into **PCAP format**
- Parsing captured handshakes into **HCCAPX file** ready to be cracked by Hashcat
- Passive handshake sniffing
- Easily extensible framework for new attacks implementations
- Management AP for easy configuration on the go using smartphone for example
- And more...

# Usage
1. [Build](#Build) and [Flash](#Flashing) project onto an ESP32 (DevKit or module)
1. Reset and power the ESP32 module
1. The management AP will be started automatically after boot
1. Connect to this AP\
**Default Configuration:**\
***SSID:*** `ManagementAP` and the ***password**:* `mgmtadmin`
1. In a web browser navigate to `192.168.4.1` and you should see a web client to configure and control tool like this:
    ![Web client UI](doc/images/ui-config.png)

# Build
- This project is currently developed using **ESP-IDF 4.1** (commit `5ef1b390026270503634ac3ec9f1ec2e364e23b2`). 
- This project has been tested with **ESP-IDF 4.4**
- ***Warning: It may be broken on a newer version of ESP-IDF.***

1) You must have [`ESP-IDF`](https://docs.espressif.com/projects/esp-idf/en/stable/esp32s2/get-started/index.html#step-1-install-prerequisites) to build this project
2) If you're building for the `Flipper Zero Wifi Dev Board` you will need to first set the chip target. Otherwise skip this step and move to Step 3.
```
idf.py set-target esp32s2
```
3) You can build the project by navigating to the project directory and running:
```shell
idf.py build
```

The legacy method using `make` is not supported by this project.

# Flashing

## Prebuilt Binaries
The easiest method of flashing is using the pre-built binaries included in the release section
1) Download the appropriate release for your OS from the latest releases
2) Put you ESP32 into download mode by holding the **BOOT** button while plugging it into the PC. 
3) Run the included flashing script within the ZIP file for **Linux** this is `Flash.sh` for **Windows** this is `Flash.bat`
4) Continue to Step 2 in [Usage](#Usage) for further instructions on how to use the **ESP32 Wi-Fi Penetration Tool**

## Manual Methods of Flashing 
- The below methods really aren't neccesary. With the included flashing scripts in the release section you can easily flash your own builds;however the instructions are here if you want to follow them. :) 

### ESP-IDF 
- If you have setup [`ESP-IDF`](https://docs.espressif.com/projects/esp-idf/en/stable/esp32s2/get-started/index.html#step-1-install-prerequisites), the easiest method of manual flashing is running `idf.py -p <PORT> flash` from the project directory replace `<PORT>` with the serial port your ESP32 is connected to.

1) Put you ESP32 into download mode by holding the **BOOT** button while plugging it into the PC
2) Run the following command
```
idf.py -p <PORT> flash
```

### Windows
1) Download [`esptool`](https://github.com/espressif/esptool)
2) Put you ESP32 into download mode by holding the BOOT button while plugging it into the PC
3) You can flash the project with the following command replacing `COM4` with the serial port your ESP32 is connected to. 
```python
esptool.exe -p COM4 -b 115200 --after hard_reset write_flash --flash_mode dio --flash_freq 40m --flash_size detect 0x8000 build/partition_table/partition-table.bin 0x1000 build/bootloader/bootloader.bin 0x10000 build/esp32-wifi-penetration-tool.bin
```

### Linux or MAC OS
1) Download [`esptool`](https://github.com/espressif/esptool)
3) Put you ESP32 into download mode by holding the BOOT button while plugging it into the PC
3) You can flash the project with the following command replacing `ttyS5` with the serial port your ESP32 is connected to. 
```python
esptool.py -p /dev/ttyS5 -b 115200 --after hard_reset write_flash --flash_mode dio --flash_freq 40m --flash_size detect 0x8000 build/partition_table/partition-table.bin 0x1000 build/bootloader/bootloader.bin 0x10000 build/esp32-wifi-penetration-tool.bin
```

# Documentation
### Wi-Fi attacks
Attack implementations in this project are described in the [main component README](main/). The theory behind these attacks is located in [doc/ATTACKS_THEORY.md](doc/ATTACKS_THEORY.md)

### API Reference Generation
This project uses Doxygen notation for documenting components API and implementation. Doxyfile is included so if you want to generate an API reference, you can run `doxygen` from the root of the project directory. It will generate HTML API references into `doc/api/html`.

### Components
This project consists of multiple components, that can be reused in other projects. Each component has it's own README with a detailed description. Here comes a brief description of the components:

- [**Main**](main) component is an entry point for this project. All neccessary initialisation steps are done here. Management AP is started and the control is handed to the webserver.
- [**Wifi Controller**](components/wifi_controller) component wraps all Wi-Fi related operations. It's used to start the AP, connect as STA, scan nearby APs, etc...
- [**Webserver**](components/webserver) component provides the web UI to configure attacks. It expects that the AP is started and no additional security features like SSL encryption are enabled.
- [**Wi-Fi Stack Libraries Bypasser**](components/wsl_bypasser) component bypasses Wi-Fi Stack Libraries restrictions to send some types of arbitrary 802.11 frames.
- [**Frame Analyzer**](components/frame_analyzer) component processes captured frames and provides parsing functionality to other components.
- [**PCAP Serializer**](components/pcap_serializer) component serializes captured frames into PCAP binary format and provides it to other components (mostly for the webserver/UI)
- [**HCCAPX Serializer**](components/hccapx_serializer) component serializes captured frames into HCCAPX binary format and provides it to other components (mostly for the webserver/UI)

### Power consumption
- Based on experimental measurements, the ESP32 consumes around 100mA during attack executions. 

## Contributing
- Feel free to contribute. Don't hestitate to refactor current code base. Please stick to Doxygen notation when commenting new functions and files. This project is mainly build for educational and demonstration purposes, so verbose documentation is welcome.

## Disclaimer
- This project demonstrates vulnerabilities of Wi-Fi networks and its underlaying 802.11 standard and how ESP32 platform can be utilised to attack on those vulnerable spots. Use responsibly against networks you have permission to attack on.
 
