# RAK811 BreakBoard （For RAK811 TrackerBoard and RAK811 SensorNodeBoard）
- chip: LoRaWAN (RAK811) + GPS (MAX-7Q) + MEMS (LIS3DH) + charge battery (BQ23210)
- Support CoIDE/Keil5 
- Base on semtech LoRaWAN1.0.2
- Support for Makefile and the gcc-arm-none-eabi toolchain under linux

For the hardware documentation, please check http://www.rakwireless.com/en/download

# Building on Ubuntu
## Installing the toolchain
In order to be able to compile for the STM32, you need to install the appropriate toolchain. There is a PPA with the gcc-arm-none-eabi toolchain (https://launchpad.net/~team-gcc-arm-embedded/+archive/ubuntu/ppa).
```
sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
sudo apt update
sudo apt install gcc-arm-none-eabi
```
Starting with Ubuntu 18.04LTS, `sudo apt update` is not necessary anymore, as it will be done automaticaly upon adding the PPA.

## Building the project
- clone the project from github: `git clone https://github.com/eiten/RAK811_BreakBoard.git`
- cd into the project folder: `cd RAK811_BreakBoard`
- Adjust the settings for your LoRaWAN network (eg `nano ./src/apps/classA/Commissioning.h`)
- compile: `make`
