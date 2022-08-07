# MIPS-32-Processor
MIPS 32 Processor Architecture design using Verilog

Introduction
The instruction set architecture of a popular Reduced Instruction Set Architecture (RISC) processor, viz.MIPS32 is a 32-bit processor, i.e it can operate on 32 bits of data at a time.
In this instruction set architecture, the concept of pipelining has been used and all the instructions are written in Verilog language.
MIPs32 has 32-bit general purpose registers(GPRs), R0 to R31.By default, register R0 contains a constant 0 which can not be overridden.
A special-purpose 32-bit program counter (PC)  is used to point the next instruction in memory to be fetched and executed.
For simplicity, No flag registers(Zero, carry, sign, etc) are used.
The processor uses very few addressing modes (register, immediate, register indexed etc) and only load and store instructions can access memory. We assume the memory word size is  32 bits(word addressable).

Instruction Subset-
   • Load  and  Store  Instructions	
                  LW R2,124(R8)    // R2 = Mem[R8+124]
                     SW R5,-10(R25)   // Mem[R25-10] = R5


  • Arithmetic and Logic Instructions(only register operands)
 ADD R1,R2,R3                       // R1 = R2 + R3
 ADD R1,R2,R0                     // R1 = R2 + 0
 SUB R12,R10,R8               // R12 = R10 – R8 
 AND R20,R1,R5               // R20 = R1 & R5
 OR R11,R5,R6                // R11 = R5 | R6
 MUL R5,R6,R7               // R5 = R6 * R7
 SLT R5,R11,R12           // If R11 < R12, R5=1; else R5=0 
• Arithmetic	and	Logic	Instructions	(immediate	operand)	
 ADDI R1,R2,25      // R1 = R2 + 25
 SUBI R5,R1,150 // R5 = R1 – 150 
SLTI R2,R10,10 // If R10<10, R2=1; else R2=0
 • Branch Instructions	
 BEQZ R1,Loop // Branch to Loop if R1=0 BNEQZ R5,Label // Branch to Label if R5!=0 
• Jump	Instruction
	 J Loop // Branch to Loop unconditionally
 • Miscellaneous	Instruction	
 HLT // Halt execution

           
   Mips Instruction Decoding

• All MIPS32 instructionscan be classified into three groups in terms of instruction	encoding.
	 – R-type (Register), I-type (Immediate), and	J-type	(Jump).
	 – In an instruction encoding,	 the 32bits of	the instruction are divided into	 several	fields	of fixed widths.
	 – All	instructions	may not use all the fields.	
 • Since the relative positions	of some of the	fields	are	same	across	 instructions,	     instruction decoding	becomes very	simple.
• Here	an instruction can use up to three register operands.
	 – Two	sources and one destination.	
 • In addition,	for shift instructions,	the number of	bits to	shift can also	be specified (we	are not considering such instructions 	here).

![image](https://user-images.githubusercontent.com/70213929/183301598-ad9e7713-cc23-4efd-9a46-f7884c422a8f.png)

• R-type instructions	considered with opcode:

![image](https://user-images.githubusercontent.com/70213929/183301619-f0768215-aff0-4fbe-b0aa-ca4534d2002e.png)

(b)	I-type	Instruc1on	Encoding

 Contains a 16-bit immediate	data field.
 Supports one	source	and one destination register.
![image](https://user-images.githubusercontent.com/70213929/183301633-0a4ef2f2-8544-492a-9492-a646fd102ef9.png)

• I-type instructions	considered with opcode: 
![image](https://user-images.githubusercontent.com/70213929/183301649-85e9eab4-91f7-43b8-aa30-ea76b8f7a5b6.png)

(c)	J-type	Instruc1on	Encoding	
 • Contains a 26-bit jump	address field.
	 – Extended to 28-bits by padding two 0’s on the right. 
![image](https://user-images.githubusercontent.com/70213929/183301663-59d69c1d-c72b-4663-8cad-4699a2d51d0f.png)
• Some instructions require two register operands rs &rt as input, while some require only rs.	
 • Gets	 known	  only	after instruction is decoded.	

 While	decoding is going on, we can	prefetch the registers	in parallel which may or may	not be	 required later. 

• Similarly,   the 16-bit and 26-bit  immediate data	are retrieved	and sign-extended to   32-bits in case they are required.


                    Addressing Modes in MIPS32
 • Register addressing                ADD R1, R2, R3	
 • Immediate addressing            ADDI R1, R2,200
 • Base	addressing               LW R5,150(R7)	
        – Content of a register is added to a“base” value to get the operand address.	
 • PC	relative addressing             BEQZ R3, Label	
                 – 16-bit offset is added to PC to get the target address.
 • Pseudo-direct	addressing J Label	
               – 26-bit offset is added to PC to get the target address. 

                                            MIPS32	Instruction	Cycle	
 • We	divide	the	instruction	execution	cycle	into	five	steps:
	 a) IF:	        Instruction Fetch	
              b) ID:	        Instruction Decode / Register Fetch
	 c) EX:	         Execution	/ Effective Address Calculation
	 d) MEM:      Memory	Access	/Branch Completion
	 e) WB:  	Register Write-back
	 • We	now show the	generic micro-instructions carries out the various steps.

