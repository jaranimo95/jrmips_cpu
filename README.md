Christian Jarani, CS 449

Project 2: JrMIPS Processor
---------------------------

NOTE: I have listed the found problems underneath the affected components. If a component does not have a specific problem listed
      underneath it, then I have not found it. If you happen to, any feedback about what the problem is and how to fix it would
      be greatly appreciated. All operations have been implemented, but some of their functionalities are not 100% correct with each
      time that they are performed. Again, I will list all complications I had in hopes of making this whole grading process a little less
      painful for the both of us. Thanks for grading all of these, by the way! I'm sure it sucks, but we all appreciate it a ton.

Components:
-----------
REGMEM (Register Memory):    Contains 16-bit registers $r0-$r7 to be read from by 3-bit values Read Register 1 & 2 as well as
                             written to by 3-bit value RegWrite. A demultiplexer is used to determine which of the registers to
                             write to, and two multiplexors determine which registers to read from. Reading and writing are
                             controlled by anded 1-bit control signal EnableWrite (0 = read, 1 = write) and a clock triggered
			     by a rising edge.

			     PROBLEMS: During loads and arithmetic operations, sometimes will save to incorrect register or not at 
				       all despite correct destination register and EnableWrite signals being present in their
				       correct input locations.
			     

ALU (Arithmetic Logic Unit): Contains a 16-bit adder, 2x subtracter (one for subtraction and the other for branch comparisons,
			     NOR, OR (used for branch comparisons), and AND gates. Basic arithmetic operations (add, sub, nor, and)
			     are selected via a 2-bit Operation control signal. Branch comparison values are sent to the BCONTROL
			     subcircuit for branch processing (see later in README).

			     SIDENOTE: Could be the cause of the problems in REGMEM, but I'm still unsure as to if it is causing the
				       problem 100% of the time.


FIELDS (Instr. Partitioner): Contains a 16-bit splitter which partitions an instruction into an I-format (converted to R-format in main).


MULT/DIV (Mult./Div. ALU):   Contains a 16-bit multiplier, divider, 3 multiplexors, 2 16-bit registers $HI and $LO, and 2 1-bit control signals
			     OPselect (0 = division || mlo, 1 = multiplication || mhi) and HiLoWrite (0 = output, 1 = input).


CONTROL (Main Control):      Contains 5 1-bit inputs opcode3-0 and subop, a ton of AND and NOT gates, a few OR gates, 12 1-bit control signals,
			     and 1 2-bit control signals (ALUop). 

			     PROBLEMS (???): To the best of my knowledge, the control did not have any problems with it. However,
			     		     I was only able to attempt to debug it for around 2 hours so I may have missed something.


BCONTROL (Branch Control):   Controls all branch operations and relevant control signals. Refer to ALU component in project file for complete
			     documentation of the branch comparison values (also stated in BCONTROL). AND's the 1-bit results of the AND'd operation
			     comparison (make sure that a branch instruction was called) and the bit indicating success or failure of the requested
			     branch. These values are OR'd in order to set the multiplexor to input the branch address into PC instead of PC + 1.

JCONTROL (Jump Control):     Controls all jump operations and relevant control signals. The opcode circuit used to determine thhe JSelect and JALSelect
			     control signals is a little messy thanks to combinational analysis, but it works. The result of the AND comparison of the 3rd,
			     1st, and 0th bits is used to determine which value to (not) put into PC or RS.