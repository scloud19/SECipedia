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

                            Ex: A process is running in 6mode but the time slice is up.  What occurs?
                                1) The CPU interrupts the current process based on an internal timer, switches into kernel mode, and hands control back to the kernel.

                                2) The kernel records the current state of the CPU and memory.
                                    When it's time to resume the interrupted process, this information will be utilized to get everything running again.

                                3) Now that the kernel has control, it performs any tasks that might of come up during the previous time slice.
                                    Ex: Collecting I/O, etc.

                                4) The kernel is now ready to let another process execute.  Here, the kernel looks at the list of processes that are ready to run and chooses one.
                                    Q: how does this choosing process occur?

                                5) Kernel prepares the memory (and CPU) for this new process.
                                    Ex: The kernel will tell the CPU, "this is how long the time slice will last"

                                6) To begin execution, the kernel switches the CPU into user mode.  Thus, the kernel gives control of the CPU to the process.

                                When does the kernel run?
                                    Between process time slices during a context switch

                                Multi-CPU considerations
                                    More complicated because the kernel doesn't need to give away control of its current CPU in order to allow a process to run on a different CPU.
                                        Typically, the kernel does so anyway to maximize the usage of all available CPUs


                        These "time slices" are so small that the system appears to be MULTITASKING (running processes at the same time)





        Memory management
            Keeps track of all memory, how it's shared between processes, etc.

            Specifically, the kernel manages memory during the context switch in which these must be taken into account
                The kernel must have its own private area in memory that user processes can't access
            
                Each user process needs its own section of memory
                
                One user process may not access the private memory of another process

                If requested, user processes can share memory in a common, “public” area.
                    This request comes from the kernel in the form of a system call.



                Some memory in the user processes can be read-only

                The system can use more memory than is physically present by using disk space.


            MMU (Memory Management Unit)
                Is housed inside modern CPU's

                Translates virtual memory addresses used by processes into real ones.

                The kernel assists the MMU by breaking the memory used by processes into smaller chunks called pages

                The kernel maintains a data structure, called a page table, that contains a mapping of a processes' virtual page addresses to a real page addresses in memory

                As a process accesses memory, the MMU translates the virtual addresses used by the process into real addresses based on the kernel's page table

                A user process doesn't actually need all of its pages to be immediately available in order to run.  The kernel generally loads and allocates pages as a process needs them.  This is known as on-demand paging or just demand paging.


                Enables virtual memory
                    A memory access scheme that is implemented using hardware and software.

                    Maps "virtual addresses" (memory addresses used by a program) into "physical addresses" in computer memory

                    Inside a program, it's "virtual address space" is seen as a continuous "chunk" of memory.  The program doesn't need to worry about where other process' are using memory.
                        From the program's perspective, it's the only process on the machine



                    When a process is using virtual memory it doesn't access the memory by its physical location in the hardware. Instead, the kernel sets up each process to act as if it had an entire machine to itself.

        How a program starts and runs as a new process
            1) The kernel loads the beginning of the program's instruction code into memory pages

            2) The kernel may allocate some working-memory pages to the new process.

            3) As the process runs, it might reach a point where the next instruction in its code isn't in any of the pages that the kernel initially loaded.  At this point, a "page fault" is triggered by the process and the kernel takes over.  The kernel then loads the necessary pages into memory, and then lets the program resume execution.
                If the program requires more working memory than was initially allocated, the kernel handles it by finding free pages (or by making room) and assigning them to the process.

            Page Fault
                If a memory page is not ready when a process wants to use it, the process triggers a page fault.

                When this occurs, the kernel takes control of the CPU from the process in order to get the page ready.

                Types: Minor and Major
                    Minor Page Faults
                        Occurs when the desired page is actually in main memory but the MMU doesn't know where it is.

                        This can occur when the process requests more memory or when the MMU doesn't have enough space to store all of the page locations for a process.
                            For this situation, the kernel can tell the MMU about the page and will then permit the process to continue.

                        These aren't a very big deal and occur often
                            Unless maximum performance is needed, this shouldn't be a large concern.

                    Major Page Faults
                        Occurs when the desired memory page isn't in main memory at all.
                            In these cases, the kernel must load it from the disk (a slower storage mechanism)

                        These can be problematic for performance reasons because they require a large amount of kernel computation.
                            In other words, when the kernel is spending a lot of time providing pages, other processes are on "hold" and the system slows down.

                        Some are unavoidable
                            Ex: When you load the code from disk when running a program for the first time.

                        To page faults that need to be avoided
                            When you start running out of memory and the kernel starts to swap pages of working memory out to the disk in order to make room for the needed pages.


                Minor Page Faults






        Device Drivers
            The "middle man" between the hardware and process.

        System calls and support
            processes use system calls to communicate with the kernel.





