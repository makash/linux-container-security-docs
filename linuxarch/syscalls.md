### System Calls

>In computing, a system call is the programmatic way in which a computer program requests a service from the kernel of the operating system it is executed on. This may include hardware-related services (for example, accessing a hard disk drive), creation and execution of new processes, and communication with integral kernel services such as process scheduling. System calls provide an essential interface between a process and the operating system.

Basically system calls allow the Ring 3 processes to do privileged operations. System calls send a software interrupt to the CPU and the Kernel performs the operations required. 

### Interrupts

>In system programming, an interrupt is a signal to the processor emitted by hardware or software indicating an event that needs immediate attention. An interrupt alerts the processor to a high-priority condition requiring the interruption of the current code the processor is executing.

Interrupts allow an immediate request to be processed when the CPU is busy with something else.

### Steps
1. User process needs to perform a privileged action, so initiates an interrupt to the CPU.
2. The interrupt puts the CPU in Ring 0 and passes control to Kernel.
3. The kernel determines if the user process should be granted the system call (based on privileges).
4. If granted, the kernel will execute the system call
5. Once finished, the kernel initiates the change to Ring 3.


