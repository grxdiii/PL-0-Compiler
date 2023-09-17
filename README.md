# PL/0 Compiler
This is an implmentation for a small PL/0 Compiler writing in C.
## Virtual Machine (virtual_machine.c)
The virtual machine or P-Machine is a stack machine that has one memory area called the process address space. The PAS is dived into sections: the text which contains the list of instructions to be performed by the machine, and the stack which organizes the data to be used by the CPU. 
### Instruction Set Architecture (ISA)
There are 10 arithmetic/logical operations that manipulate the data within the stack segment.
```
01 LIT 0, M Pushes a constant value (literal) M onto the stack
02 OPR 0, M Operation to be performed on the data at the top of the stack (or return from function)
03 LOD L, M Load value to top of stack from the stack location at offset M in AR located L lexicographical levels down
04 STO L, M Store value at top of stack in the stack location at offset M in AR located L lexicographical levels down
05 CAL L, M Call procedure at code index M (generates new Activation Record and PC <- M)
06 INC 0, M Allocates M memory words (increment SP by M)
07 JMP 0, M Jump to instruction M (PC <- M)
08 JCP 0, M Jump to instruction M if top stack element is 0
09 SYS 0, 1 Write the top stack element to the screen
   SYS 0, 2 Read in input from the user and store it on top of the stack
   SYS 0, 3 End of program (Set Halt flag to zero)
```
### Vocabulary
```
AR - Activation Record
OPR - Operation Code
PAS - Process Address Space
L - Lexicographical level
M - Depending on the operators indicates:
    a. a number (when OP is LIT or INC)
    b. a program address (when OP is JMP, JCP, or CAL)
    c. a data address (when OP is LOD, STO)
    d. the identify of the arithmetic operation associated to the OPR op-code.
PC - Program Counter
IR - Instruction Register
BP - Base Pointer
SP - Stack Pointer
```

