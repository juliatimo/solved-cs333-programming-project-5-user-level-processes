Download Link: https://assignmentchef.com/product/solved-cs333-programming-project-5-user-level-processes
<br>



In this project, you will explore user-level processes.  You will create a single process, running in its own address space.  When this user-level process executes, the CPU will be in “user mode.”

The user-level process will make system calls to the kernel, which will cause the CPU to switch into “system mode.”  Upon completion, the CPU will switch back to user mode before resuming execution of the user-level process.

The user-level process will execute in its own “logical address space.”  Its address space will be broken into a number of “pages” and each page will be stored in a frame in memory.  The pages will be resident (i.e., stored in frames in physical memory) at all times and will not be swapped out to disk in this project.  (Contrast this with “virtual” memory, in which some pages may not be resident in memory.)

The kernel will be entirely protected from the user-level program; nothing the user-level program does can crash the kernel.

Download New Files

The files for this project are available in:<strong>http://www.cs.pdx.edu/~harry/Blitz/OSProject/p5/</strong>

Please retain your old files from previous projects and don’t modify them once you submit them.

You should get the following files:

<strong>Switch.s </strong>

<strong>     Runtime.s </strong>

<strong>     System.h </strong>

<strong>     System.c </strong>

<strong>     List.h </strong>

<strong>     List.c </strong>

<strong>     BitMap.h </strong>

<strong>     BitMap.c </strong>

<strong>     makefile       FileStuff.h </strong>

<strong>     FileStuff.c </strong>

<strong>     Main.h </strong>

<strong>     Main.c </strong>

<strong>     DISK </strong>




<strong>     UserRuntime.s </strong>

<strong>     UserSystem.h </strong>

<strong>     UserSystem.c </strong>

<strong>     MyProgram.h </strong>

<strong>     MyProgram.c </strong>

<strong>     TestProgram1.h </strong>

<strong>     TestProgram1.c </strong>

<strong>     TestProgram2.h </strong>

<strong>     TestProgram2.c </strong>

The following files are unchanged from the last project and you should not modify them:

<strong>Switch.s </strong>

<strong>     Runtime.s </strong>

<strong>     System.h </strong>

<strong>     System.c</strong>  — except HEAP_SIZE has been modified

<strong>     List.h </strong>

<strong>     List.c </strong>

<strong>     BitMap.h </strong>

<strong>     BitMap.c </strong>

The following files are not provided; instead you will modify what you created in the last project.  <u>Copy</u> these files to your <strong>p5</strong> directory, so that you keep the previous <strong>p4</strong> versions in your <strong>p4</strong> directory, and modify the new copies.

<strong>     Kernel.h       Kernel.c </strong>Merging New “File Stuff” Code

For this project, we are distributing additional code which you should add to the <strong>Kernel</strong> package.  Please add the material in <strong>FileStuff.c</strong> to the end of file <strong>Kernel.c.</strong>  It should be inserted directly before the final <strong>endCode</strong> keyword.

Also, please add the material in <strong>FileStuff.h</strong> to the end of file <strong>Kernel.h</strong>.  It should be inserted directly before the final <strong>endHeader</strong> keyword.

This code adds the following classes: DiskDriver     FileManager  FileControlBlock       OpenFile

You will use these classes, but you should not modify them.

There will be a single <strong>DiskDriver</strong> object (called <strong>diskDriver</strong>) which is created and initialized at start-up time.  There will be a single <strong>FileManager</strong> object (called <strong>fileManager</strong>) which is created and initialized at start-up time.  The new <strong>main</strong> function contains statements to create and initialize the <strong>diskDriver</strong> and the <strong>fileManager</strong> objects.

<strong>FileControlBlock</strong> and <strong>OpenFile</strong> objects will be handled much like <strong>Threads</strong> and

<strong>ProcessControlBlocks</strong>.  They are a limited resource.  A limited supply is created at start-up time and then they are managed by the <strong>fileManager</strong>.  There is a free list of <strong>FileControlBlock</strong> objects and a free list of <strong>OpenFile</strong> objects.   The <strong>fileManager</strong> oversees both of these free lists.  Threads may make requests and may return resources, by invoking methods in the <strong>fileManager</strong>.

The <strong>diskDriver</strong> object encapsulates all the hardware specific details of the disk.  It provides a method that allows a thread to read a sector from disk into a memory frame and it provides a method that writes a frame from memory to a sector on disk.

Other Changes To Your Kernel Code

Please make the following changes to your copy of <strong>Kernel.h</strong>:

Change

NUMBER_OF_PHYSICAL_PAGE_FRAMES = 27               — for testing only to:

NUMBER_OF_PHYSICAL_PAGE_FRAMES = 100              — for testing only

Change

–diskDriver: DiskDriver

–fileManager: FileManager to:

diskDriver: DiskDriver        fileManager: FileManager

Add a function prototype for the function <strong>InitFirstProcess</strong>.  You can add it after the other function prototypes:

Change

ProcessFinish (exitStatus: int) to:

ProcessFinish (exitStatus: int)

InitFirstProcess ()

Please make the following changes to your copy of <strong>Kernel.c</strong>:

Change the <strong>DiskInterruptHandler</strong> function from:

FatalError (“DISK INTERRUPTS NOT EXPECTED IN PROJECT 4”) to:

currentInterruptStatus = DISABLED

— print (“DiskInterruptHandler invoked!
”)       if diskDriver.semToSignalOnCompletion

diskDriver.semToSignalOnCompletion.Up()

endIf<strong><u>Task 1:</u></strong>




Your first task is to load and execute the user-level program called <strong>MyProgram</strong>.  Since the user-level program must be read from a file on the BLITZ disk, you’ll first need to understand how the BLITZ disk works, how files are stored on the disk, and how the <strong>FileManager</strong> code works.

<strong>MyProgram</strong> invokes the <strong>SystemShutdown</strong> syscall, which you’ll need to implement.

<strong><u>Task 2:</u></strong>

Modify all the syscall handlers so they print the arguments that are passed to them.  In the case of integer arguments, this should be straightforward, but the following syscalls take a pointer to an array of char as one of their arguments.

Exec    Create             Open

This pointer is in the user-program’s logical address space.  You must first move the string from userspace to a buffer in kernel space.  Only then can it be safely printed.

Also, some of the syscalls return a result.  You must modify the handlers for these syscalls so that the following syscalls return these values.  (These are just arbitrary values, to make sure you can return something.)

<table width="0">

 <tbody>

  <tr>

   <td width="120">            <strong>Fork</strong></td>

   <td width="36">1000</td>

  </tr>

  <tr>

   <td width="120">            <strong>Join</strong></td>

   <td width="36">2000</td>

  </tr>

  <tr>

   <td width="120">            <strong>Exec</strong></td>

   <td width="36">3000</td>

  </tr>

  <tr>

   <td width="120">            <strong>Create</strong></td>

   <td width="36">4000</td>

  </tr>

  <tr>

   <td width="120">            <strong>Open</strong></td>

   <td width="36">5000</td>

  </tr>

  <tr>

   <td width="120">            <strong>Read</strong></td>

   <td width="36">6000</td>

  </tr>

  <tr>

   <td width="120">            <strong>Write</strong></td>

   <td width="36">7000</td>

  </tr>

  <tr>

   <td width="120">            <strong>Seek</strong></td>

   <td width="36">8000</td>

  </tr>

 </tbody>

</table>




For this task, you should modify only the handler methods (e.g., <strong>Handle_Sys_Fork</strong>, <strong>Handle_Sys_Join</strong>, etc.)  You should <u>not</u> modify <strong>SyscallTrapHandler</strong> or the wrapper functions in <strong>UserSystem</strong>

<strong><u>Task 3:</u></strong>

Implement the <strong>Exec</strong> syscall.  The <strong>Exec</strong> syscall will read a new executable program from disk and copy it into the address space of the process which invoked the <strong>Exec</strong>.  It will then begin execution of the new program.  Unless there are errors, there will not be a return from the <strong>Exec</strong> syscall.

The User-Level View

First, let’s look at our operating system from the users’ point of view.  User-level programs will be able to invoke the following kernel routines:

Exit     Shutdown       Yield  Fork    Join     Exec    Create             Open  Read  Write  Seek              Close

(This is the grand plan for our OS; these system calls will not be implemented in this project.)

These syscalls are quite similar to kernel syscalls of the same names in Unix.  We describe their precise functionality later.

A user-level program will be written in KPL and linked with the following files:UserSystem.h             UserSystem.c             UserRuntime.

We are providing a sample user-level program in <strong>MyProgram.h / .c</strong>.

The <strong>UserSystem</strong> package includes a wrapper (or “jacket”) function for each of the system calls.  Here are the names of the wrapper functions.  There is a one-to-one correspondence between the system calls and the wrapper functions.




