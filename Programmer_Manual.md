# Features
1.LCD1602 or LCD2004
2.Multibank RAM/EEPROM and Status LED Output
3.DS12885+ Clock Module
4.Keyboard
5.Buzzer IC output

# LCD1602 or LCD2004
not yet finished

# Multibank RAM/EEPROM and Status LED Programming

It's just basically a 74hc573 on IO Port 4(0xFF40).
Bit 0: Low\_Activate ChipSelect Signal for 32k RAM on address 0x8000 to 0xFEFF
Bit 1: Low\_Activate ChipSelect Signal for EEPROM on address 0x8000 to 0xFEFF
Bit 2-7: Status LED on board

```z80asm
; Define IO Address
BANK\_REG   EQU 0xFF40

; Enable EEPROM and Disable RAM and Turn Off all LEDs
ld a, 01h
out (BANK\_REG), a

; Enable RAM and Disable EEPROM and Turn On all LEDs
ld a, FEh
out (BANK\_REG), a
```

# DS12885+ Clock Module Programming [Reference Document](https://www.analog.com/media/en/technical-documentation/data-sheets/DS12885-DS12C887A.pdf)

It's on the IO  Port of the Z80.
The Clock Module have two modes : BCD Mode and Binary Mode

### Table 2A. Time, Calendar, and Alarm Data Modes—BCD Mode (DM = 0)



|ADDRESS|BIT 7|BIT 6|BIT 5|BIT 4|BIT 3|BIT 2|BIT 1|BIT 0|FUNCTION|RANGE|
|-|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|-|-|
|00H|0|<-|10 Seconds|->|<-|Seconds|->||Seconds|00–59|
|01H|0|<-|10 Seconds|->|<-|Seconds|->||Seconds Alarm|00–59|
|02H|0|<-|10 Minutes|->|<-|Minutes|->||Minutes|00–59|
|03H|0|<-|10 Minutes|->|<-|Minutes|->||Minutes Alarm|00–59|
|04H|AM/PM|0|10 Hours||<-|Hours|->||Hours|1–12 +AM/PM / 00–23|
|05H|AM/PM|0|10 Hours||<-|Hours|->||Hours Alarm|1–12 +AM/PM / 00–23|
|06H|0|0|0|0|0|<-|Day|->|Day|01–07|
|07H|0|0|<-|10 Date|<-|Date|->||Date|01–31|
|08H|0|0|0|10 Mo.|<-|Month|->||Months|01–12|
|09H|<-|10 Years|->||<-|Year|->||Year|00–99|
|0AH|UIP|DV2|DV1|DV0|RS3|RS2|RS1|RS0|Control|—|
|0BH|SET|PIE|AIE|UIE|SQWE|DM|24/12|DSE|Control|—|
|0CH|IRQF|PF|AF|UF|0|0|0|0|Control|—|
|0DH|VRT|0|0|0|0|0|0|0|Control|—|
|0EH-31H|X|X|X|X|X|X|X|X|RAM|—|
|32H|<-|10 Century|->||<-|Century\*|->||Century\*|00–99|
|33H-7FH|X|X|X|X|X|X|X|X|RAM|—|

\---

### Table 2B. Time, Calendar, and Alarm Data Modes—Binary Mode (DM = 1)



|ADDRESS|BIT 7|BIT 6|BIT 5|BIT 4|BIT 3|BIT 2|BIT 1|BIT 0|FUNCTION|RANGE|
|-|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|-|-|
|00H|0|0|<-|->|Seconds|<-|->||Seconds|00–3B|
|01H|0|0|<-|->|Seconds|<-|->||Seconds Alarm|00–3B|
|02H|0|0|<-|->|Minutes|<-|->||Minutes|00–3B|
|03H|0|0|<-|->|Minutes|<-|->||Minutes Alarm|00–3B|
|04H|AM/PM|0|0|<-|->|Hours|<-|->|Hours|01–0C +AM/PM / 00–17|
|05H|AM/PM|0|0|<-|->|Hours|<-|->|Hours Alarm|01–0C +AM/PM / 00–17|
|06H|0|0|0|0|0|<-|Day|->|Day|01–07|
|07H|0|0|0|<-|->|Date|<-|->|Date|01–1F|
|08H|0|0|0|0|<-|->|Month||Months|01–0C|
|09H|0|<-|->|Year|<-|->|<-|->|Year|00–63|
|0AH|UIP|DV2|DV1|DV0|RS3|RS2|RS1|RS0|Control|—|
|0BH|SET|PIE|AIE|UIE|SQWE|DM|24/12|DSE|Control|—|
|0CH|IRQF|PF|AF|UF|0|0|0|0|Control|—|
|0DH|VRT|0|0|0|0|0|0|0|Control|—|
|0EH-31H|X|X|X|X|X|X|X|X|RAM|—|
|32H|N/A|N/A|<-|->|Century\*|<-|->||Century\*|—|
|33H-7FH|X|X|X|X|X|X|X|X|RAM|—|

\---

**Notes:**

* **X** = Read/Write Bit.
* **\*** = DS12C887, DS12C887A only. General-purpose RAM on DS12885, DS12887, and DS12887A.
* **Note**: Unless otherwise specified, the state of the registers is not defined when power is first applied. Except for the seconds register, 0 bits in the time and date registers can be written to 1, but may be modified when the clock updates. 0 bits should always be written to 0 except for alarm mask bits.

