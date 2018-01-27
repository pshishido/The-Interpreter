# The Interpreter for Language X
Project Overview:

	For this project we were tasked with finishing the implementation of the interpreter for the language “x”. We were given this project as a partially completed skeleton, but there was still ample work to do. We were responsible with implementing four classes: ByteCodeLoader (BCL), Program, RuntimeStack (RTS), and VirtualMachine (VM). On top of this we also had to implement fifteen byte code classes: halt, pop, false branch, go to, store, load, lit, args, call, return, bop, read, write, dump, and label; all of which are extensions of the abstract class ByteCode. BCL is responsible for taking in the file name of a “<filename>.x.cod” file as a command line argument, parsing through this file creating instances of each byte code in the file, and adding these byte code instances to the Program array list. The CodeTable, which contains an instance of a hash map, allows us to create these instances of each byte code on the fly. At the end of BCL, the “resolveAddrs” method in Program is call with the program array list as a parameter. This method then traverses through the Program array list and extracts the integer representation of the label addresses, and passes this information to byte code instances that will need this address for future execution (FalseBranchCode, GotoCode, and CallCode will need this). Once the addresses of the Program array list are all resolved, an instance of VM is created with this Program array list. The instance of VM then calls upon it’s “executeProgram” method which will call upon the execute method of every byte code in program (the execute that is invoked is dependent on the type of byte code instance that is calling upon it). Although each of the byte code instances will need to interact with the RTS when executing the execute method, we cannot call upon these RTS methods directly because doing so will break encapsulation. To get around this, VM contains a set of methods whose sole purpose is to pass calls from bytecode execute methods to the RTS class. 


#Compilation/Execution Instructions:

	This project was built in IntelliJ IDEA. Compile and execute this program as you normally would, being sure to pass the file name of the program you would like to test in the format: <filename>.x.cod, as a command line argument. The “Interpreter” class is the entry point to this project.



#Assumptions:

	One assumption that is made throughout this project is that the file that is passed to the Interpreter is a valid (syntactically, logically) .x.cod file with no errors.
  

#Implementation Details:

BCL:

The “loadCodes” method in BCL was completed using a combination of a BufferedReader (BR) object and a StringTokenizer (ST) object to read in the relevant information from the given file. The BR allowed one line at a time to be read in, and the ST helped me tokenize each line to obtain the class name of the byte code (using the CodeTable class), as well as each byte codes individual arguments. Each byte code in the file is initialized with their given arguments, using the “init” abstract method in ByteCode, and then added to the Program array list. Finally, after the file has been fully read in, “resolveAddrs” is called with the Program array list as an argument before exiting “loadCodes”.


Program:

The “resolveAddrs” method in Program was completed by utilizing a hash map that stores label codes string argument as keys, and the associated line number/index of those labels as values! This is done by traversing the Program array list whilst checking for “instanceof” label codes; every time a label code is seen, it stored along with the location at which it is found. Finally, the Program array list is traversed once more whilst checking for “instanceof” false branch codes, go to codes, and call codes. Every time an instance of these byte codes is seen, I use the hash map to grab the integer value that represents the label address to which the byte code should jump to when appropriate, and save that value in the corresponding byte code’s data field using the “init” function.

RTS:

The RTS contains several methods that allow us to operate on the contents of the RTS. Furthermore, these methods (most, but not all) are mirrored in the VM class to allow the VM to act on the RTS and the framePointStack without breaking encapsulation. These methods include pop and peek (for the RTS and the FPS), push, load, and store. The dump method is also contained with the RTS class, which allows us to print the contents of the RTS to output in a specified format. Care was taken to not print the dump codes whilst dumping the RTS and maintain proper formatting. Two iterator objects we used to accomplish this task; one for the RTS and one for the FPS. It was necessary to do this because we need to know when a new frame starts and ends in order to print the ‘[‘ and ‘]’ in the correct locations. Beyond this, a “toString” method was implemented in every byte code class to make printing each byte code cleaner and easier (can use System.out.print(ByteCode)).

VM:

The VM class contains methods that act as a “middle-man” between the RTS and the VM. In every byte codes’ execute method, the an action on the RTS must be performed. However, if the VM directly calls upon methods such as “vm.runStack.pop()”, encapsulation is broken. So these methods allow the VM to pass the call on to the RTS without breaking encapsulation. Besides adding these methods, all that ws needed to complete the VM class was to add in one more variable (isDumping) that allows dump to be toggled on/off, and using a if-statement inside of the “executeProgram” method to determine when we should turn dump on.

ByteCode:

The abstract class ByteCode (BC) allows for extendibility the byte code class in the language x. Beyond this, we are able to utilize dynamic-binding because of implementing an abstract BC class; without it would not be possible to determine which BC execute method to during runtime! Each one of the extensions of the BC was filled out according to the definitions in the table within the PDF.



#Results and Conclusion:

	This project, being one of the larger programs I have worked on in my career as a Computer Science major, provided many new insights into “programming in the large”. Up until this point, most projects that I have worked on are solely based on logical reasoning (solving programming problems, in contrast to developing), so being able to work on an assignment that required me to think deeper into programming design really helped me grow as an intermediate programmer. One example of this is the requirement of not breaking encapsulation; before this project this is something I honestly never thought of, but I can now see the reasoning behind doing so. As programmer’s we need to not think in terms of throwing code at the wall and hoping it works, but rather think in terms of planning and designing beforehand in order to avoid running in circles. The goal is to create code that is reusable and easily updatable.