<table width="0">

 <tbody>

  <tr>

   <td width="162">            <strong><u>System call</u></strong></td>

   <td width="169"><strong><u>Wrapper function name</u></strong></td>

  </tr>

  <tr>

   <td width="162">               Exit</td>

   <td width="169">Sys_Exit</td>

  </tr>

  <tr>

   <td width="162">               Shutdown</td>

   <td width="169">Sys_Shutdown</td>

  </tr>

  <tr>

   <td width="162">               Yield</td>

   <td width="169">Sys_Yield</td>

  </tr>

  <tr>

   <td width="162">               Fork</td>

   <td width="169">Sys_Fork</td>

  </tr>

  <tr>

   <td width="162">               Join</td>

   <td width="169">Sys_Join</td>

  </tr>

  <tr>

   <td width="162">               Exec</td>

   <td width="169">Sys_Exec</td>

  </tr>

  <tr>

   <td width="162">               Create</td>

   <td width="169">Sys_Create</td>

  </tr>

  <tr>

   <td width="162">               Open</td>

   <td width="169">Sys_Open</td>

  </tr>

  <tr>

   <td width="162">               Read</td>

   <td width="169">Sys_Read</td>

  </tr>

  <tr>

   <td width="162">               Write</td>

   <td width="169">Sys_Write</td>

  </tr>

  <tr>

   <td width="162">               Seek</td>

   <td width="169">Sys_Seek</td>

  </tr>

  <tr>

   <td width="162">               Close</td>

   <td width="169">Sys_Close</td>

  </tr>

 </tbody>

</table>




(In Unix, the wrapper function often has the same name as the syscall.  All wrapper functions have names beginning with  <strong>Sys_</strong>  just to help make the distinction between wrapper and syscall.)

Each wrapper function works the same way.  It invokes an assembly language routine called <strong>DoSyscall</strong>, which executes a “syscall” machine instruction.  When the kernel call finishes, the <strong>DoSyscall</strong> function simply returns to the wrapper function, which returns to the user’s code.

Arguments may be passed to and from the kernel call.  In general, these are integers and pointers to memory.  The wrapper function works with <strong>DoSyscall</strong> to pass the arguments.  When the wrapper function calls <strong>DoSyscall</strong>, it will push the arguments onto the stack.  The <strong>DoSyscall</strong> will take the arguments off the stack and move them into registers.  Since it runs as a user-level function, it places them in the “user” registers.  (Recall that the BLITZ machine has a set of 16 “system registers” and a set of 16 “user registers.”)

Each wrapper function also uses an integer code to indicate which kernel function is involved.  Here is the <strong>enum</strong> giving the different codes.  For example, the code for “Fork” is 4.

enum SYSCALL_EXIT = 1,        SYSCALL_SHUTDOWN,        SYSCALL_YIELD,

SYSCALL_FORK,

SYSCALL_JOIN,

SYSCALL_EXEC,

SYSCALL_CREATE,

SYSCALL_OPEN,

SYSCALL_READ,

SYSCALL_WRITE,

SYSCALL_SEEK,

SYSCALL_CLOSE




These code numbers are used both by the user-level program and by the kernel.  Consequently, there is an identical copy of this <strong>enum</strong> in both <strong>Kernel.h </strong>and <strong>UserSystem.h</strong>.  (You should not change the system call interface, but if one were to change these code numbers, it would be critical that both<strong> enum</strong>s were changed identically.)




As an example, here is the code for the wrapper function for “Read.”  It simply invokes <strong>DoSyscall</strong> and returns whatever <strong>DoSyscall</strong> returns.




<u>function</u> Sys_Read (fileDesc: <u>int</u>,                        buffer: <u>ptr</u> <u>to</u> <u>char</u>,                        sizeInBytes: <u>int</u>) <u>returns</u> <u>int</u>         <u>return</u> DoSyscall (SYSCALL_READ,                           fileDesc,                           buffer <u>asInteger</u>,                           sizeInBytes,

0)       <u>endFunction</u>




Here is the function prototype for <strong>DoSyscall</strong>:




<u>external</u> DoSyscall (funCode, arg1, arg2, arg3, arg4: <u>int</u>) <u>returns</u> <u>int</u>




The <strong>DoSyscall</strong> routine is set up to deal with up to 4 arguments.  Since the <strong>Read</strong> syscall only needs 3 arguments, the wrapper function must supply an extra zero for the fourth argument.




<strong>DoSyscall</strong> treats all of its arguments as untyped words (i.e., as <strong>int</strong>), so the wrapper functions must coerce the types of the arguments if they are not <strong>int</strong>.  Whatever <strong>DoSyscall</strong> returns, the wrapper function will return.




<strong>DoSyscall</strong> is in <strong>UserRuntime.s</strong>, which will be linked with all user programs.  The code is given next.




It moves each of the 4 arguments into registers r1, r2, r3, and r4.  It then moves the function code into register r5 and executes the <strong>syscall</strong> instruction.  It assumes the kernel will place the result (if any) in r1, so after the <strong>syscall</strong> instruction, it moves the return value from r1 to the stack, so that the wrapper function can retrieve it.




DoSyscall:

load [r15+8],r1 ! Move arg1 into r1           load       [r15+12],r2       ! Move arg2 into r2           load       [r15+16],r3       ! Move arg3 into r3           load       [r15+20],r4       ! Move arg4 into r4           load       [r15+4],r5 ! Move funcCode into r5             syscall r5       ! Do the syscall

store       r1,[r15+4] ! Move result from r1 onto stack           ret         ! Return




Some of the kernel routines require no arguments and/or return no result.  As an example, consider the wrapper function for <strong>Yield</strong>.  The compiler knows that <strong>DoSyscall</strong> returns a result, so it insists that we do something with this value.  The wrapper function simply moves it into a variable and ignores it.




<u>function</u> Sys_Yield ()         var ignore: <u>int</u>

ignore = DoSyscall (SYSCALL_YIELD, 0, 0, 0, 0)       <u>endFunction</u>




Here is a list of all the wrapper functions, including their arguments and return types.




<strong>Sys_Exit</strong> (returnStatus: <u>int</u>)

<strong>Sys_Shutdown</strong> ()

<strong>Sys_Yield</strong> ()

<strong>Sys_Fork</strong> () <u>returns</u> <u>int</u>

<strong>Sys_Join</strong> (processID: <u>int</u>) <u>returns</u> <u>int</u>

<strong>Sys_Exec</strong> (filename: String) <u>returns</u> <u>int</u>

<strong>Sys_Create</strong> (filename: String) <u>returns</u> <u>int</u>

<strong>Sys_Open</strong> (filename: String) <u>returns</u> <u>int</u>

<strong>Sys_Read</strong> (fileDesc: <u>int</u>, buffer: <u>ptr</u> <u>to</u> char, sizeInBytes: <u>int</u>)

returns <u>int</u>

<strong>Sys_Write</strong> (fileDesc: <u>int</u>, buffer: <u>ptr</u> <u>to</u> char, sizeInBytes: <u>int</u>)

returns <u>int</u>

<strong>Sys_Seek</strong> (fileDesc: <u>int</u>, newCurrentPos: <u>int</u>) returns <u>int</u>

<strong>Sys_Close</strong> (fileDesc: <u>int</u>)




In addition to the wrapper functions, the <strong>UserSystem</strong> package contains a few other routines that support the KPL language.  These are more-or-less duplicates of the same routines in the <strong>System</strong> package.  Likewise, some of the material from <strong>Runtime.s</strong> is duplicated in <strong>UserRuntime.s</strong>.  This duplication is necessary because user-level programs cannot invoke any of the routines that are part of the kernel.




For example the functions <strong>print</strong>, <strong>printInt</strong>, <strong>nl</strong>, etc. have been duplicated at the user level so the userlevel program has the ability to print.




[Note that, at this point, all printing is done by cheating, using a “trapdoor” in the emulator.  Normally, a user-level program would need to invoke syscalls (such as <strong>Sys_Write</strong>) to perform any output, since user-level programs can’t access the I/O devices directly.  However, since we are not yet ready to address questions about output to the serial device, we are including these cheater print functions, which rely on a trapdoor in the emulator.]




Every user-level program needs to “use” the <strong>UserSystem</strong> package and be linked with the <strong>UserRuntime.s</strong> code.  For example:




<strong>            <u>MyProgram.h</u> </strong>     header MyProgram    uses UserSystem          functions           main ()     endHeader




<strong>            <u>MyProgram.c</u> </strong>       code MyProgram

function main ()

print (“My user-level program is running!
”)

Sys_Shutdown ()

endFunction  endCode




Here are the commands to prepare a user-level program for execution.  The <strong>makefile</strong> has been modified to include these commands.




