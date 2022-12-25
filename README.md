# Operating System Project

## Scheduling and Virtual Memory in xv6:
* This is a simple priority-based scheduler. For all processes contain number of ticket. If a process sets its ticket value to 1 it has **high** priority; otherwise, a process should have a ticket value of 0 and thus have **low** priority. All other ticket values are not valid; **1 (high priority)** should be the default. **High-Priority** processes should always have priority over **Low-Priority** processes. 
* Case1: If there is a single **High-Priority** process and a single **Low-Priority** process, the **High-Priority** one will run to completion, and starving the **Low-Priority** process.
* Case2: If there two or more processes with **Same Priority**, they should alternate **Round-Robin**.

## The objectives for this project:
* To gain futher knowledge of a real kernel, xv6.
* To change that scheduler to a new algorithm.

## Details:
You'll need two new system calls to implement this scheduler. 
* The first is `int settickets(int number)`, which sets the number of tickets of the calling process. By default, each process can raise the number of tickets it receives, and thus receive a higher proportion of CPU cycles. This routine should **return 0** if successful, and **-1** otherwise(if, for example, the caller passes in a number less than one).
* The second is `int getpinfo(struct pstat*)`. This routine returns some information about all running processes, including how many times each has been chosen to run and the process ID of each. You can use this system call to build a variant of the command line program `ps`, which can then be called to see what is going on. The structure `pstat` is defined below; note, you cannot change this structure, and must use it exactly as is. This routine should **return 0** if successful, and **-1** otherwise (if, for example, a **bad** or **NULL** pointer is passed into the kernel).
* Most of the code for the scheduler is quite localized and can be found in **`proc.c`**; the associated header file, **`proc.h`** is also quite useful to examine. To change the scheduler, not much needs to be done; study its control flow then try some small changes.
* You'll need to assign tickets to a process when it is created. Specfically, you'llneed to make sure a child process inherits the same number of tickets as its parents. 
* Finally, you'll need to understand how to fill in the structure `pstat` in the kernel and pass the results to user space. The structure should look like what you see here, in a file you'll have to include called **`pstat.h`**.

**`pstat.h`**:
``` C
#ifndef _PSTAT_H_
#define_PSTAT_H_
#include"param.h"
struct pstat {
    int inuse[NPROC];   // whether this slot of the process table is in use (1 or 0)
    int tickets[NPROC]; // the number of tickets this process has
    int pid[NPROC];     // the PID of each process int ticks[NPROC];   // the number of ticks each process has accumulated 
};
#endif// _PSTAT_H_
```

## Solution:
* Modified **c** file: **`exec.c`**, **`proc.c`**, **`syscall.c`**, **`sysproc.c`**, **`vm.c`**
* Modified **header** file: **`defs.h`**, **`proc.h`**, **`syscall.h`**, **`user.h`**
* Modified **other** file: **`usys.S`**
* Add system call in xv6 [reference website](https://www.geeksforgeeks.org/xv6-operating-system-adding-a-new-system-call/).
    * need to modify **`syscall.h`**, **`syscall.c`**, **`sysproc.c`**, **`usys.S`**, **`user.h`**.
    * add system call `int settickets(int number)`,`int getpinfo(struct pstat *ps)`, `int mprotect(void *addr, int len)`,`int munprotect(void *addr, int len)`
    * modify or add some code to the rest of the modified file.


