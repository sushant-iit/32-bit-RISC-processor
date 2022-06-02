<h1 align="center">RISC Processor Design and Implementation </h1>

<h5 align="center">Design and Implementation of 32-bit RISC processor using Logisim. </h5>
<h5 align="center">Computer Organisation And Architecture | MiniProject | IIT BBS Autumn 2021 </h5>
<img src="https://user-images.githubusercontent.com/59964272/171685712-6a789fce-a1d4-4808-88a1-626dd4bb29d1.gif">
This RISC proceessor can execute assembly programs (can be generated using this table) for the supported operations.

<h2 id="contents">Contents</h2>

 1. [Usage](#usage)
 2. [Supported Operations](#supported_operations)
 3. [Specifications](#specifications)
 4. [Five Stage Architecture Principle](#five_stage_architecture_principle)
 5. [Components](#components)
 6. [Instruction Encoding](#instruction_encoding)
 7. [Sample Encoding With Memory Image](#sample_encodings)
 

<h2 id="usage">Usage</h2>

1. Clone this repository using the following command: \
   `git clone https://github.com/sushant-iit/32-bit-RISC-processor.git`
2. Download logisim using the [link](http://www.cburch.com/logisim/download.html) here and make sure to follow the instructions as mentioned to get the software up and running.
3. Convert the assembly code to machine code using the [instruction encoding](#instruction) guidelines as mentioned.
4. Open **Full_CPU.circ** file in logisim.
5. Click on RESET button before proceeding any further to make sure that data in the registers and RAM is flushed out. \
   Also, make sure that clock is in low state before pressing RESET.
6. Load the encoded instructions into RAM from (0000)<sub>16</sub> address and make sure instruction end at **HALT** instruction (needed to stop the processor from processing further). This is done by right clicking the RAM and clicking *Edit Content* options present.
7. Make sure to enter the data required by programs at appropriate locations in RAM and registers respectively (e.g., inputting base address in the register). 
8. Enable ticks in logisim by pressing `Ctrl+K` to simulate the processor.
9. The processing will stop once it executes the halt instruction.


<h2 id="supported_operations">Supported Operations </h2>

This RISC processor supports the following instructions:

- **MOVE Ri, Rj** The content of Rj is transferred to Ri.
- **MOVE Ri, Immediate (16-bit)** The immediate value (32-bit unsigned extended) will be transferred to Ri.
- **LOAD Ri, X (Rj)** The content of memory location [[Rj] + X] is loaded into Ri, where X is a 16-bit unsigned immediate value.
- **STORE Ri, X (Rj)** The content of register Ri is stored in memory [[Rj] + X], where X is a 16-bit unsigned immediate value.
- **ADD Ri, Rj, Rk** Ri = Rj + Rk.
- **ADI Ri, Rj, Immediate (16-bit)** Ri = Rj + Immediate Value (32-bit unsigned extended)
- **SUB Ri, Rj, Rk** Ri = Rj - Rk
- **SUI Ri, Rj, Immediate (16-bit)** Ri = Rj - Immediate Value (32-bit unsigned extended)
- **AND Ri, Rj, Rk** Ri = Rj AND Rk.
- **ANI Ri, Rj, Immediate (16-bit)** Ri = Rj AND Immediate Value (32-bit unsigned extended)
- **OR Ri, Rj, Rk** Ri = Rj OR Rk.
- **ORI Ri, Rj, Immediate (16-bit)** Ri = Rj OR Immediate Value (32-bit unsigned extended)
- **HLT** STOPS the execution


<h2 id="specifications">Specifications </h2>

| Components | Description |
|:----------:|:-----------:|
|General Purpose Registers (GPRs) | 8 (R0-R7) |
|Special Purpose Registers (SPRs) | 7 (Ra, Rb, Rz, Ry, Rm, IR, PC) |
|Clock cycles / instruction | 5 |
|Address width | 16-bit |
|Data width | 32-bit |
|Number of supported Instructions | 13 |


<h2 id="five_stage_architecture_principle">Five Stage Architecture Principle</h2>

The whole processor architecuture is based on five stage architecture principle which has the following stages:

- Fetch: The content of memory at the address pointed by program counter is fetched into Instruction Register and program counter is incremented as to point to next instruction.
- Decode: The control unit decodes the Op-code inside the IR (Instruction Register) and based on this value, the control signals are generated. The operands for the instruction are also fetched and stored into Ra and/or Rb in this stage.
- Execute: Based on the control signal, the ALU (Arrithmetic Logic Unit) performs addition, subtraction, logical AND, logical OR, or simply bypasses the value to Rz.
- Memory : If the instruction is Load/Store, the results of ALU (the effective address of the data) interacts with memory unit to load/store the data else the result is passed to Ry.
- Writeback: In this stage, if the instruction requires the value to be written back to register file, the value from Ry is written back to register file.

All the above stages can be visualised in a bit lower abstraction of the CPU shown above:
<p align = "center">
 <img src = "https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/CPU_Five_Stage.JPG" height = "500"/>
</p>


<h2 id="components">Components</h2>

The overview for all the components shown above in five staged abstraction of the CPU is shown below:

**1. Arithmertic Logical Unit (ALU):**
   
   <p align="center">
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/ALU_Internal_Implementaion.JPG" height="300"/>
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/ALU_Block.JPG" height="100"/>
   </p>

- It is a combinational circuit that supports the following operations : ADD, SUBTRACT, AND, OR on In_A and In_B and produces result as ALU_OUT.
- The operation being performed is selected using the control signal : **Inst_sel**

**2. Register File (RF):**
   
   <p align="center">
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/RF_Internal_Implementaion.JPG" height="300"/>
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/RF_Block.JPG" height="300"/>
   </p>

- It has 8 registers for storing the data (R0 - R7)
- The writing to RF is selected based on **write** control signal, when **write** = 1 which enables writing of **Input_Data** to one of the 8 reigsters in RF based on the control signal **Input_Addr** bits.
- The data is read through **A** and **B** based on the address provided in **A_Addr** and **B_Addr**, when **write** = 0.
- The **Reset** pin asyncronously clears the data in all the registers.

**3. Program Counter (PC):**

   <p align="center">
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/PC_Internal_Implementaion.JPG" height="300"/>
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/PC_Block.JPG" height="250"/>
   </p>
   
   - It points to the instruction currently being executed in the memory.
   - The **PC_Inc** value decides whether **PC_val** should be increased or remain the same. When **PC_Inc** = 1, **PC_val** increased by 1 (Fetch stage), otherwise remains the same.
   - The **Reset** pin asynchronously clears the data in the program counter.
  
  **4. Memory Management Unit (MMU):**
  
   <p align="center">
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/MMU_Internal_Implementaion.JPG" height="300"/>
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/MMU_Block.JPG" height="300"/>
   </p>
   
   - It interfaces processor with the memory.
   - The **Data_In_From_Mem** can go to either **IR** (in Fetch stage) or **muxY** (in memory stage of instruction, say, load) depending on the control signal **IR_muxY_sel**.
   - The **Data_Out_To_Mem** can go from **Rm** ((in memory stage of instruction, say, store) only.
   - The **Address** bits for accessing memory can come from either **Rz** (if the address is computed/bypassed by ALU) or **PC_val** (in case of fetching the instrcutions from the memory). As the memory has 8 bit address line, LSB 8 bits are selected from **PC_val** or **Rz** based on control signal **PC_Rz_Sel**.

**5. Bits Separator Block:**

   <p align="center">
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/Bits_Internal_Implementation.JPG" height="300"/>
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/Bits_Separator_Block.JPG" height="300"/>
   </p>
   
   - It separates different types of bits from the instruction register and feeds them to different components.
   - The encoding scheme is shown below:
   
|(31-28) Op-Code | (27-25) Dest | (24-22) Src1 | (21-19) Src2 | (18-16) WasteBits | (15-0) Immediate |
|:--------------:|:------------:|:------------:|:------------:|:-----------------:|:----------------:|

**6. Control Unit (CU):**

   <p align="center">
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/CU_Internal_Implementaion.JPG" height="300"/>
     <img src="https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Images/CU_Block.JPG" height="300"/>
   </p>
   
   - It generates timed control signals for controlling all the other components.
   - It generates **PC_Inc**, **CPU_Enable**, **Write**, **IR_muxY_Sel**, **PC_Rz_Sel**, **Inst_Sel**, **MuxB**, **MuxA**, **MuxY**, **Ra_En**, **Rz_En**, **Rm_En**, **Ry_En**, **IR_En**, **Mem_Read** and **Mem_Write**.
   - For getting design approach, [this](https://drive.google.com/file/d/1QNXU-a8gz_NZ5sjQJoMl4DkPRfj-cHtd/view?usp=sharing) report can be looked into.

<h2 id="instruction_encoding">Instruction Encoding</h2>

All the instructions in the designed CPU starts with 4-bit opcode followed by 3-bit destination register address (the CPU has a total of 8 registers) and then 3-bit src1 register address, followed by 3-bit src2 address and then 3-bits are simply wastages (don’t care bits) and in the end 16-bit values for IMMEDIATE field.

|(31-28) Op-Code | (27-25) Dest | (24-22) Src1 | (21-19) Src2 | (18-16) WasteBits | (15-0) Immediate |
|:--------------:|:------------:|:------------:|:------------:|:-----------------:|:----------------:|

The Op-code table with an example encoding for each instruction is shown below:
(x represents don't care conditions)

| Op-Code | Instruction | Example Instruction | Example Encoding |
|:-------:|:-----------:|:-------------------:|:-----------------|
| 0000 | MOVE Ri, Rj | MOVE R2, R3 | 0000 010 011 xxx xxx xxxx-xxxx-xxxx-xxxx |
| 0001 | MOVE Ri, Immediate (16-bit) | MOVE R0, 63 | 0001 000 xxx xxx xxx 0000-0000- 0011-1111 |
| 0010 | LOAD Ri, X (Rj) | LOAD R2, 1 (R3) | 0010 010 011 xxx xxx 0000-0000-0000-0001 |
| 0011 | STORE Ri, X (Rj) | STORE R2, 1 (R3) | 0011 xxx 011 010 xxx 0000-0000-0000-0001 |
| 0100 | ADD Ri, Rj, Rk | ADD R0, R1, R2 | 0100 000 001 010 xxx xxxx-xxxx-xxxx-xxxx |
| 0101 | ADI Ri, Rj, Immediate (16-bit) | ADI R0, R2, 1 | 0101 000 010 xxx xxx 0000-0000-0000-0001 |
| 0110 | SUB Ri, Rj, Rk | SUB R0, R1, R2 | 0110 000 001 010 xxx xxxx-xxxx-xxxx-xxxx |
| 0111 | SUI Ri, Rj, Immediate (16-bit) | SUI R0, R1, 1 | 0111 000 001 xxx xxx 0000-0000-0000-0001 |
| 1000 | AND Ri, Rj, Rk | AND R0, R1, R2 | 1000 000 001 010 xxx xxxx-xxxx-xxxx-xxxx |
| 1001 | ANI Ri, Rj, Immediate (16-bit) | ANI R0, R1, 1 | 1001 000 001 xxx xxx 0000-0000-0000-0001 |
| 1010 | OR Ri, Rj, Rk | OR R0, R1, R2 | 1010 000 001 010 xxx xxxx-xxxx-xxxx-xxxx |
| 1011 | ORI Ri, Rj, Immediate (16-bit) | ORI R0, R1, 1 | 1011 000 001 xxx xxx 0000-0000-0000-0001 |
| 1100 | HLT | HLT | 1100 xxx xxx xxx xxx xxxx-xxxx-xxxx-xxxx |

<h2 id="sample_encodings">Sample Encodings</h2>

<h4> Example 1 </h4>

Suppose, this instruction is to be executed: \
      A = B + C - Immediate \
And, they store these values: \
  Base address register (R2) = 0xA (10 in decimal) \
  B = 0x37 (55 in decimal)    (RAM address 11 in decimal) \
  C = 0x8E (142 in decimal)   (RAM address 12 in decimal) \
  Immediate = 0x65 (101 in decimal) \
  A = Not defined              (RAM address 13 in decimal)
  
A sample assembly code for this can be: \
Load R1, X(R2) ; Loads B \
Load R3, Y(R2) ; Loads C \
Add R1, R1, R3 ; Adds B+C \
Sui R1, R1, #Immediate ; Subtracts Immediate from (B+C) \
Store R1, Z(R2) ; Stores result in A \
HLT ; Halts execution \
where X = 1, Y = 2 and Z = 3.

The required variables like base register and B, C are placed at proper address before starting the execution. \
The machine instructions would be: (Don’t cares replaced by zeroes, doesn’t matter): 
```
Mem_Loc:       Instruction_Encoding      Hex format to be loaded 
00000000: 00100010100000000000000000000001 => 0x22800001 
00000004: 00100110100000000000000000000010 => 0x26800002 
00000008: 01000010010110000000000000000000 => 0x42580000 
0000000C: 01110010010000000000000001100101 => 0x72400065 
00000010: 00110000100010000000000000000011 => 0x30880003 
00000014: 11000000000000000000000000000000 => 0xC0000000 
```

The RAM image for the above program can be easily downloaded from [here](https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Sample%20Programs/Sampe_Program_1_Ram_Image). Just download the image and load the RAM after RESETTING the processor and loading the base register R2 with 0xA, and start the processor by turning on the simulation.


<h4> Example 2 </h4>

Suppose, this instruction is to be executed: \
  A = (B+C).Immediate // + denotes OR, . denotes AND \
 And, they store these values: \
  Base address register (R2) = 0xA (10 in decimal) \
  B = 0x37 (55 in decimal)    (RAM address 11 in decimal) \
  C = 0x8E (142 in decimal)   (RAM address 12 in decimal) \
  Immediate = 0x65 (101 in decimal) \
  A = Not defined              (RAM address 13 in decimal)  
  
A sample assembly code for this can be: \
(Some redundant steps are there to illustrate their encodings as well): \
Load R1, X(R2) ; Loads B \
Move R3, R1 ; Moves R1 to R3 \
Loads R1, Y(R2); Loads C \
Move R4, R1, Moves R1 to R4 \
OR R1, R3, R4 ; Performs OR of R3 and R4 \
ANI R5, R1, #Immediate ; Performs AND with Immediate \
Store R5, Z(R2) ; Stores the result in A \
HLT ; Halts execution \
where X = 1, Y = 2 and Z = 3.


The required variables like base register and B, C are placed at proper address before starting the execution. \
The machine instructions would be: (Don’t cares replaced by zeroes, doesn’t matter): 
```
Mem_Loc:       Instruction_Encoding      Hex format to be loaded 
00000000: 00100010100000000000000000000001 => 0x22800001
00000004: 00000110010000000000000000000000 => 0x06400000
00000008: 00100010100000000000000000000010 => 0x22800002
0000000C: 00001000010000000000000000000000 => 0x08400000
00000010: 10100010111000000000000000000000 => 0xA2E00000
00000014: 10011010010000000000000001100101 => 0x9A400065
00000018: 00110000101010000000000000000011 => 0x30A80003
0000001c: 11000000000000000000000000000000 => 0xC0000000
```

The RAM image for the above program can be easily downloaded from [here](https://github.com/sushant-iit/32-bit-RISC-processor/blob/main/Sample%20Programs/Sampe_Program_2_Ram_Image). Just download the image and load the RAM after RESETTING the processor and loading the base register R2 with 0xA, and start the processor by turning on the simulation.