asm  UserRuntime.s comp UserSystem -unsafe asm  UserSystem.s comp MyProgram -unsafe asm  MyProgram.s

lddd UserRuntime.o UserSystem.o MyProgram.o -o MyProgram




Note that there is no connection with the kernel.  The user-level programs are compiled and linked independently.  All communication with the kernel will be through the syscall interface, via the wrapper functions.




This is exactly the way Unix works.  For user-level programs, library functions and wrapper functions are brought into the “a.out” file at link-time, as needed.  This explains why a seemingly small “C” program can produce a rather large “a.out” executable.  One small use of <strong>printf</strong> in a program might pull in, at link-time, more output formatting and buffering routines than you can possibly imagine.




When an OS wants to execute a user-level program, it will go to a disk file to find the executable.  Then it will read that executable into memory and start up the new process.




In order to execute MyProgram, we need to introduce the BLITZ “disk.”  The disk is simulated with a Unix file called “DISK.”  After the user-level program is compiled, it must be placed on the BLITZ disk with the following Unix commands:




diskUtil -i

diskUtil -a MyProgram MyProgram




The first command creates an empty file system on the disk.  The second command copies a file from the Unix file system to the BLITZ disk.  It creates a directory entry and moves the data to the proper place on the simulated BLITZ disk.  Commands to initialize the BLITZ disk have also been added to the <strong>makefile</strong>.




Once the kernel is running, it will read the file from the simulated BLITZ disk and copy it into memory.










<h1>The Syscall Interface</h1>




In our OS, each process will have exactly one thread.  A process may also have several open files and can do I/O via the <strong>Read</strong> and <strong>Write</strong> syscalls.  The I/O will go to the BLITZ disk.  For now, there is no serial (i.e., terminal) device.




Next we describe each syscall in more detail.




<h2>function Sys_Exit (returnStatus: int)</h2>




This function causes the current process and its thread to terminate.  The <strong>returnStatus</strong> will be saved so that it can be passed to a <strong>Sys_Join</strong> executed by the parent process.  This function never returns.




<strong>function Sys_Shutdown () </strong>




This function will cause an immediate shutdown of the kernel.  It will not return.




<h2>function Sys_Yield ()</h2>




This function yields the CPU to another process on the ready list.  Once this process is scheduled again, this function will return.  From the caller’s perspective, this routine is similar to a “nop.”




<h2>function Sys_Fork () returns int</h2>




This function creates a new process which is a copy of the current process.  The new process will have a copy of the virtual memory space and all files open in the original process will also be open in the new process.  Both processes will then return from this function.  In the parent process, the <strong>pid</strong> of the child will be returned; in the child, zero will be returned.




<h2>function Sys_Join (processID: int) returns int</h2>




This function causes the caller to wait until the process with the given <strong>pid</strong> has terminated, by executing a call to <strong>Sys_Exit</strong>.  The <strong>returnStatus</strong> passed by that process to <strong>Sys_Exit</strong> will be returned from this function.  If the other process invokes <strong>Sys_Exit</strong> first, this <strong>returnStatus</strong> will be saved until either its parent executes a <strong>Sys_Join</strong> naming that process’s <strong>pid</strong> or until its parent terminates.




<h2>function Sys_Exec (filename: String) returns int</h2>




This function is passed the name of a file.  That file is assumed to be an executable file.  It is read in to memory, overwriting the entire address space of the current process.  Then the OS will begin executing the new process.  Any open files in the current process will remain open and unchanged in the new process. Normally, this function will not return.  If there are problems, this function will return -1.

function Sys_Create (filename: String) returns int

This function creates a new file on the disk.  If all is okay, it returns 0, otherwise it returns a nonzero error code.  This function does not open the file; so the caller must use <strong>Sys_Open</strong> before attempting any I/O.

function Sys_Open (filename: String) returns int




This function opens a file.  The file must exist already exist. If all is OK, this function returns a file descriptor, which is a small, non-negative integer.  It errors occur, this function returns -1.

function Sys_Read (fileDesc: int, buffer: ptr to char, sizeInBytes: int) returns int




This function is passed the <strong>fileDescriptor</strong> of a file (which is assumed to have been successfully opened), a pointer to an area of memory, and a count of the number of bytes to transfer.  This function reads that many bytes from the current position in the file and places them in memory.  If there are not enough bytes between the current position and the end of the file, then a lesser number of bytes are transferred.  The current file position will be advanced by the number of bytes transferred.




If the input is coming from the serial device (the terminal), this function will wait for at least one character to be typed before returning, and then will return as many characters as have been typed and buffered since the previous call to this function.




This function will return the  number of characters moved.  If there are errors, it will return -1.




<h2>function Sys_Write (fileDesc: int, buffer: ptr to char, sizeInBytes: int) returns int</h2>




This function is passed the <strong>fileDescriptor</strong> of a file (which is assumed to have been successfully opened), a pointer to an area of memory, and a count of the number of bytes to transfer.  This function writes that many bytes from the memory to the current position in the file.




If the end of the file is reached, the file’s size will be increased.




The current file position will be advanced by the number of bytes transferred, so that future writes will follow the data transferred in this invocation.




The output may also be directed to the serial output, i.e., to the terminal.




This function will return the  number of characters moved.  If there are errors, it will return -1. <strong>function Sys_Seek (fileDesc: int, newCurrentPosition: int) returns int </strong>

This function is passed the <strong>fileDescriptor</strong> of a file (which is assumed to have been successfully opened), and a new current position.  This function sets the current position in the file to the given value and returns the new current position.

Setting the current position to zero causes the next read or write to refer to the very first byte in the file.  If the file size is N bytes, setting the position to N will cause the next write to append data to the end of the file.

The current position is always between 0 and N, where N is the file’s size in bytes.

If -1 is supplied as the new current position, the current position will be set to N (the file size in bytes) and N will be returned.

It is an error to supply a <strong>newCurrentPosition</strong> that is less than -1 or greater than N.  If so, -1 will be returned.

function Sys_Close (fileDesc: int)




This function is passed the <strong>fileDescriptor</strong> of a file, which is assumed to be open.  It closes the file, which includes writing out any data buffered by the kernel.




<strong> </strong>




<h1> asynchronous  Interrupts</h1>




From time-to-time an asynchronous interrupt will occur.  Consider a <strong>DiskInterrupt</strong> as an example.

When this happens, an assembly routine called <strong>DiskInterruptHandler</strong> in <strong>Runtime.s</strong> will be jumped to.  It begins by saving the system registers (after all, a Disk Interrupt might occur while a kernel routine is executing and we’ll need to return to it).  Then <strong>DiskInterruptHandler</strong> performs an “upcall” to the function named <strong>DiskInterruptHandler</strong> in <strong>Kernel.c</strong>.  Perhaps it is a little confusing to have an assembly routine and a KPL routine with the same name, but, oh well…




The high-level <strong>DiskInterruptHandler</strong> routine simply signals a semaphore and returns to the assembly <strong>DiskInterruptHandler</strong> routine, which restores the system registers and returns to whatever code was interrupted.  All the time while these routines are running, interrupts are disabled and no other interrupts can occur.




Also note that the interrupt handler uses space on the system stack of whichever thread was interrupted.  It might be that some unsuspecting user-level code was running.  Although the interrupt handler will use the system stack of that thread, the thread will be none-the-wiser.  While the interrupt handler is running, it is running as part of some more-or-less randomly selected thread.  The interrupt handler is not a thread on its own.

<h1>Error Exception Handling</h1>




When a runtime error is  detected by the CPU, the CPU performs exception processing, which is similar to the way it processes an interrupt.  Here are the sorts of runtime errors that can occur in the BLITZ architecture:




<h2>            Illegal Instruction      Arithmetic Exception            Address Exception    Page Invalid Exception              Page Read-only Exception  Privileged Instruction           Alignment Exception</h2>




As an example, consider what happens when an <strong>Alignment Exception</strong> occurs.  (The others are handled the same way.)




The CPU will consult the interrupt vector in low memory (see <strong>Runtime.s</strong>) and will jump to an assembly language routine called <strong>AlignmentExceptionHandler</strong>.  The assembly routine first checks to see if the interrupted code was executing in system mode or not.  If it was in system mode, then the assumption is that there is a bug in the kernel, so the assembly routine prints a message and halts execution.




However, if the CPU was in user mode, the assumption is that the user-level program has a bug.  The OS will need to handle that bug without itself stopping.  So the assembly <strong>AlignmentExceptionHandler</strong> routine makes an upcall to a KPL routine with the same name.




The high-level <strong>AlignmentExceptionHandler</strong> routine simply prints a message and terminates the process.  Process termination is performed in a routine called <strong>ProcessFinish</strong>, which is not yet written.  (For now, we’ll assume that user-level programs do not have any bugs.)




