BASED ON PREVIOUS PROJECT [View github Repo](https://github.com/12zcab/Handheld-Z80-Retro-Computer)
# What is this?
This is a portable handheld computer made with z80 customized for assembly language programming and simplified system for educational purpose.
The computer contains a Z80 cpu,a 1602 or 2004 LCD and 2 EEPROM slot,a RAM slot,a buzzer with IO ic driver
Its originally designed for another project and edited for better performance as a programmable calculator.
The circuit is edit for PURE HAND WIRING.

# What it does?
It's a calculator that you can do programming on!
My final goal is to do complex trigonometry and statistic calculation on this calculator!
Also hopefully I can do a Bad Apple Animation on this device XD

# Why it exists?
I just learnt how computer calculates complicated functions with taylor series simplification and I wanna try it in real life and make my own cool retro calculator :D



# Basic Structure

\##Memory Map

|Address|Compoenent|Function|
|-|-|-|
|0x0000 - 0x7FFF|EEPROM|Executable Code of Operating System|
|0x8000 - 0xFEFF|RAM or EEPROM|Multibanked External EEPROM and RAM|
|0xFF00 - 0xFFFF|IO Outputs|for LCD Buzzer and Keyboard Input Output|
|----|----|----|



# Compoenents

## Main Compoenents

|Name|Usage|Quantity|
|-|-|-|
|Z84C00xxP|The CPU of the Computer|1|
|DS12885+ Clock Module|Clock Module For Z80|1|
|LCD1602 or 2004|Display  for Z80|1|
|SN76489AN|Buzzer Output IC driver|1|
|62256|32k RAM for Z80|1|
|28C256|32k EEPROM for External file and Bootloader and Applications|2|
|PTS 810 SMD Button|Keyboard Input for Z80|64|
|4 Pin 6mm Switch|Reset Button for Z80|1|
|LED SMD 1206|SMD LED with internal resistor for Signal and Data Bus displaying|39|
|Buzzer|D12mm height 9.5mm with RM7.6mm Buzzer for ESP32 C3 Supermini to Emit sound when receive specific serial input from Z80|1|
|CR2032 battery holder|Battery for DS12885+ Clock Module|1|

## Logic Gates and ICs

|Name|Usage|Quantity|
|-|-|-|
|74HC573|A Registor for Input and Output and Multibanking|3|
|74HC04|Hex Logic Invertor for toggling High-Activate Signals and Low-Activate Signals|2|
|4068|8 Input AND/NAND Logic Gate for IO Signal Filtering|1|
|74154|4 to 16 Decoder for IOS Address Decoding|1|
|74AHCT1G32|Single Or Gate|2|
|74AHCT1G02|Single NOR Gate|1|

## Crystals

|Name|Usage|Quantity|
|-|-|-|
|4 Pin Crystals|20MHz and 4MHz for CPU and Buzzer Driver ic respectively|2|
|2 Pin Crystal|32768Hz for DS12885+ Clock Module|1|

## Resistors

|Name|Usage|Quantity|
|-|-|-|
|Potential Rotary Resistor|for LCD1602|1|
|1K resistor|for LCD1602 or 2004|1|
|10K resistor|Pull-up Resistors|4|



