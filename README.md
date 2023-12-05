# M6809

New development work with the Motorola 6809 8-bit CPU for novel products.

## Building a microcomputer-experimental station around the Motorola 6809 CPU.

This is for a kit that uses Motorola 6809 as CPU. It has a UART chip added, an 6850 ACIA. The circuit is as simple as possible, so uses a small number of components. All decoder logic is placed in a PLD chip. This makes the circuit is very easy to build. 

It uses a cc09 c-compiler for 6809. The monitor was developed using c and assembly code. The main clock frequency is 4.9152MHz. UART chip uses E clock, 4.9152MHz/4 as the TXD/RXD clock. The prescaler is 64, so the UART will produce 19,200 bit/s rate.

The 6809 has long branch using 16 bit offset. One of the monitor key provides 16-bit HEX calculator, so it is elementary to find the 8-bit or 16-bit offset. Programming comes in two contexts:

* For assembly (low-level) coding, one can use hex key to enter and test the program.
* For c programming, one can use the UART for S19 file downloading.

## Hardware description

* U5 is the Motorola 68B09 CPU. The 4.9152MHz is generated by onchip oscillator with internal frequency 1.228MHz for CPU operations. NMI, HALT, IRQ, FIRQ are pull-up to logic 1 with R2. Only E and R/W and A0,A1, A10-A15 are used for memory decoding.
* U1 is 32kB EPROM, 27C256. The address space for EPROM is decoded at 0xC000-0xFFFF.
* U2 is 32kB static RAM, 62256. The RAM space is located at 0x0000-0x7FFF. Zero page is located at 0x0000-0x00FF. User program can be tested from address 0x0200.
* U4, memory and I/O spaces decoder chip is made with GAL16V8D. It provides chip selected signals for memory and I/O chips.
* U3, the 20-pin 89C2051 microcontroller chip produces 10ms tick. SW1 selects between 10ms tick or manual IRQ button.
* U6 is 6850 ACIA, UART. The shift clock is derived from E clock, 1228800Hz. Internal prescaler is 64. Thus the bit rate will be 19200 bit/s.
* U12, 74HC541 is 8-bit input port (PORT0). Six bits, PA0-PA5 are input signals of the row keypad.
* U10, 74HC573 is 8-bit output port (PORT2). The 8-bit output drives the 7-segment LED directly. No current limit resistor. U11 (PORT1) drives 6-digit common cathode pin. The brightness is controlled by software controlled PWM. PC7 is speaker output for beep signal.
* U13, 74HC573 is 8-bit output port for 8-bit binary number display. D13 lifts the forward biasing for proper brightness. JR1 is 16-pin socket for text LCD interface. U14, HIN232 converts TTL level to RS232 level.
* Q2, KIA7042 is reset chip for power brownout.  

![Hardware schematic](/images/schematic.png)

### Hardware Features

* CPU: Motorola 68B09, 8-bit Microprocessor @1.2288MHz clock
* Memory: 32kB RAM, 16kB EPROM
* Memory and I/O Decoder chip: Programmable Logic Device GAL16V8D
* Display: high brightness 6-digit 7-segment LED
* Keyboard: 36 keys
* RS232 port: 6850 ACIA 19200 bit/s 8n1
* Debugging LED: 8-bit GPIO1 LED at location $8000
* Tick: 10ms tick produced by 89C2051 for time trigger experiment
* Text LCD interface: direct CPU bus interface text LCD
* Brownout reset: KIA7042 reset chip for power brownout reset
* Expansion header: 40-pin header

The monitor program was developed using c and assembly language. Source code was compiled with cc09, c compiler for 6809 CPU. The source code is available for customizing your own monitor.

### The monitor program features

* Simple hex code entering
* Insert and Delete byte
* User registers: A, B, X, Y, S, U, DP Condition code registers for storing CPU status after -program execution
* HEX calculator for offset calculation
* Copy block of memory
* Motorola s-record S19 downloading
* Memory dump
* Beep ON/OFF
* TEST 10ms