When <strong>ProcessFinish</strong> is implemented in a later project, it will need to return the <strong>ProcessControlBlock</strong>

(PCB) to the free pool.  It will also need to free any additional resources held by the process, such as <strong>OpenFile</strong> objects.  Of course, any open files will need to be closed first.  Finally, <strong>ProcessFinish</strong> will call <strong>ThreadFinish</strong> and will not return.




(Note that a <strong>Thread</strong> object cannot be added back to the free thread pool by the thread that is running.  Instead, in <strong>ThreadFinish</strong> the thread is added to a list called <strong>threadsTobeDestroyed</strong>.  Later, after another thread begins executing (in <strong>Run</strong>) the first thing it will do is add any threads on that list back to the free pool by calling <strong>threadManager.FreeThread</strong>.)










<h1>Syscalls</h1>




When a user-level thread executes a syscall instruction, the assembly routine <strong>SyscallTrapHandler</strong> in <strong>Runtime.s</strong> will be invoked.  The assembly routine will then call a KPL routine with the same name.




The assembly routine does not need to save registers because the interrupted code was executing in user mode and the handler will be executed in system mode.




Recall that just before the syscall, the <strong>DoSyscall</strong> routine placed the arguments in the (user) registers <strong>r1</strong>, <strong>r2</strong>, <strong>r3</strong>, and <strong>r4</strong>, with an integer indicating which kernel function is wanted in register <strong>r5</strong>.  The

<strong>SyscallTrapHandler</strong>  assembly routine takes the values from the user registers.  Since it is running in system mode, it must use a special instruction called “readu” to get values from the user registers.  It pushes them on to the system stack so that the high-level routine can access them.  Then it calls the high-level <strong>SyscallTrapHandler</strong>  routine.  When the high-level routine returns, it takes the returned value from the stack and moves it into user register <strong>r1</strong>, using an instruction called “writeu,” and then executes a “reti” instruction to return to the interrupted user-level process.  Execution will resume back in <strong>DoSyscall</strong> directly after the “syscall” instruction.




The high-level routine called <strong>SyscallTrapHandler</strong> simply takes a look at the function code and calls the appropriate routine to finish the work.  For every kind of syscall, there is a corresponding “handler routine” in the OS.




<table width="0">

 <tbody>

  <tr>

   <td width="162">            <strong><u>System call</u></strong></td>

   <td width="212"><strong><u>Handler function in the kernel</u></strong></td>

  </tr>

  <tr>

   <td width="162">               Exit</td>

   <td width="212">Handle_Sys_Exit</td>

  </tr>

  <tr>

   <td width="162">               Shutdown</td>

   <td width="212">Handle_Sys_Shutdown</td>

  </tr>

  <tr>

   <td width="162">               Yield</td>

   <td width="212">Handle_Sys_Yield</td>

  </tr>

  <tr>

   <td width="162">               Fork</td>

   <td width="212">Handle_Sys_Fork</td>

  </tr>

  <tr>

   <td width="162">               Join</td>

   <td width="212">Handle_Sys_Join</td>

  </tr>

  <tr>

   <td width="162">               Exec</td>

   <td width="212">Handle_Sys_Exec</td>

  </tr>

  <tr>

   <td width="162">               Create</td>

   <td width="212">Handle_Sys_Create</td>

  </tr>

  <tr>

   <td width="162">               Open</td>

   <td width="212">Handle_Sys_Open</td>

  </tr>

  <tr>

   <td width="162">               Read</td>

   <td width="212">Handle_Sys_Read</td>

  </tr>

  <tr>

   <td width="162">               Write</td>

   <td width="212">Handle_Sys_Write</td>

  </tr>

  <tr>

   <td width="162">               Seek</td>

   <td width="212">Handle_Sys_Seek</td>

  </tr>

  <tr>

   <td width="162">               Close</td>

   <td width="212">Handle_Sys_Close</td>

  </tr>

 </tbody>

</table>




It is these routines that you will need to implement, in this and other projects.




Note that interrupts will be disabled when the <strong>SyscallTrapHandler</strong> routine begins.  The first thing the high-level routine does is set the global variable <strong>currentInterruptStatus</strong> to DISABLED so that it is accurate.  In fact, all the interrupt and exception handlers begin by setting <strong>currentInterruptStatus</strong> to DISABLED for this reason.




Also note that after the handler routines return to the interrupted routine, interrupts will be re-enabled.  Why?  Because the Status Register in the CPU will be restored as part of the operation of the reti instruction, restoring the interrupt (and paging and system mode) status bits to what they were when the interrupt occurred.  (Note that we do not bother to change <strong>currentInterruptStatus</strong> to ENABLED before returning to user-level code, because any re-entry to the kernel code must be through <strong>SyscallTrapHandler</strong>, or an interrupt or exception handler, and each of these begins by setting <strong>currentInterruptStatus</strong>.)




Implementing the <strong>Shutdown</strong> syscall is straightforward.  The handler should call <strong>FatalError</strong> with the following message:




Syscall ‘Shutdown’ was invoked by a user thread













<h1>The BLITZ Disk</h1>




The BLITZ computer includes a disk which is emulated using a file called DISK on the host computer.

In other words, a write to the BLITZ disk will cause data to be written to a Unix file and a read from the BLITZ disk will cause a read from the Unix file.  The emulator will simulate the delays involved in reading, by taking account of the current (simulated) disk head position.  When the I/O is complete— that is the simulated time when the emulator has calculated the disk I/O will have completed—the emulator causes a <strong>DiskInterrupt</strong> to occur.




To interface with the BLITZ disk, we have supplied a class called <strong>DiskDriver</strong>, which makes it unnecessary for you to write the code that actually reads and writes disk sectors.  You can just use the code in the class <strong>DiskDriver</strong>.  There is only one <strong>DiskDriver</strong> object; it is created and initialized at startup time.




<u>class</u> DiskDriver     <u>superclass</u> Object     <u>fields</u>

…       semToSignalOnCompletion: <u>ptr</u> <u>to</u> Semaphore       semUsedInSynchMethods: Semaphore       diskBusy: Mutex     <u>methods</u>       Init ()

SynchReadSector  (sectorAddr, numberOfSectors, memoryAddr: <u>int</u>)       StartReadSector  (sectorAddr, numberOfSectors, memoryAddr: <u>int</u>,                         whoCares: <u>ptr</u> <u>to</u> Semaphore)

SynchWriteSector (sectorAddr, numberOfSectors, memoryAddr: <u>int</u>)       StartWriteSector (sectorAddr, numberOfSectors, memoryAddr: <u>int</u>,                         whoCares: <u>ptr</u> <u>to</u> Semaphore)   <u>endClass</u>




This class provides a way to read and write sectors <em>synchronously</em> as well as a way to read and write sectors <em>asynchronously</em>.




To perform a disk operation without blocking the calling thread, you can call <strong>StartReadSector</strong> or <strong>StartWriteSector</strong>.  These methods are passed the number of the sector on the disk at which to begin the transfer, the number of sectors to transfer and the location in memory to transfer the data to or from.

These methods are also passed a pointer to a Semaphore; upon completion of the operation (possibly in error!) this semaphore will be signaled with an <strong>Up()</strong> operation.  This is exactly the semaphore that is signaled whenever a <strong>DiskInterrupt</strong> occurs.  So to perform asynchronous I/O, the caller will invoke <strong>StartReadSector</strong> (or <strong>StartWriteSector</strong>) giving it a Semaphore.  Then the caller can either do other stuff, or wait on the Semaphore.




Since it may be a little tricky to manage asynchronous I/O correctly, the <strong>DiskDriver</strong> class also provides a couple of methods to make it easy to do I/O synchronously.




When you call <strong>SynchReadSector</strong> or <strong>SynchWriteSector</strong>, the caller will be suspended and will be returned to only after a successful completion of the I/O.  These routines will deal with transient errors by retrying the operation until it works.  Other errors (such as a bad <strong>sectorAddr</strong> or bad <strong>memoryAddr</strong>) will be dealt with by a call to <strong>FatalError</strong>.




In order to implement these methods, the <strong>DiskDriver</strong> contains a mutex called <strong>diskBusy</strong> and a semaphore called <strong>SemUsedInSynchMethods</strong>.  Each synch method makes sure the disk is not busy with I/O from some other thread and, if so, waits until it is completed.  This is the purpose of the <strong>diskBusy</strong> mutex.  After acquiring the lock, each synch method will call <strong>StartReadSector</strong> (or <strong>StartWriteSector</strong>) supplying the semaphore.  The synch method will then wait until the disk operation is complete.  The calling thread will remain blocked for the duration.










<h1>The “Stub” File System</h1>




