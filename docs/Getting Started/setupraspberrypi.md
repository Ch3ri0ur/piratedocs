# Setup Raspberry Pi

1. **[Install Raspberry Pi OS](#install)**
    - Any version of Raspbian OS will probably work.
    - For max. performance a none Desktop version should be used.
2. **[Basic Configuration Raspberry Pi](#config)**
    - Change default Password
    - Activate SSH
    - Activate WiFi (optional)
    - Perform Update & upgrade
3. **[Setup Raspberry Pi for PirAtE](#setup)**
    - Activate RPi Camera Interface
    - Activate Serial Interface
    - Install Git
    - Install [Nodejs](../Pirate-Bridge/Theory/nodejs.md)
    - Install [Docker](../Pirate-Bridge/Theory/docker.md)

More detailed setup instructions for a Raspberry Pi Desktop can be found here:
https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up

## 1. Install Raspberry Pi OS on an SD Card<a id="install"></a>
The Installation can be done in two ways.

    - Using the official Installer from Raspberry Pi
    - Using another Imager and download the Image manually

Both methods are shown on the Raspberry Pi Homepage
https://www.raspberrypi.org/documentation/installation/installing-images/README.md


1. Using Downloader and Installer from Raspberry Pi
    Raspberry Pi Imager can be downloaded from the Raspberry Homepage.
    https://www.raspberrypi.org/downloads/ \
    After Starting the Imager it will let you select different Images
        - Select "Raspberry Pi OS (32-bit) Lite"
        - if you already got an image on your Computer you can select "Custom"


2. Download Image from Raspberry Pi homepage and using an Imager to install it
    1. Download Image
        - "Raspberry Pi OS (32-bit) Lite" from https://www.raspberrypi.org/downloads/raspberry-pi-os/
    2. Flash OS on SD Card
        - https://www.balena.io/etcher/ 
        - or https://win32diskimager.download/



## 2. Basic Configuration Raspberry Pi <a id="config"></a>
**For complete remote Access, the SSH and WiFi need to be activated before starting**<a id="headlessSSH"></a>
https://www.raspberrypi.org/documentation/configuration/wireless/headless.md 

For the Full Headless mode (no attached Screen) the SSH (and WiFi) need to be Setup before the First boot

Enable SSH:

- Put file with name "ssh" without extension in root folder of boot partition
    https://www.elektronik-kompendium.de/sites/raspberry-pi/1906281.htm


Enable Wifi otherwise use Ethernet connection:

- Put "wpa_supplicant.conf" file in root folder
    Content of wpa_supplicant.conf
    adapt countrycode, ssid and psk:
    ```
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    country=<Insert 2 letter ISO 3166-1 country code here e.g. DE> 
    
    network={
        ssid="<Name of your wireless LAN>"
        psk="<Password for your wireless LAN>"
    }
    ```
    Replace all ```<...>``` !


1. Starting Pi

2. Access Terminal Shell on Raspberry PI
    -  Using ssh (Requires that ssh is active and pi is connect to network see [Headless SSH Setup](#headlessSSH)
        - type ```ssh pi@raspberrypi``` to connect to "a" Raspberry PI on the network. If this does not work replace "raspberrypi" with its IP adress. 
        -  Default User is "```pi```" and its Password is "```raspberry```"
    -  Using Monitor and Keyboard (not headless Methode)
        -  Login
        -  Default User is "```pi```" and its Password is "```raspberry```"
        -  Open a Terminal

3. First steps
    - Change Password with
      	- ```
          passwd
          ```
    - When not done before use Raspberry Config Manager to enable:
        - ```
          sudo raspi-config
          ```
        - SSH
            - ```Interfaces``` -> ```SSH``` -> Select "Yes"
        - WiFi
            - ```Localisation Options``` -> ```Change wireless country``` -> Select "DE"
            - ```Network Options``` -> ```WiFi``` -> Enter WiFi Name and Password
            - Later you can display the connection state with ```ifconfig```
    - Update Package Library and Upgrade Software
        - ```
          sudo apt update && sudo apt -y upgrade
          ```
4. Other useful Commands/Knowledge
    - Multi terminals
        - More than One SSH-Connection or Terminal can be open at once
    - Shutdown
        - ```
          sudo shutdown -h 0
          ```
    - Reboot
        - ```
          sudo shutdown -r 0
          ```
        - ```
          sudo reboot -h 0
          ```
    - Display performance
        - ```
          htop
          ```
    - Other Tip and Tricks for Basic Config
        - https://www.raspberrypi.org/documentation/configuration/raspi-config.md
        - https://www.elektronik-kompendium.de/sites/raspberry-pi/1906291.htm

## 3. Setup Raspberry Pi for PirAtE<a id="setup"></a>
1. Activate needed Interfaces in ```sudo raspi-config```
    - ```Interfaces``` -> ```Camera``` -> Select "Yes"
    - ```Interfaces``` -> ```Serial``` -> Select "Yes"
    - It will do a restart after this
2. Install needed Software
    - Install Git (on Lite Version it isn't installed by default) 
        - ```
          sudo apt install git -y
          ```
3. Install NodeJs
    - [nodejs](../Pirate-Bridge/Theory/nodejs.md)
4. Install Docker
    - [docker](../Pirate-Bridge/Theory/docker.md)

## Password less SSH
add your public ssh key to ~/.ssh/authorized_keys (on the pi) for passwordless ssh connection

### (optional) generate SSH key
https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

To avoid hassle leave password of the key blank

### Copy public key
Adapt to match your file / key names / domain names

On Linux:
```
cat ~/.ssh/id_rsa.pub | ssh pi@raspberrypi 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```

On Windows:
```
type %USERPROFILE%\\.ssh\\id_rsa.pub | ssh  pi@raspberrypi "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

## (optional) create ssh key on pi and connect it with github

https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh

