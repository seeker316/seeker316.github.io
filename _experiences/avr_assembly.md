---
layout: post
header-style: text
title: AVR assembly notes
img: img/experience/avr_assm.jpeg
---

# ATMEGA 328 Architecture
- Based on Havard Architecture - Data memory and program memory are kept seperate. Hence 2 independent paths to data and memory.
- 8-bit RISC (Reduced Instruction Set Computer) based CPU.
- 32KB of flash memory program storage.
- 32 general registers 0x0000 - 0x001F 
- 64 I/O Registers 0x0020 - 0x005F
- 160 Extendable I/O registers 0x0060 - 0x00FF
- 2KB internal data 0x0100 - 0x08FF

- 32 general registers from R0 to R31.

- atmega 328p microcontroller I/O PORTS, there are total 4 ports
    - PORT A (PORT A is used by the ADC. The ADC is connected to an 8 channel Analog Multiplexer which allows eight single-ended voltage inputs constructed from pins of PORT A)
    - PORT B (8 bit port)
    - PORT C (7 bit port)
    - PORT D (8 bit port)

- Available USART,SPI and TWI.

## General purpose registers (R0-R31)
General-purpose registers in the ATmega328P are 32 registers (R0 to R31) used for temporary data storage, arithmetic operations, and addressing during program execution.

**r0 (Zero Register)** - when R0 is involved in operations where its value is read, it acts as if it always contains zero. 

**r26-r27 (Z Register)** -  Used as a pointer register for indirect addressing in the program memory and SRAM.

**r28-r29 (Y Register)** - nother pair of registers that can be used for indirect addressing, specifically for operations like LDS and STS.

**r30-r31 (X Register)** - also used as a pointer register as well, allowing for indirect addressing similar to the Z and Y registers.

## Special purpose registers - 

**SREG** (status register) - contains status flags
Flags:
- **I** (Global Interrupt Enable): Enables/disables global interrupts.
- **T** (Bit Copy Storage): Used for bit manipulation.
- **H** (Half Carry Flag): Indicates a carry from bit 3 to bit 4 (used in BCD operations).
- **S** (Sign Flag): Set if the result of an operation is negative.
- **V** (Overflow Flag): Indicates an arithmetic overflow.
- **N** (Negative Flag): Set if the result is negative.
- **Z** (Zero Flag): Set if the result of an operation is zero.
- **C** (Carry Flag): Set if there is a carry out from the most significant bit.

### Other special purpose registers
- 16 bit Stack pointer register
- ADC registers
- EEPROM registers
- Timer registers
- and other registers for USART, SOI, TWI etc.

# AVR ASSEMBLY NOTES

**AVR disassembler**
> sudo apt install avra or install via git clone https://github.com/Ro5bert/avra.git

Link if you want to build a stand alone system using just the microcontroller
http://arduino.cc/en/Main/Standalone

The file m328Pdef.inc should be put inside the includes directory or anywhere else you like. We will be including it in our assembly language programs. It gives each of the registers in the microcontroller names from the data sheet so that we don't have to use their hexadecimal names.

for assembling the code 
```shell
avra hello.asm
```
for flashing code in microcontroller
```shell
avrdude -p m328p -c stk5001 - b 57600 /dev/tty/USB0 -U flash:w:hello
```

> comments in assembly are written starting eith a **';'** semicolon in assembly.

> To include a file in assembly we use the .include directive **.include "filepath"**

> **ldi** stands for "load immediate", here in are loading bin value in the r16 working register.
```assembly
ldi r16, 0b00100000 
```
> To write data from a register to an I/O port we use the **out** instruction. It copies the general purpose registers to an I/O register.
```assembly
out PortB, r16
```
> to complete our simple hello world program and keep it running, we need a label. A label in assembly is a named marker that represents a specific memory address or location in the code, used for branching, jumps, or data reference.
int the next line we jump to the label creating a infinite loop
```assembly
Start:
   rjmp Start
```

**LDI**  - Loads value in general purpose registera
LDI r16, 0x2F

**MOV** - Copies contents of register r2 to register r1
MOV r1,r2

**ADD** - Add contents of reg_2 to reg_1 and stores it in reg_1
ADD r1, r2

**SUB** - Sub contents of reg_2 from reg_1 and stores it in reg_2
ADD r1, r2

Similarly there are other standard arithmetic operations like **MUL AND XOR** 

**INC** - increments value in reg 17 by 1 
INC r17

**COM** - performs ones complement on the vlaue of the register (inverts)
COM r19

**LDS** - Load direct from data space, loads data directly from data space (given address).
LDS r16, 0xA9

**STS** - Stores direct from data space, stores aa value from a register into specified memory location.
STS 0X34, r13

**IN** - Reads the value from IO register and stores it in  a destination register
IN R0, PINB 

**OUT** Writes the value from a specified register to the destination IO register


*Branch instructions in AVR assembly are used to control the flow of execution by allowing the program to jump to different sections of code based on certain conditions or unconditionally.*

Types - 

**JMP** The JMP (Jump) instruction is used to transfer program control to a specified address. there are 2 types of jumps conditional and unconditional
In unconditional jump the CPU executes the JUMP regardless of any condition.
```assembly

; Example of an Unconditional Jump
START:
    LDI R0, 0x05   ; Load immediate value 5 into R0
    JMP LOOP       ; Unconditionally jump to LOOP

LOOP:
    ; Code to execute in the loop
    DEC R0         ; Decrement R0
    BREQ END       ; If R0 is zero, branch to END
    JMP LOOP       ; Otherwise, jump back to LOOP

END:
    ; Code after the loop

```

In conditional jump, JMP executes only if a specified condition is fulfilled. 
The **BRNE** (Branch if Not Equal) instruction is used for conditional branching. It allows the program to jump to a specified address if the result of the previous comparison (using the CP or CPI instructions) indicates that two values are not equal.
1. Before using BRNE, a comparison is typically made using CP (Compare Register) or CPI (Compare Immediate).
2. BRNE checks the Zero flag (Z). If Z is cleared (meaning the two compared values are not equal), the program control jumps to the specified label.
3. If Z is set (the values are equal), the program continues executing the next instruction sequentially.
```assembly

; Example of a Conditional Jump
START:
    LDI R0, 0x03   ; Load immediate value 3 into R0
    LDI R1, 0x03   ; Load immediate value 3 into R1
    CP R0, R1      ; Compare R0 and R1
    BRNE NOT_EQUAL  ; Branch to NOT_EQUAL if R0 is not equal to R1

EQUAL:
    ; Code for when R0 equals R1
    JMP END        ; Jump to END

NOT_EQUAL:
    ; Code for when R0 does not equal R1

END:
    ; Code after the conditional execution
```

Similar to this is, 
- **BREQ** (Branch if Equal)
- **BRGE**: Jumps if the Sign flag (N) is clear and Zero flag (Z) is clear (greater or equal).
- **BRLT**: Jumps if the Sign flag (N) is set (less than).
- **BRSH**: Jumps if the Carry flag (C) is clear (same or higher).
- **BRHC**: Jumps if the Half Carry flag (H) is set.
- **BRLO**: Jumps if the Carry flag (C) is set (lower).
- **BRMI**: Jumps if the Sign flag (N) is set (negative).
- **BRPL**: Jumps if the Sign flag (N) is clear (positive).
- **BRTS**: Jumps if the T flag is set.
- **BRVC**: Jumps if the Overflow flag (V) is clear.
- **BRVS**: Jumps if the Overflow flag (V) is set.

## Interrupts
AVR interrupts are hardware features that allow the microcontroller to respond to specific events or conditions by temporarily halting the normal program flow and executing an interrupt service routine (ISR). 

Global interrupts must be enabled using the SEI (Set Interrupt Flag) instruction. Specific interrupts are enabled by setting their corresponding bits in the relevant control registers

Each interrupt type has a specific address in the interrupt vector table. When an interrupt occurs, the microcontroller jumps to the address associated with the interrupt's ISR.

An ISR is a special function that handles the interrupt. It must be defined using the ISR macro, which associates the function with a specific interrupt vector.

### how to write an ISR?
Define the ISR: Use the specific vector for the interrupt.
Enable the Interrupt: Set the necessary control bits in registers.
Set Global Interrupt Flag: Use the SEI instruction.
Handle the Interrupt: Implement the necessary actions in the ISR.
Return from Interrupt: Use the RETI instruction to return to the main program.

```assembly
.include "m328Pdef.inc"  ; Include device definitions

.org 0x00                ; Reset vector
    rjmp main            ; Jump to main program

.org INT0_vect           ; Interrupt vector for INT0
    rjmp int0_handler     ; Jump to INT0 interrupt handler

main:
    ; Set up INT0 (external interrupt)
    ldi r16, (1 << ISC01) ; Trigger on falling edge
    sts EICRA, r16       ; Store in EICRA
    ldi r16, (1 << INT0)  ; Enable INT0
    sts EIMSK, r16       ; Store in EIMSK

    sei                   ; Enable global interrupts

loop:
    ; Main program loop
    nop                   ; Do nothing (or other tasks)
    rjmp loop            ; Loop forever

int0_handler:
    ; Code to execute when INT0 is triggered
    ; Clear the interrupt flag
    ldi r16, (1 << INT0)  ; Clear INT0 flag
    sts EIFR, r16         ; Store in EIFR
    ; Additional handling code here
    reti                  ; Return from interrupt

```