As a later project, you might want to implement a full file system, more like the one used by Unix systems.  For now, you are supplied with a very minimal file system, called the “stub” file system.  In Unix, directories are structured in a tree shape and there are lots of complexities concerning how files are stored on the disk.




In the stub file system, the disk will contain only one directory, and several files.  The directory is limited in size to one sector and is kept in sector 0 of the disk.  The exact number of files that can be accommodated depends on how long the file names are.




Each file has a name and a file length (in bytes).  Each file is stored on disk in a sequence of consecutive sectors.  Once a file is placed on the disk, and more files are added after it, it is impossible to increase the size of the file.  Each file is allocated an integral number of sectors.  (Since the last sector in each file may be only partially full, it would be possible to increase the size of a file up to the next sector boundary.  However, it is not worth the effort.  Instead, the solution is to design a better file system!)




For now, the directory is read-only, so files may not be created and the size of files may not be changed.




The classes <strong>FileManager</strong>, <strong>FileControlBlock</strong>, and <strong>OpenFile</strong> are provided for you, to make it easier to use the file system from within the kernel.










<h1>The “diskUtil” Tool</h1>




The BLITZ tool called <strong>diskUtil</strong> can be used to create a file system on the BLITZ disk, to add files to the disk, to remove files, and to print out the directory.




The BLITZ DISK is organized as follows.  The disk contains a single directory and this is kept in sector 0.  The files are placed sequentially on the disk, one after the other.  Each file will take up an integral number of sectors.  Each file has an entry in the directory.  Each entry contains




<ul>

 <li>The starting sector</li>

 <li>The file length, in bytes (possibly zero)</li>

 <li>The number of characters in the file name</li>

 <li>The file name</li>

</ul>




The directory begins with three numbers:




<ul>

 <li>Magic Number (0x73747562 = “stub”)</li>

 <li>Number of files (possibly zero)</li>

 <li>Number of the next free sector</li>

</ul>




These are followed by the entries for each file.




Once created, a BLITZ file may not have its size increased.  When a file is removed, the free sectors become unusable; there is no compaction or any attempt to reclaim the lost space.




Each time the <strong>diskUtil</strong> program is run, it performs one of the following functions:




<strong>Initialize</strong>          set up a new file system on the BLITZ disk

<table width="0">

 <tbody>

  <tr>

   <td width="120">      <strong>List</strong></td>

   <td width="313">list the directory on the BLITZ disk</td>

  </tr>

  <tr>

   <td width="120">      <strong>Create</strong></td>

   <td width="313">create a new file of a given size</td>

  </tr>

  <tr>

   <td width="120">      <strong>Remove</strong></td>

   <td width="313">remove a file</td>

  </tr>

  <tr>

   <td width="120">      <strong>Add</strong></td>

   <td width="313">copy a file from Unix to BLITZ</td>

  </tr>

  <tr>

   <td width="120">      <strong>Extract</strong></td>

   <td width="313">copy a file from BLITZ to Unix</td>

  </tr>

  <tr>

   <td width="120">      <strong>Write</strong></td>

   <td width="313">write sectors from a Unix file to the BLITZ disk</td>

  </tr>

 </tbody>

</table>




The following command line options tell which function is to be performed by <strong>diskUtil</strong>:




<strong>  -h </strong>

Print help info.




<strong>  -d  DiskFileName </strong>

The file used to emulate the BLITZ disk.  If missing, “DISK” will be used.




<h2>  -i</h2>

Initialize the file system on the BLITZ “DISK” file.  This will         effectively remove all files on the BLITZ disk and reclaim all available         space.




<strong>  -l </strong>

List the directory on the BLITZ disk.




<h2>  -c  BlitzFileName  SizeInBytes</h2>

Create a file of the given size on the BLITZ disk.  The BLITZ         disk must not already contain a file with this name.  Only the         directory will be modified; the actual data in the file will be         whatever bytes happened to be on the disk already.




<strong>  -r  BlitzFileName </strong>

Remove the file with the given name from the directory on the BLITZ disk.




<h2>  -a  UnixFilename  BlitzFileName</h2>

Copy a file from Unix to the BLITZ disk.  If BlitzFileName already         exists, it must be large enough to accommodate the new data.




<h2>  -e  BlitzFileName  UnixFileName</h2>

Extract a file from the BLITZ disk to Unix.  This command will copy         the data from the BLITZ disk to a Unix file.  The Unix file may or may         not already exist; its size will be shortened or lengthened as necessary.




<h2>  -w  UnixFileName  SectorNumber</h2>

The UnixFileName must be an existing Unix file. The SectorNumber is an         integer.  The Unix file data will be  written to the BLITZ disk, starting         at sector SectorNumber.  The directory will not be modified.




We are providing a DISK file which should be large enough, but if you want, you may create a new BLITZ disk file of a different size.  The new disk file must also be initialized properly; it can be created and initialized with the <strong>format</strong> command in the BLITZ emulator.  For example:




% <strong><u>blitz</u> </strong>…  &gt; <strong><u>format</u></strong>  …

The name of the disk file is “DISK”.

The file “DISK” did not previously exist.  (It could not                                          be opened for reading.)  Enter the number of tracks (e.g., 1000; type 0 to abort):

<strong><u>3</u> </strong>

…

Initializing sectors 0 through 47…

Successful completion.




Next, we use <strong>diskUtil</strong> to create a file system, add several files, and print the directory, by typing these commands at the Unix prompt:




% <strong><u>diskUtil –i</u></strong>

% <strong><u>diskUtil -a temp1 MyFileA</u></strong>

% <strong><u>diskUtil -a temp2 MyFileB</u></strong>

% <strong><u>diskUtil -a MyProgram MyProgram</u></strong>

% <strong><u>diskUtil –l</u></strong>

StartingSector   SizeInSectors    SizeInBytes        FileName

==============   =============    ===========    =====================

<ul>

 <li>1 8192            &lt; directory &gt;</li>

 <li>1 8192            MyFileA</li>

 <li>3 17000           MyFileB</li>

</ul>

5               8               60264           MyProgram




<h1>The FileManager</h1>

There is only one <strong>FileManager</strong> object; it is created and initialized at startup time.

We are supplying several methods to help you access files on the “stub” file system; these methods are located in this class.  You’ll need to know how to access files in order to create the first user-level process.  You’ll need to open the executable file, read the bytes from disk, then close the file.  You’ll also need to use the <strong>fileManager</strong> when you implement the <strong>Exec</strong> syscall.

Some of the following material pertains more to the next project than this project.  Read it all now to get familiar with the framework.  You may want to review it again during the next project.




Associated with the <strong>FileManager</strong> class, there are two other classes called <strong>FileControlBlock</strong> and

<strong>OpenFile</strong>.  These two classes contain fields, but do not contain many methods of their own (besides <strong>Init()</strong> and <strong>Print()</strong> methods).  Instead, most of the work associated with the file system is done by the <strong>FileManager</strong> methods.




The <strong>FileControlBlock</strong> (<strong>FCB</strong>) objects and the <strong>OpenFile</strong> objects are limited resources.  The

<strong>FileManager</strong> maintains a free list for each of these, as well as code to allocate new <strong>FCB</strong> objects and new <strong>OpenFile</strong> objects and maintain the free lists.




The <strong>FileManager</strong> also deals with opening files.  This involves finding the file in the file system, that is, determining the file’s location on disk.  In the “stub” file system this is pretty simple since there is only one directory and it fits into a single sector.  The <strong>FileManager</strong>—as programmed now—reads the directory sector (sector 0) into a frame as part of the <strong>FileManager.Init</strong> method.  Subsequent attempts to open a file require no disk accesses.  (Of course, for a “real” file system, things won’t be so simple!)

<h1>FileControlBlock (FCB) and OpenFile</h1>

The semantics of files in the kernel you are building will be similar to the semantics of files in Unix.

Consider the case where one process has opened a file and does a kernel call to read, say, 10 bytes.  The kernel must read the appropriate sector, extract the 10 bytes out of that sector, and finally copy those 10 bytes into the process’s virtual memory space.  This requires the kernel to maintain a frame of memory to use as a buffer; the sector will be read into this buffer by the OS.




If the 10 bytes happen to span the boundary between sectors, the kernel must read both sectors in order to complete the <strong>Read</strong> syscall.  And of course, during the I/O operations other threads must be allowed to run.




Now consider what happens when a process wants to write, say, 20 bytes to a file.  The kernel will need to bring in the appropriate sector and copy the 20 bytes from the process’s virtual address space to the buffer.  Should the kernel write the buffer back to disk immediately?  No; it is likely that the process will want to write some more bytes to that very same sector, so it is more efficient to leave the sector in memory.