The monitor program will be updated and available for testing at the download links.

### Keyboard layout

Making key layout sticker is simply done by printing the SVG file to sticker paper.

![Keyboard layout](/images/key.png)

### Test code

![An example](/images/test-code.png)

Simple program that writes accumulator content to gpio1 LED at $8000. It will show 8-bit binary counting. Delay1 is small delay subroutine that uses X register. One can enter the hex code into memory and test run directly.

Can you change the speed to run faster?

Another example of using 10ms tick generator for counting binary at 1Hz rate. Change SW1 to 10ms tick.

![Tick-generator example](/images/tick-generator.png)

The IRQ vector in ROM is pointed to new location in RAM at $7FF0. Students can modify where to put IRQ service routine. Above example uses location $6000 for IRQ service. The main code then inserts JMP to IRQ service instruction, 7E 60 00 to location 7FF0. Then clear I flag and wait for interrupt.

The IRQ service uses location 0 for tick counting. When it reaches 100, clear it and increment location 1. We can see 1Hz rate counting of location 1 by sending it to gpio1 LED at location 8000.

Can you change from 1Hz to 10Hz counting rate?

![Terminal](/images/terminal1.png)
Example of using Tera Terminal with key DUMP.

![Terminal](/images/terminal2.png)
S-record file transfer with 1ms character delay setting.

### Program-state output

![With display](/images/6809v1-2s.jpg)

### Parts list

#### Semiconductors

* U1 27C256, 32kB Eprom
* U2 HM62256B, 32kB SRAM
* U3 AT89C2051, 8-bit microcontroller
* U4 GAL16V8D, PLD
* U5 Motorola 68B09, 8-bit microprocessor
* U6 Motorola 6850, ACIA chip
* U7 7805, voltage regulator
* U9,U8 LTC-4727, 7-segment display
* U10,U11,U13 74HC573
* U12 74HC541
* U14 HIN232, RS232 converterQ1 BC557
* Q2 KIA7045
* Q3 BC557 D4 1N4007
* D13 1N5227A
* D14 1N4733A D1,D5,D6,D7,D8,D9,D10, LED
* D11,D12
* D3 POWER LED

#### Resistors (all resistors are 1/8W +/-5%)

* R1 680
* R2 RESISTOR SIP 9
* R3 100
* R13,R4 10k
* R6,R5 1k
* R9,R7 4.7k
* R11,R8 10k RESISTOR SIP 9
* R12,R10 10

#### Capacitors

* C1,C4,C5,C15,C18,C19,C20 10uF
* C3,C2 27pF
* C6 10uF 16V
* C7 1000uF25V
* C8,C9,C10,C11,C12 0.1uF
* C13,C14 0.1uF
* C21,C16 100nF
* C17 10uF 10V

#### Additional parts

* JP1 HEADER 20X2
* JR1 CONN RECT 16
* J1 DC Input
* J2 CON3
* J3 CON4A
* LS1 SPEAKERSW1 ESP switch
* SW2 IRQ
* SW3 RESET
* SW4 FIRQ
* SW5 NMI
* S1,S2,S3,S4,S5,S6,S7,S8
* SW PUSHBUTTON: S9,S10,S11,S12,S13,S14,S15,S16,S17,S18,S19,S20,S21,S22,S23,S24,S25,S26,S27,S28,S29,S30,S31,S32
* TP1,TP2,TP3 TEST POINT
* TP4 +5V
* TP5 GND
* VB1 SUB-D 9, Male (cross cable)
* Y1 4.9152MHz XTAL
* PCB double side plate through hole display filter sheet, Amber color, Keyboard sticker printable SVG file

### Files

TBD.

### Keyboard spacer

![Keyboard spacer](/keypad/MICRO09KEY.stl) 3D file for 6809 kit.