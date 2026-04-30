BASED ON PREVIOUS PROJECT [View github Repo](https://github.com/12zcab/Handheld-Z80-Retro-Computer)
# What is this?
This is a portable handheld computer made with z80 customized for assembly language programming and simplified system for educational purpose.
The computer contains a Z80 cpu,a 1602 or 2004 LCD and 2 EEPROM slot,a RAM slot,a buzzer with IO ic driver
Its originally designed for another project and edited for better performance as a programmable calculator.
The circuit is edited to make it more simple.

# What it does?
It's a calculator that you can do programming on!
My final goal is to do complex trigonometry and statistic calculation on this calculator!
Also hopefully I can do a Bad Apple Animation on this device XD

Main Functions:
|Type|Function|
|-|-|
|Varaible|ABCDEHIJKLMNOPQRSTUVWXYZ 26 in total :D Hopefully can support 1*10^-99 to 9.999999999*10^99|
|basic operation|+ - * /|
|Trigo|Sin Cos Tan,ArcSin ArcCos ArcTan|
|Sqrt and indexes|^ NthRoot Sqr Sqrt|
|Log|Ln,Log,e^,10^|
|Formula|Save custom formula to input variables and output the result|
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