When should the kernel write the sector back to disk?  When the process closes the file, the kernel must write it back.  Also, other I/O operations on the file may need different sectors, so the kernel should write the sector back to disk when the buffer is needed for another sector.  However, if the buffer has not been modified, then there is no need to write it back to the disk.  Therefore, we associate a Boolean called <strong>bufferIsDirty</strong> with each buffer frame.  When a buffer is first read in from disk, it is considered to be “clean,” but after any operation modifies the buffer, it should be marked “dirty.”

Next consider the case in which two processes have both opened the same file.  (Let’s call them processes “A” and “B.”)  Any update by process A must be immediately visible to process B.  If process A writes to a file and B reads from that same file, even before A has closed the file, then B should see the new data.  Since the kernel may not actually write to the disk for a long time after process A does the write, it means that processes A and B must share the buffer.




Also, when one process finally closes a file, the buffer must be written back to the disk.  The guarantee the kernel makes is that once we return from a call to <strong>Sys_Close</strong>, the disk has been updated.  The program can stop worrying about failures, etc., and can tell the user that it has completed its task.  Any changes the program has made—even if the system crashes in the next instance—will be permanent and will not be lost.  After a <strong>Sys_Close</strong>, the kernel must not return to the user-level program until the buffer (or all buffers, if there are more than one) is written to the disk successfully.




The purpose of a <strong>FileControlBlock</strong> (FCB) is to record all the data associated with a single file.  This includes the buffer and the <strong>bufferIsDirty</strong> bit.  Here is the definition of FCB:




<u>class</u> FileControlBlock     <u>superclass</u> Listable     <u>fields</u>       fcbID: <u>int</u>

numberOfUsers: <u>int</u>             — count of OpenFiles pointing here       startingSectorOfFile: <u>int</u>      — or -1 if FCB not in use       sizeOfFileInBytes: <u>int</u>

bufferPtr: <u>int</u>                 — addr of a page frame       relativeSectorInBuffer: <u>int</u>    — or -1 if none

bufferIsDirty: <u>bool</u>            — Set to true when buffer is modified     <u>methods</u>       Init ()       Print ()     <u>endClass</u>




A small number FCBs are preallocated and kept in a table called <strong>fcbTable</strong>, which is maintained by the <strong>FileManager</strong>.  The <strong>FileManager</strong> is responsible for allocating new <strong>FileControlBlock</strong> objects and for returning unused <strong>FileControlBlock</strong> objects to a free pool called <strong>fcbFreeList</strong>.




The <strong>startingSectorOfFile</strong> tells where the file is located on the disk.  Since all the sectors in a file are contiguous, the starting address and the length are all we need.  The meaning of <strong>sizeOfFileInBytes</strong> is… well, obvious.  [Descriptive variable names like we tend to use are a HUGE help in understanding and reading code!]  A single memory frame is allocated for each FCB at kernel startup time and <strong>bufferPtr</strong> is set to point to that memory region.  <strong>relativeSectorInBuffer</strong> tells which sector of the file is currently in the buffer and is –1 if there is no valid data in the buffer.




Next consider a process “A” that has opened a file.  All of the “read” and “write” operations that the user-level process executes are relative to a “current position” in the file.  Several processes may have the same file open.  All processes that have file “F” open will share a single FCB.  However, they will each have a different “current position” in the file.




To handle the current position, we have the class <strong>OpenFile</strong>, which is defined as:




<u>class</u> OpenFile     <u>superclass</u> Listable     <u>fields</u>

kind: <u>int</u>                      — FILE, TERMINAL, or PIPE       currentPos: <u>int</u>                — 0 = first byte of file       fcb: <u>ptr</u> <u>to</u> FileControlBlock   — null = not open

numberOfUsers: <u>int</u>             — count of Processes pointing here     <u>methods</u>       Print ()

ReadBytes (targetAddr, numBytes: <u>int</u>) <u>returns</u> <u>bool</u>        — true=All Okay

ReadInt () <u>returns</u> <u>int</u>

LoadExecutable (addrSpace: <u>ptr</u> <u>to</u> AddrSpace) <u>returns</u> <u>int</u>  — -1 = problems   <u>endClass</u>




Like the FCBs, there is a preallocated pool of <strong>OpenFile</strong> objects, which are created at system startup time.  The <strong>FileManager</strong> is responsible for allocating new <strong>OpenFile</strong> objects and for returning unused <strong>OpenFile</strong> objects to a free pool called <strong>openFileFreeList</strong>.




When process “A” opens a file, a new <strong>OpenFile</strong> object must be allocated and made to point to an FCB describing the file.  If there is already an FCB for that file, then the <strong>OpenFile</strong> should be made to point to it; otherwise, we’ll have to get a new FCB, check the directory, and set up the FCB.




When do we return an FCB to the free pool?  When there are no more <strong>OpenFiles</strong> using it.  This is the reason we have a field called <strong>numberOfUsers</strong> in the FCB.  This field is a “reference counter.”  It tells the number of <strong>OpenFile</strong> objects that point to the FCB.  When a new <strong>OpenFile</strong> is allocated and made to point to an FCB, the count must be incremented.  When an <strong>OpenFile</strong> is closed, the count should be decremented.  When the count becomes zero, the FCB must be returned to the free pool.




When a process is terminated, for example due to an error such as an <strong>AlignmentException</strong>, the kernel must close any and all <strong>OpenFiles</strong> the process is using.  The process may explicitly close an <strong>OpenFile</strong> with the <strong>Close</strong> syscall.  Once a file is closed, the process should attempt no further I/O on the file and if the process does, the kernel should catch it and treat it as an error (by returning an error code from the <strong>Sys_Read</strong> or <strong>Sys_Write</strong> kernel call).




Our file I/O will follow the semantics of Unix.  When a process is cloned with the <strong>Fork</strong> syscall, all open files in the parent process must be shared with the child process.  Consider what happens when a parent and a child are both writing to the same file, which was originally opened in the parent.  Since both processes share the <strong>OpenFile</strong> object, they will share the current position.  If the child writes 5 bytes, the current position will be incremented by 5.  Then, if the parent writes 13 bytes, these 13 bytes will follow the 5 bytes written by the child.




In order to implement these semantics, it will be possible for several PCBs to point to the same

<strong>OpenFile</strong> object.  We need to maintain a reference count for the <strong>OpenFiles</strong>, just like the reference count for the FCBs.  Whenever a process opens a file, we need to allocate a new <strong>OpenFile</strong> object and set its count to 1.  Whenever a process forks, we’ll need to increment the count.  When a process closes a file (either by invoking the <strong>Close</strong> syscall or by dying), we’ll need to decrement the count.  If the count goes to zero, we’ll need to return the <strong>OpenFile</strong> to the free pool and decrement the count associated with the FCB.




User-level processes must not be allowed to use pointers into kernel memory and cannot be allowed to touch kernel data structures such as <strong>OpenFiles</strong> and <strong>FCBs</strong>.  So how does a user process refer to an <strong>OpenFile</strong> object?  Indirectly, through an integer.  Here’s how it works.




Each <strong>Process</strong> will have a small array of pointers to <strong>OpenFiles</strong> called <strong>fileDescriptor</strong>.




<u>class</u> ProcessControlBlock

…     <u>fields </u>

…       fileDescriptor: <u>array</u> [MAX_FILES_PER_PROCESS] <u>of</u> <u>ptr</u> <u>to</u> OpenFile     <u>methods</u>

…   <u>endClass</u>




When a process invokes the <strong>Open</strong> syscall, a new <strong>OpenFile</strong> will be set up.  Then the kernel will select an unused position in this array and make it point to the <strong>OpenFile</strong>.  For example, positions 0, 1, and 2

might be in use, so the kernel may assign a file descriptor of 3 for the newly opened file.  The kernel must make <strong>fileDescriptor[3]</strong> point to the <strong>OpenFile</strong> and should return “3” as the <strong>fileDescriptor</strong> to the user-level process.




When the user-level process wants to do an I/O operation, such as <strong>Read</strong>, <strong>Write</strong>, <strong>Seek</strong>, or <strong>Close</strong>, it must supply the <strong>fileDescriptor</strong>.  The kernel must check that (1) this number is a valid index into the array, and (2) the array element points to a valid <strong>OpenFile</strong>.  When closing the file, the kernel will need to decrement the reference count for the <strong>OpenFile</strong> object and also set <strong>fileDescriptor[3]</strong> to null.  Then, if the user process attempts any future I/O operations with file descriptor 3, the kernel can detect that it is an error.




Since user-level file I/O will not be implemented in this project, you will not need to worry about <strong>fileDescriptors</strong> yet.




When a user-level program does a <strong>Read</strong> or <strong>Write</strong> syscall—in Unix or in our OS—the data may be transferred from/to either