```z80asm
; =====================================================================
; DS12885+ REAL TIME CLOCK DRIVER (Z80)
; Port Mapping:
; FF71h -> AS High (Address Strobe High)
; FF70h -> AS Low  (Latch Address / Data Access)
; =====================================================================

; --- Port Definitions ---
RTC\_CMD      EQU 0FF71h    ; Port with A0=1 (AS High)
RTC\_DATA     EQU 0FF70h    ; Port with A0=0 (AS Low)

; --- Internal Register Map ---
RTC\_SEC      EQU 00h       ; Seconds
RTC\_MIN      EQU 02h       ; Minutes
RTC\_HOUR     EQU 04h       ; Hours
RTC\_DAY\_WEEK EQU 06h       ; Day of week (1-7)
RTC\_DAY\_MON  EQU 07h       ; Day of month (1-31)
RTC\_MONTH    EQU 08h       ; Month (1-12)
RTC\_YEAR     EQU 09h       ; Year (00-99)
RTC\_REG\_A    EQU 0Ah       ; Status Register A
RTC\_REG\_B    EQU 0Bh       ; Status Register B

; =====================================================================
; INITIALIZATION: Setup Oscillator and 24-Hour BCD Mode
; =====================================================================
INIT\_RTC:
    ; 1. Setup Register A: Turn on the 32.768kHz oscillator
    LD A, RTC\_REG\_A
    CALL SELECT\_REG
    LD A, 00100000b       ; DV2-0 = 010 (Osc On), RS3-0 = 0000 (None)
    OUT (C), A

    ; 2. Setup Register B: 24Hr mode, BCD format, Daylight Savings Off
    LD A, RTC\_REG\_B
    CALL SELECT\_REG
    LD A, 00000010b       ; DM=0 (BCD), 24/12=1 (24Hr), DSE=0
    OUT (C), A
    RET

; =====================================================================
; READ TIME: Safely reads Seconds, Minutes, and Hours
; Returns: H = Hours, D = Minutes, E = Seconds (all BCD)
; =====================================================================
GET\_TIME:
    ; Step 1: Wait for Update-In-Progress (UIP) bit to be clear
    ; This ensures we don't read data while the clock is updating.
WAIT\_RTC:
    LD A, RTC\_REG\_A
    CALL SELECT\_REG
    IN A, (C)
    AND 80h               ; Check bit 7 (UIP)
    JR NZ, WAIT\_RTC       ; Loop if update is in progress

    ; Step 2: Read values
    LD A, RTC\_SEC
    CALL SELECT\_REG
    IN E, (C)             ; E = Seconds

    LD A, RTC\_MIN
    CALL SELECT\_REG
    IN D, (C)             ; D = Minutes

    LD A, RTC\_HOUR
    CALL SELECT\_REG
    IN H, (C)             ; H = Hours
    RET

; =====================================================================
; WRITE TIME: Set clock to 14:05:00 (Example)
; =====================================================================
SET\_TIME\_EXAMPLE:
    ; 1. Enter "Set Mode" (Stop updates)
    LD A, RTC\_REG\_B
    CALL SELECT\_REG
    LD A, 10000010b       ; Set SET bit (7) to 1
    OUT (C), A

    ; 2. Write New Time (14:05:00)
    LD A, RTC\_SEC
    CALL SELECT\_REG
    LD A, 00h
    OUT (C), A

    LD A, RTC\_MIN
    CALL SELECT\_REG
    LD A, 05h
    OUT (C), A

    LD A, RTC\_HOUR
    CALL SELECT\_REG
    LD A, 14h             ; 2 PM in BCD
    OUT (C), A

    ; 3. Exit "Set Mode" (Resume updates)
    LD A, RTC\_REG\_B
    CALL SELECT\_REG
    LD A, 00000010b       ; Clear SET bit
    OUT (C), A
    RET

; =====================================================================
; HELPER: Multiplexed Address Latch
; Input: A = Register Index
; Output: BC = RTC\_DATA port address for subsequent IN/OUT
; =====================================================================
SELECT\_REG:
    LD BC, RTC\_CMD        ; A0 = 1 (AS High)
    OUT (C), A            ; Put index on data bus
    LD BC, RTC\_DATA       ; A0 = 0 (AS High-to-Low transition)
    OUT (C), A            ; Address is now latched inside DS12885
    RET

```



# Keyboard Programming

Keyboard is connected to two 74hc573s,IO Port 0 (0xFF00) for Row Select and IO Port 1(0xFF10) for Column Read.
Although the keyboard is wired as a 8x8 matrix,the layout on the PCB is 16\*4.
Please refer to the KiCad Design before programming as the layout is a bit hard to describe in pure text document.
Note that as no diodes are connected to the keyboard circuit, multiple(more than two) keypress may cause glitched input

```z80asm
COL   EQU 0xFF00
ROW   EQU 0xFF10
; Select Column
LD A,01h
OUT (COL),A
; Read Row
IN A,(ROW)
```


# Buzzer Output Programming
Not yet Finished
