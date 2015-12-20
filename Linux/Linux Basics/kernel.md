Kernel
    Runs in kernel mode, as opposed to "user mode"
                Kernel mode code has unrestricted access to the processor and main memory.
                    Kernel processes can easily crash the entire system.
    Core of the operating sytem

    Is software residing in memory that tells the CPU what to do.

    Manages the hardware and acts primarily as an interface between the hardware and any running program.

    kernel space
        The area that only the kernel can access 

    Exs: System calls, Process Management, Memory Management, Device Drivers

    Almost everything the kernel does revolves around RAM.

    One of the kernel's tasks is to split RAM into many subdivisions and it must maintain certain state information about those subdivisions at all times.
        Each process gets its own share of memory, and the kernel must ensure that each process keeps to its share.


    The kernel is in charge of managing tasks in four general areas:
        Processes
            The traffic cop!
                Which processes are allowed to use the CPU

        Memory management
            Keeps track of all memory, how it's shared between processes, etc.

        Device Drivers
            The "middle man" between the hardware and process.

        System calls and support
            For communications, processes "call" the kernel through a system call