<ul>

 <li>a file on the disk</li>

 <li>an I/O device such as a keyboard or display (these are called “special files” in Unix)       another process, via a “pipe”</li>

</ul>

In all three cases, an <strong>OpenFile</strong> object will be used.  The field called <strong>kind</strong> tells whether the object corresponds to a <strong>FILE</strong>, the <strong>TERMINAL</strong>, or a <strong>PIPE</strong>.  In this project, we will only use <strong>OpenFiles</strong> to perform the <strong>Exec</strong> syscall, so the kind will be only <strong>FILE</strong> (and not <strong>TERMINAL</strong> or <strong>PIPE</strong>).










<h1>To Read in an Executable File</h1>




To read in an executable file from disk, your code will need to:




<ul>

 <li>Open the file</li>

 <li>Invoke <strong>LoadExecutable</strong> to do the work</li>

 <li>Close the file</li>

</ul>




Read through the code for <strong>FileManager.Open</strong>:




<u>method</u> Open (filename: String) <u>returns</u> <u>ptr</u> <u>to</u> OpenFile




<strong>Open</strong> is passed a <strong>ptr to array of char</strong>; this is the name of the file on the BLITZ disk that you want to read from.  It will allocate a new <strong>OpenFile</strong> object and a new <strong>FCB</strong> object and set them up.  Then it will return a pointer to the <strong>OpenFile</strong> object, which you’ll use when calling <strong>LoadExecutable</strong>.  If anything goes wrong, <strong>Open</strong> returns <strong>null</strong>.  The only real danger is getting the filename wrong.




In BLITZ, like Unix, executable files have rather complex format.  For details, you can read through the document titled “<em>The Format of BLITZ Object and Executable Files</em>.”  So that you don’t have to write all this code, we are providing a method called <strong>OpenFile.LoadExecutable</strong>:




<u>method</u> LoadExecutable (addrSpace: <u>ptr</u> <u>to</u> AddrSpace) <u>returns</u> <u>int</u>




Look through <strong>LoadExecutable</strong>; it will




<ul>

 <li>Create a new address space (by calling <strong>GetNewFrames</strong>)</li>

 <li>Read the executable program into the new address space</li>

 <li>Determine the starting address (the initial program counter, also called the “entry point”)</li>

 <li>Return the entry point</li>

</ul>




If there are any problems with the executable file, this method will return –1.  Otherwise it will return the entry point of the executable.  This is the address (in the logical address space) at which execution should begin.  Normally, this will be 0x00000000.










<h1>User-Level Processes</h1>




Each user-level process will have a single thread which will normally execute in User mode, with “paging” turned on and interrupts enabled.




Each user-level process will have a logical address space, which will consist of




<ul>

 <li>A Page for “environment” data</li>

 <li>Pages for the text segment</li>

 <li>Pages for the data segment</li>

 <li>Pages for the BSS segment</li>

 <li>Pages for the user’s stack</li>

</ul>




These are shown in order, with the stack pages in the highest addresses of the logical address space.




The environment page will sit at address 0 and will contain information that the OS wishes to pass to a new user-level process.  This includes userID, working directory, etc.  We will not use an environment page, so the text pages will begin at address 0.




<strong>Kernel.h</strong> contains this:




const

NUMBER_OF_ENVIRONMENT_PAGES = 0

USER_STACK_SIZE_IN_PAGES = 1        MAX_PAGES_PER_VIRT_SPACE = 20




The text pages contain the program and any constant values.




The data pages will contain the static (global) program variables.




The BSS pages will contain space for uninitialized program variables (such as large arrays).  The OS will set all bytes in the BSS pages to zero.  Most KPL programs do not use a BSS segment, so there will usually be zero BSS pages.




The user-level program will have a stack, which will grow downward.  Each logical address space will have a predetermined small number of pages (in our case, this is one page) set aside for its stack.  In Unix, if a user process’s stack grows beyond its initial allocation, more stack pages would be added.  In our OS, if a user process’s stack grows beyond this, it will begin overwriting the BSS and data pages, and the program will probably get an error of some sort soon thereafter.




As an example, a program might use:




0 environment pages

2 text pages

1 data page

<ul>

 <li>BSS pages</li>

 <li>stack page</li>

</ul>




This process’s logical address space will have 4 pages.  Each page has PAGE_SIZE bytes (8 Kbytes), so the entire address space will be 32 Kbytes.  Any address between 0x00000000 and 0x00007FFF (which is 32K-1 in hex) would be legal for this program.  If the program tries to use any other address, a PageInvalid Exception will occur.




In Unix, the environment and text pages would be marked read-only and any attempt to update bytes in those pages would cause an exception.  In this project, all pages of the logical address space will be read-write, so our OS will not be able to catch that sort of error in the user-level program.




Each page in the logical address space will be stored in one frame in memory.  The frames do not have to be contiguous and the pages may be stored in pretty much any order.  However, all pages will be in memory throughout the process’s lifetime.




The page table will keep track of where each page is kept.  While the process is executing, “paging” will be turned on so that the memory management unit (MMU) will translate all logical addresses into physical addresses.  Our example program will not be able to read or write anything outside of its 4 pages.




There may be several processes in the system at any time.  Each <strong>ProcessControlBlock</strong> contains an <strong>AddrSpace</strong>, which tells how many pages the process’s address space has and which frame in physical memory holds each page.




When some process (call it “P”) is ready to be scheduled and given a time-slice, the MMU will be need to be set up so that it points to the page table for process P.  You can do this with the method:




AddrSpace.SetToThisPageTable ()




which calls an assembly routine to load the MMU registers.  This method must be invoked before paging is turned on.  When paging is turned off (i.e., whenever kernel code is being executed), the MMU registers are ignored.




Note that each thread will have two stacks: a user stack and a system stack.  We have already seen the system stack; it is used when one kernel function calls another kernel function.  The user stack will be used when the thread is running in user mode.  The system stack, which is fairly small, normally contains nothing while the user-level program is running.  In other words, the system stack is completely empty.




After the user-level program begins executing, execution can re-enter the kernel in only through exception processing.  That is, the only ways to get back into the kernel are:




<ul>

 <li>an interrupt,</li>

 <li>a program exception, or</li>

 <li>a syscall</li>

</ul>




In each of these cases, the exact same thing happens: some information is pushed onto the system stack, the mode is changed to system mode, paging is turned off, and a jump is made to a kernel “handler” routine.




The BLITZ computer has two sets of registers: one for user-mode code and one for system-mode code.  Thus, the user registers do not need to be saved, unless the kernel will switch to another thread.  This is done in the <strong>Run</strong> method, which contains this code:




<u>if</u> prevThread.isUserThread

SaveUserRegs (&amp;prevThread.userRegs[0])       <u>endIf</u>

…

Switch (prevThread, nextThread)

…

<u>if</u> currentThread.isUserThread

RestoreUserRegs (&amp;currentThread.userRegs[0])         currentThread.myProcess.addrSpace.SetToThisPageTable ()       <u>endIf</u>




If the kernel handler code wishes to return to the same user-level code that was interrupted, it can merely return to the assembly language handler routine, which will perform a “reti” instruction.  The user registers and the MMU registers will (presumably) be unchanged, so when the mode reverts to “user mode” and the paging reverts to “paging enabled,” the user-level program will resume execution with the same values in the user registers and the same logical address space.










<h1>Creating a User-Level Process</h1>




The <strong>main</strong> function calls function <strong>InitFirstProcess</strong>, which you must implement.  The first thing you’ll need to do is get a new thread object by invoking <strong>GetANewThread</strong>.  Since the <strong>InitFirstProcess</strong> function should return, you cannot use the current thread.  Next you’ll need to initialize the thread and invoke <strong>Fork</strong> to start it running.  (You can name this new thread something like “UserProgram,” but the name is only used in the debugging printouts.)




The new thread should execute the <strong>StartUserProcess</strong> function, which will do the remainder of the work

in starting up a user-level process.  <strong>InitFirstProcess</strong> can supply a zero as an argument to <strong>StartUserProcess</strong> and can return after forking the new thread.




The first thing you’ll need to do in <strong>StartUserProcess</strong> is allocate a new PCB (with <strong>GetANewProcess</strong>) and connect it with the thread.  So initialize the <strong>myThread</strong> field in the PCB and the <strong>myProcess</strong> field in the current thread.




Next, you’ll need to open the executable file.  It is acceptable to “hardcode” the filename (e.g., “TestProgram1”) into the call to <strong>Open</strong>, although changing the name of the initial process will require a recompile of the kernel.  If there are problems with the <strong>Open</strong>, this is a fatal, unrecoverable error and the kernel startup process will fail.




Next, you’ll need to create the logical address space and read the executable into it.  The method <strong>OpenFile.LoadExecutable</strong> will take care of both tasks.  If this fails, the kernel cannot start up.  <strong>LoadExecutable</strong> returns the entry point, which you might call <strong>initPC</strong>.




