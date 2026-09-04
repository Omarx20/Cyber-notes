  #                                                           CHAPTER:1
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
1:every data every input is a bunch of bits and interpreting these bits debeds on the context, it could be same sequence of bits but it could be a number, ASCII text, instructions


2:programs are translated by other programs(compile drivers) to the low level machine code  it's written by text editor but it's human readable(source program),
linked with some standard libraries,using functions, using variables : 

first: the compiling driver first trasnnlate it into modified source program(preprocessor) that it extends the file headers ad defines it (still an ASCII text file),

second: it compiles the source program(becoming a assembly program), 

third: assembling the program to become an machine code(binary)program , 

forth:linking, that we talked about the used functions that are defined in another standard libraries then it becomes an excutable program

3:processors take instructions --> when you run an executable program it's loaded from disk to the main memory, pc(proram counter) points at the addres of the program in memory,
loading these instuction into registers, opreating these instructions in ALU 

shell is a command line program that you can executes command from

multi threading is that the processor can execute multible control flow for same process

multi core is that processor can operate multi proccess parralle and each has it's own memory cash l1,l2  but l3 and lower memory and storage is shared 

every input is a stream of bits so input devices it could store as files(sequence of bytes) 

buses carry bits in words(fixed amount of bytes) : it could be 32bit,64bit

but who gives these instructions to run the program, shedueling proccess, handle the software instructins : it's the kernel

operating systems: makes an abstraction layer to proccess : processor --> processes, main memory --> virtual memory, i/o devices --> files

these abstraction layers make us handling easier with these complicated level 

text+-----------------------------------+ 0xFFFFFFFF (4 GB)


|           Kernel Space            |


|       (Operating System/I/O)      |

+-----------------------------------+ 0xC0000000 (3 GB)

|                                   |


|         Unused / Guard            |


|                                   |


+-----------------------------------+

|               user Stack               |  --> every time y use functions


|                 v                 |


+-----------------------------------+


|       memory maped regoin for shred libraries        | --> functions                |


|          (Memory Growth)          |


|                 ^                 |


+-----------------------------------+



|        run time heap         | --> memory allocations


+-----------------------------------+



|        read/write data          | --> variables


+-----------------------------------+

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
#                                                    CHAPTER:2

|          read only code and data |           |


+-----------------------------------+ 0x00000000 (0 GB)

