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
        Processes management
            The traffic cop!
                "Which processes are allowed to use the CPU?"

            The starting, pausing, resuming, and terminating of processes.

            In an OS, many processes run "simultaneously" (think of two apps running at the same time)
                However, this is an illusion and often these proccesses do not run at exactly the same time

                Ex: In a one-core CPU, multiple processes can use the CPU in a timeframe, but only one process may actually use the CPU at a GIVEN time.
                    1 process utilizes the CPU for a number of ms (a time slice), then it's paused.  At this pause, a "context switch" started and the another process uses the CPU for a few ms, etc.
                        Time slice
                            Enough time for a significant computation

                            A single sliice is usually enough for a process to finish its current task.
                        Context switch
                            That act of one process giving control of the CPU to another

                            The kernel's responsibility.

                            Ex: A process is running in user mode but the time slice is up.  What occurs?
                                1) The CPU interrupts the current process based on an internal timer, switches into kernel mode, and hands control back to the kernel.

                                2) The kernel records the current state of the CPU and memory.

                                3) Now that the kernel has control, it performs any tasks that might of come up during the previous time slice.
                                    Ex: Collecting I/O, etc.

                                4) The kernel is now ready to let another process execute.  Here, the kernel looks at the list of processes that are ready to run and chooses one.
                                    Q: how does this choosing process occur?

                                5) Kernel prepares the memory (and CPU) for this new process.
                                    Ex: The kernel will tell the CPU, "this is how long the time slice will last"

                                6) To begin execution, the kernel switches the CPU into user mode.  Thus, the kernel gives control of the CPU to the process.

                                When does the kernel run?
                                    Between process time slices during a context switch

                                CURRENTLY AT

                                TODO: Bring up threads, what are cores, etc.  Read wikipedia on linx kernal, etc.





                        These "time slices" are so small that the system appears to be MULTITASKING (running processes at the same time)





        Memory management
            Keeps track of all memory, how it's shared between processes, etc.

        Device Drivers
            The "middle man" between the hardware and process.

        System calls and support
            processes use system calls to communicate with the kernel.