(a)	IF:	Instruction Fetch	
            • Here	the instruction pointed to by	PC is fetched from	memory, and	also the	next value of PC	is computed.
	 – Every  MIPS32 instruction	is of	32-bits.
	 – Every memory word is of 32 bits and has a  unique address.
	 – For	a  branch instruction, the new value of the   PC  may be	the	target	address. So PC is not	updated in this stage;
	a new	value	is stored in a	register NPC.  
![image](https://user-images.githubusercontent.com/70213929/183301682-6aeb415d-daf2-4668-b307-ee3b821983c6.png)
For byte-addressable memory, PC has to be incremented by 4.

(b) ID	:	Instruction	Decode
      • The	instruction already fetched	in IR is decoded.
	 – Opcode is 6-bits (bits  31:26).
	 – First	source	operand rs(bits 25:21), second source operand rt(bits 20:16).
	 – 16-bit immediate data (bits	15:0).
	 – 26-bit immediate data (bits	25:0).
	 • Decoding	is done	in parallel with reading the register operands rs and	rt.
	                – Possible because these fields are	in a fixed location in	the instruction 	format.
	 • In	a similar way,	the immediate data are sign-extended.

ID : 
![image](https://user-images.githubusercontent.com/70213929/183301705-dc4a0485-78ac-4db3-9d76-daa9a5e701c9.png)
   A, B, Imm, Imm1 are temporary registers.

(c)   EX: Execution/ Effective Address Computation
      • In	   this	step,	the ALU is used to perform some calculations.
	     – The exact	operation depends on the instruction that is already decoded.
	      – The ALU	operates on	operands that	have been already made ready in the	previous cycle.
      • A, B, Imm,	etc.
      • We show	the micro-instructions corresponding to the type of instruction.  
![image](https://user-images.githubusercontent.com/70213929/183301716-fa5fd73c-b1d2-47e1-9b1d-234a0a18e82e.png)
(d)	MEM:	Memory Access/Branch Completion
	 • The	only instructions that make use of this step	are loads, stores, and branches. 
	 – The	load and store	instructions access the memory.
	 – The	branch	instruction updates PC is depending	upon	the outcome of the branch condition.
![image](https://user-images.githubusercontent.com/70213929/183301734-74c4fb84-80e9-4d12-b402-59a97302c11c.png)
![image](https://user-images.githubusercontent.com/70213929/183301738-1f3a3712-caf2-4ad1-922b-17ff2f288748.png)
 (e)	WB:	Register Write Back

• In this step, the result is written back into	the register file.
	 – Result may come from the	ALU.
	 – Result may come from the memory system (viz. a load instruction).
	 • The	position of the destination register in the instruction word	depends on the	instruction    → already known after decoding.
![image](https://user-images.githubusercontent.com/70213929/183301768-fb4a0413-1208-48d7-bf30-6263c0cc98a6.png)
PIPELINE IMPLEMENTATION OF  PROCESSOR-

 Basic	requirements	for pipelining the MIPS32 data path:	 – We	should	be able to start	a	new	instruction every clock cycle.
	 – Each	of the	five steps mentioned	before	(IF, ID, EX, MEM, and	WB)becomes	the pipeline stage.
	 – Each	stage	must	finish	its execution within one clock	 cycle.
![image](https://user-images.githubusercontent.com/70213929/183301781-d2a6e18f-46c3-4660-8397-8fa168aa8d38.png)
![MIPS 32 processor](https://user-images.githubusercontent.com/70213929/183301807-c5c388c9-99c6-4498-ad4b-163605626efe.png)
