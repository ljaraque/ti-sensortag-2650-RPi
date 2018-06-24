**Texas Instruments Sensortag 2650 and Raspberry Pi**  
---   
*title: TI-SensorTag2650&Rpi*  
*date: 2016-01-30*  
*by: ljaraque@yahoo.com*

#### **1. Introduction**  
This document is intended to provide a validated procedure to setup and run TI SensorTag 2650 along with Raspberry Pi.  
All steps are validated with real deployment over:  
**Raspbian Jessie Lite**  
Version: November 2015  
Release date: 2015-11-21  
Kernel version: 4.1  

Other repositories used through this document were cloned on 2016.01.30.  

#### **2. Activation of Bluetooh Low Energy (BLE) on Raspberry Pi**  

Follow **Reference_1** for installation of packages necessary to activate BLE on Rpi:  
Some libraries have to be updated due to obsolescence of some package names in this reference:  
Original: `sudo apt-get install bluez-utils libopenobex1 build-essential libglib2.0-dev libdbus-1-dev`  
Updated: `sudo apt-get install bluez libopenobex1 build-essential libglib2.0-dev libdbus-1-dev`  
(Amongst other things, this will install the hcitools necessary for next steps of this post).  

Then, to see the Bluetooth interface follow the `First Tests` section from **Reference_2**:  

To see the USB BLE interface run:  
`lsusb`

To see all details of the interface run:  
`hciconfig --all`

If the interface is down, set it up with:  
`sudo hciconfig hci0 up`

On-Air BLE devices (to get the name of the device of interest for example B0:B4:48:E0:42:15) can be explored with:  
`sudo hcitool lescan`

Thus, we have our BLE interface on Raspberry Pi up and running.  

#### **3. Installing the Python Interface to BLE devices.**   

Based on steps from **Referece_1** and Package from **Reference_3** also includes code for the particular case of TI SensorTag.  

Get and install **bluepy** library:  

    git clone https://github.com/IanHarvey/bluepy.git
    cd bluepy/bluepy
    make

On the same directory there will be a script called `sensortag.py`, which can be used from linux command line through:  

    sensortag.py [-h] [-n COUNT] [-t T] [-T] [-A] [-H] [-M] [-B] [-G] [-K]
    [--all]
    host

    positional arguments:
    host MAC of BT device

    optional arguments:
    -h, --help show this help message and exit
    -n COUNT Number of times to loop data
    -t T time between polling
    -T, --temperature
    -A, --accelerometer
    -H, --humidity
    -M, --magnetometer
    -B, --barometer
    -G, --gyroscope
    -K, --keypress
    --all

For example, to read the temperature five times every 0.5s it will be:  
`python sensortag.py B0:B4:48:E0:42:15 -n 5 -t 0.5 -T`  

At the same time, `sensortag.py` can be used as import into other specific scripts like the following example:  

    import time
    import sensortag

    tag = sensortag.SensorTag('B0:B4:48:E0:42:15')

    time.sleep(1.0)
    tag.IRtemperature.enable()
    for i in range(5):
    tag.waitForNotifications(1.0)
    print tag.IRtemperature.read()
    tag.disconnect()
    del tag

####  **4. Working with other sensors**  

From **Reference_4**, demo scripts for different sensors can be used from `python/TIFirmware`, for example:  
`python getIRTemp.py B0:B4:48:E0:42:15`
These scripts can be worked further to improve speed on getting values since they take considerable time to output the read values.  

#### **5. TI SensorTag 2650 Vendor Documentation**  

**Reference_5** is a Wiki for original SensorTag board (Only included here for reference).  
**Reference_6** is a Wiki including parameters, samplig rates, etc for sensors in SensorTag2650.  



#### **6. References**  
**Reference_1:** https://smidgeonpigeon.wordpress.com/2015/07/21/raspberry-pi-2-ble-ti-sensor-tag/  
**Reference_2:** http://www.elinux.org/RPi_Bluetooth_LE  
**Reference_3:** https://github.com/IanHarvey/bluepy  
**Reference_4:** https://github.com/digitalhack/dhSensorTag2015  
**Reference_5:** http://processors.wiki.ti.com/index.php/SensorTag_User_Guide  
**Reference_6:** http://processors.wiki.ti.com/index.php/CC2650_SensorTag_User's_Guide  