Don’t forget to close the executable file you opened earlier, or else a system resource will be permanently locked up.




Next, you’ll need to compute the initial value for the user-level stack, which you might call

<strong>InitUserStackTop</strong>.  It should be set to the logical address just past the end of the logical address space, since the initial push onto the user stack will first decrement the top pointer.  The logical address space starts at zero.  The logical address space contains




addrSpace.numberOfPages




pages.  Each page has size <strong>PAGE_SIZE</strong> bytes.




The <strong>StartUserProcess</strong> function will end by jumping into the user-level program.  This is a one way jump; execution will never return.  (Instead, if the user-level program needs to re-enter the kernel, it will execute a syscall).  As such, nothing on the system stack will ever be needed again.  We want to have a full-sized system stack available for processing any syscalls or interrupts that happen later, so you need to reset the system stack top pointer, effectively clearing the system stack.




You might call the new value <strong>initSystemStackTop</strong>.  You’ll need to set it to:




&amp; currentThread.systemStack[SYSTEM_STACK_SIZE-1]




Next, you’ll need to turn this thread into a user-level thread.  This involves these actions:




<ol>

 <li>Disable interrupts</li>

 <li>Initialize the page table registers for this logical address space</li>

 <li>Set the <strong>isUserThread</strong> variable in the current thread to true</li>

</ol>




<ol start="4">

 <li>Set system register r15, the system stack top</li>

 <li>Set user register r15, the user stack top</li>

 <li>Clear the System mode bit in the condition code register to switch into user mode</li>

 <li>Set the Paging bit in the cond. code register, causing the MMU to do virtual memory mapping</li>

 <li>Set the Interrupts Enabled bit in the cond. code register, so that future interrupts will be handled</li>

 <li>Jump to the initial entry point in the program</li>

</ol>




Recall that every thread begins life with interrupts enabled, so your <strong>StartUserProcess</strong> function will be executing with interrupts enabled.  The first step is to disable interrupts, since there are possible race conditions with steps (2) and (3).




[What is the race problem?  Consider what happens if a context switch (i.e., timer interrupt) were to occur between setting the page table registers and setting <strong>isUserThread</strong> to true.  Look at the <strong>Run</strong> method.  The MMU registers would be changed for the other process; then when this thread is once again scheduled, the code in <strong>Run</strong> will see <strong>isUserThread</strong>==false so it will not restore the MMU registers.  Merely swapping the order of steps (2) and (3) results in a similar race condition.]




The first 3 steps can be done in high-level KPL code, but steps (4) through (9) must be done in assembly language.




Read through the <strong>BecomeUserThread</strong> assembly routine in the file <strong>Switch.s</strong>., which will take care of steps (4) through (9).  <strong>StartUserProcess</strong> should end with a call to this routine:




BecomeUserThread (initUserStackTop, initPC, initSystemStackTop)




The <strong>BecomeUserThread</strong> will change the mode bits and perform the jump “atomically.” This must be done atomically since the target jump address is a logical address space.  (The way it does this is a little tricky: it pushes some stuff onto the system stack to make it look like syscall or interrupt has occurred, and then executes a “reti” instruction.)




<strong>BecomeUserThread</strong> jumps to the user-level<strong> main</strong> routine and never returns.










<h1>Approach to Implementing the Exec Syscall</h1>




The sequence of steps in <strong>InitFirstProcess</strong> and <strong>StartUserProcess</strong> is very similar to what you’ll need when implementing the <strong>Exec</strong> syscall.  You should be able to copy much of this code when implementing <strong>Sys_Handle_Exec</strong>.




One difference is that during an <strong>Exec</strong>, you already have a process and a thread, so you will not need to allocate a new <strong>ProcesControlBlock</strong>, allocate a new <strong>Thread</strong> object, or do a fork.  However, you will have to work with two virtual address spaces.  The <strong>LoadExecutable</strong> method requires an empty <strong>AddrSpace</strong> object; it will then allocate as many frames as necessary and initialize the new address space.




Unfortunately, <strong>LoadExecutable</strong> may fail and, if so, your kernel must be able to return to the process that invoked <strong>Exec</strong> (with an error code, of course).  So you better not get rid of the old address space until after the new one has been initialized and you can be sure that no more errors can occur.




One approach is to create a local variable of type <strong>AddrSpace</strong>.  Don’t allocate it on the heap, just use something like:




var newAddrSpace: AddrSpace = new AddrSpace




Then, after the new address space has been set up, you can copy it into the <strong>ProcessControlBlock</strong>, e.g.,




currentThread.myProcess.addrSpace = newAddrSpace




Don’t forget to free the frames in the previous address space first, or else valuable kernel resources will remain forever unavailable and the kernel will eventually freeze up!




Another tricky thing is copying the filename string from a virtual address space into the kernel address space where it can be used.  The<strong> filename</strong> argument is a virtual address, but since the kernel is running in <strong>Handle_Sys_Exec</strong>, paging will be turned off.




You’ll need to copy the characters into an array variable, not something newly allocated on the heap.  It is okay to put a maximum size on this array and then check that it is not exceeded.  In fact, there is a constant in <strong>Kernel.h</strong> for this purpose:




const

MAX_STRING_SIZE = 20




(In a real OS, the maximum string size would be much larger or even nonexistent.  Here, we use a small size to make testing the limits easier.)




Note that the filename pointer is virtual address, which must be translated into a physical address; you can’t just use it, as is.  This requires some code to perform the page table lookup in software.

Furthermore, since the filename string is in virtual space, it may cross page boundaries.  (In fact, the test program contains cases where this happens!)




Dealing with the filename is fairly complex, but it turns out that we are giving you a method




GetStringFromVirtual (kernelAddr: String, virtAddr, maxSize: int) returns int




which will do most of the work.  (<strong>GetStringFromVirtual</strong> calls <strong>CopyBytesFromVirtual</strong> to do the copying.)  The <strong>GetStringFromVirtual</strong> method can be used like this:




var     strBuffer: array [MAX_STRING_SIZE] of char   …

i = currentThread.myProcess.addrSpace.GetStringFromVirtual (

&amp; strBuffer,              filename asInteger,              MAX_STRING_SIZE)   if i &lt; 0     …error…

endIf




You might think of allocating a temporary buffer on the heap, but remember that we do not want to allocate anything on the heap after kernel start-up.




[ Recall that the “alloc” expression in KPL always allocates bytes on the heap.  Once the kernel has booted and is running, you must avoid further allocations.  Why?  One problem is <em>automatic garbage collection</em> like you see in Java; we can’t use automatic garbage collection since it would produce unpredictable delays and might cause the kernel to miss interrupts or, in the case of a real-time system, miss deadlines.  Also, there is the possibility that the heap might fill up, and dealing with a “heap full” error in the kernel is difficult.  Another option might be to try to manage the heap without automatic garbage collection, but years of C++ experience has taught everybody that this is very difficult to do correctly.  This explains why we have gone to the trouble to create classes like <strong>ThreadManager</strong> and <strong>ProcessManager</strong>, instead of simply allocating new <strong>Thread</strong> and <strong>ProcessControlBlock</strong> objects. ]










<h1>AllocateRandomFrames</h1>




The <strong>main</strong> function includes a function named <strong>AllocateRandomFrames</strong>, which is aimed only at catching bugs in the kernel.  This function will allocate every other frame in the physical memory and never release them, creating a “checkerboard pattern” in memory.  Henceforth, no two pages will ever be allocated to contiguous page frames.




Large, multi-byte chunks of data in the user-level process’s address space will occasionally span page boundaries.  Since these pages may not be in adjacent frames, your kernel will have to be careful about moving data to and from user space.  What may appear to the user-level program as a string of adjacent bytes may in fact be spread all over memory.




Some of the user-level syscalls pass pointers to the kernel.  For example, <strong>Open</strong> passes a pointer to a string of characters.  Keep in mind that this pointer is a logical address, not a physical address.  As such, you cannot simply use the pointer as is.  Take a look at these methods <strong>AddrSpace</strong>:




CopyBytesFromVirtual (kernelAddr, virtAddr, numBytes: <u>int</u>) <u>returns</u> <u>int</u>

CopyBytesToVirtual (virtAddr, kernelAddr, numBytes: <u>int</u>) <u>returns</u> <u>int</u>

GetStringFromVirtual (kernelAddr: String, virtAddr, maxSize: <u>int</u>) <u>returns</u> <u>int</u>




An invocation of <strong>AllocateRandomFrames</strong> has been added just after the <strong>FrameManager</strong> is initialized.

Please leave this in and do not modify the <strong>AllocateRandomFrames</strong> routine.





