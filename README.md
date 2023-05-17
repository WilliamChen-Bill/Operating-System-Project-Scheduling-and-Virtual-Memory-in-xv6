# My Operating System Project: Innovations in Scheduling and Virtual Memory for xv6

Welcome to my exciting project on the xv6 operating system! Here, I've delved into the intricacies of operating system design, focusing on scheduling and virtual memory. This project will demonstrate my understanding of these complex topics and highlight my ability to effectively modify and improve existing systems.

## Exciting Work on Scheduling:

The first part of my project involves implementing a priority-based scheduler in the xv6 OS. I've chosen to create a ticket-based system to prioritize processes. A process can set its ticket value to 1, thereby marking it as high-priority, or leave it at 0 to be considered low-priority. The design ensures high-priority processes take precedence over low-priority ones, showcasing my understanding of how to manage process prioritization effectively.

Take a look at a couple of cases I've handled:

- When there's a single high-priority process and a single low-priority process, the high-priority process will run to completion, resulting in the starvation of the low-priority process.
- When there are two or more processes with the same priority, I've implemented a round-robin mechanism to ensure fair scheduling.

I've added two new system calls, `settickets(int number)` and `getpinfo(struct pstat*)`, to manage this new scheduling scheme. Additionally, I've ensured that a child process inherits the same number of tickets as its parent, demonstrating my ability to consider important details in system design.

## Intriguing Work on Virtual Memory:

The second part of my project sees me modifying xv6 to support two features common in modern operating systems - triggering an exception when a null pointer is dereferenced and allowing the modification of protection levels of certain pages in a process's address space.

I've implemented a null-pointer dereference exception and added system calls `mprotect(void *addr, int len)` and `munprotect(void *addr, int len)` to change the protection bits of parts of the page table. This prevents programs from accidentally overwriting their own text, showcasing my knowledge of common OS security practices.

## Insights Gained:

Working on this project has given me valuable insights into the workings of a real kernel, xv6, and the opportunity to modify the scheduler with a new algorithm. It has also honed my skills in handling system calls, understanding the control flow, and dealing with user space and kernel space intricacies.

## Important Files and Modifications:

I've made modifications to several C files (`exec.c`, `proc.c`, `syscall.c`, `sysproc.c`, `vm.c`) and header files (`defs.h`, `proc.h`, `syscall.h`, `user.h`). Additionally, I've worked on `usys.S` and included a new file `pstat.h` to define the pstat structure.

This project has been an engaging journey into the depths of operating system design and functionality. Feel free to explore my code and reach out if you have any questions. I'm eager to discuss the interesting challenges and solutions that this project presented me with!