### Example Input
```
7 0 75
7 0 6
6 0 4
3 1 4
4 0 3
3 1 4
1 0 1
2 0 2
4 1 4
3 1 4
1 0 0
2 0 5
8 0 45
1 0 1
4 1 3
3 1 4
1 0 0
2 0 9
8 0 60
5 1 6
3 1 3
3 0 3
2 0 3
4 1 3
2 0 0
6 0 5
1 0 3
4 0 4
5 0 6
3 0 3
9 0 1
9 0 3
```
### Corresponding Output
```
		  PC   BP   SP   stack
Initial values:    0   96   95

    JMP   0  75   75   96   95   
    INC   0   5   78   96   100   0 0 0 0 0 
    LIT   0   3   81   96   101   0 0 0 0 0 3 
    STO   0   4   84   96   100   0 0 0 0 3 
    CAL   0   6    6   101   100   0 0 0 0 3 | 
    INC   0   4    9   101   104   0 0 0 0 3 | 96 96 87 0 
    LOD   1   4   12   101   105   0 0 0 0 3 | 96 96 87 0 3 
    STO   0   3   15   101   104   0 0 0 0 3 | 96 96 87 3 
    LOD   1   4   18   101   105   0 0 0 0 3 | 96 96 87 3 3 
    LIT   0   1   21   101   106   0 0 0 0 3 | 96 96 87 3 3 1 
    SUB   0   2   24   101   105   0 0 0 0 3 | 96 96 87 3 2 
    STO   1   4   27   101   104   0 0 0 0 2 | 96 96 87 3 
    LOD   1   4   30   101   105   0 0 0 0 2 | 96 96 87 3 2 
    LIT   0   0   33   101   106   0 0 0 0 2 | 96 96 87 3 2 0 
    EQL   0   5   36   101   105   0 0 0 0 2 | 96 96 87 3 0 
    JCP   0  45   45   101   104   0 0 0 0 2 | 96 96 87 3 
    LOD   1   4   48   101   105   0 0 0 0 2 | 96 96 87 3 2 
    LIT   0   0   51   101   106   0 0 0 0 2 | 96 96 87 3 2 0 
    GTR   0   9   54   101   105   0 0 0 0 2 | 96 96 87 3 1 
    JCP   0  60   57   101   104   0 0 0 0 2 | 96 96 87 3 
    CAL   1   6    6   105   104   0 0 0 0 2 | 96 96 87 3 | 
    INC   0   4    9   105   108   0 0 0 0 2 | 96 96 87 3 | 96 101 60 0 
    LOD   1   4   12   105   109   0 0 0 0 2 | 96 96 87 3 | 96 101 60 0 2 
    STO   0   3   15   105   108   0 0 0 0 2 | 96 96 87 3 | 96 101 60 2 
    LOD   1   4   18   105   109   0 0 0 0 2 | 96 96 87 3 | 96 101 60 2 2 
    LIT   0   1   21   105   110   0 0 0 0 2 | 96 96 87 3 | 96 101 60 2 2 1 
    SUB   0   2   24   105   109   0 0 0 0 2 | 96 96 87 3 | 96 101 60 2 1 
    STO   1   4   27   105   108   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 
    LOD   1   4   30   105   109   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 1 
    LIT   0   0   33   105   110   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 1 0 
    EQL   0   5   36   105   109   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 0 
    JCP   0  45   45   105   108   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 
    LOD   1   4   48   105   109   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 1 
    LIT   0   0   51   105   110   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 1 0 
    GTR   0   9   54   105   109   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 1 
    JCP   0  60   57   105   108   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 
    CAL   1   6    6   109   108   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 | 
    INC   0   4    9   109   112   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 | 96 105 60 0 
    LOD   1   4   12   109   113   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 | 96 105 60 0 1 
    STO   0   3   15   109   112   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 
    LOD   1   4   18   109   113   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 1 
    LIT   0   1   21   109   114   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 1 1 
    SUB   0   2   24   109   113   0 0 0 0 1 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 0 
    STO   1   4   27   109   112   0 0 0 0 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 
    LOD   1   4   30   109   113   0 0 0 0 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 0 
    LIT   0   0   33   109   114   0 0 0 0 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 0 0 
    EQL   0   5   36   109   113   0 0 0 0 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 1 
    JCP   0  45   39   109   112   0 0 0 0 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 
    LIT   0   1   42   109   113   0 0 0 0 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 1 
    STO   1   3   45   109   112   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 
    LOD   1   4   48   109   113   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 0 
    LIT   0   0   51   109   114   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 0 0 
    GTR   0   9   54   109   113   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 0 
    JCP   0  60   60   109   112   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 
    LOD   1   3   63   109   113   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 1 
    LOD   0   3   66   109   114   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 1 1 
    MUL   0   3   69   109   113   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 1 
    STO   1   3   72   109   112   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 | 96 105 60 1 
    RTN   0   0   60   105   108   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 
    LOD   1   3   63   105   109   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 1 
    LOD   0   3   66   105   110   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 1 2 
    MUL   0   3   69   105   109   0 0 0 1 0 | 96 96 87 3 | 96 101 60 2 2 
    STO   1   3   72   105   108   0 0 0 2 0 | 96 96 87 3 | 96 101 60 2 
    RTN   0   0   60   101   104   0 0 0 2 0 | 96 96 87 3 
    LOD   1   3   63   101   105   0 0 0 2 0 | 96 96 87 3 2 
    LOD   0   3   66   101   106   0 0 0 2 0 | 96 96 87 3 2 3 
    MUL   0   3   69   101   105   0 0 0 2 0 | 96 96 87 3 6 
    STO   1   3   72   101   104   0 0 0 6 0 | 96 96 87 3 
    RTN   0   0   87   96   100   0 0 0 6 0 
    LOD   0   3   90   96   101   0 0 0 6 0 6 
Output result is:  6
    SYS   0   1   93   96   100   0 0 0 6 0 
    SYS   0   3   96   96   100   0 0 0 6 0
```