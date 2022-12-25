# Operating System Project:Scheduling and Virtual Memory in xv6

# Scheduling Part:
* About scheduling, we will implement a simple priority-based scheduler. For all processes contain number of ticket. If a process sets its ticket value to 1 it has **high** priority; otherwise, a process should have a ticket value of 0 and thus have **low** priority. All other ticket values are not valid; **1 (high priority)** should be the default. **High-Priority** processes should always have priority over **Low-Priority** processes. 
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

# Virtual Memory Part:
* About virtual memory, we will have to change xv6 to support a feature virtually every modern OS does: causing an exception to occur when your program **dereferences a null pointer**, and adding the ability to **change the protection levels of some pages in a process's address space**.

## Null-pointer Dereference:
* In xv6, the VM system uses a simple two-level page table as discussed in class. As it currently is structured, user code is loaded into the very first part of the address space. Thus, if you dereference a null pointer, you will not see an exception (as you might expect); rather, you will see whatever code is the first bit of code in the program that is running. Try it and see!
* Thus, the first thing you might do is create a program that dereferences a null pointer. It is simple! See if you can do it. Then run it on Linux as well as xv6, to see the difference.
* Your job here will be to figure out how xv6 sets up a page table. Thus, once again, this project is mostly about understanding the code, and not writing very much. Look at how `exec()` works to better understand how address spaces get filled with code and in general initialized.
* You should also look at `fork()`, in particular the part where the address space of the child is created by copying the address space of the parent. What needs to change in there?
* The rest of your task will be completed by looking through the code to figure out where there are checks or assumptions made about the address space.Think about what happens when you pass a parameter into the kernel, for example; if passing a pointer, the kernel needs to be very careful with it, to ensure you haven't passed it a bad pointer. How does it do this now? Does this code need to change in order to work in your new version of xv6?
* One last hint: you'll have to look at the xv6 makefile as well. In there user programs are compiled so as to set their entry point (where the first instructionis) to 0. If you change xv6 to make the first page invalid, clearly the entry point will have to be somewhere else (e.g., the next page, or 0x1000). Thus, something in the makefile will need to change to reflect this as well.

## Read-only Code:
* In most operating systems, code is marked read-only instead of read-write. However, in xv6 this is not the case, so a buggy program could accidentally overwrite its own text. Try it and see!
* In this portion of the xv6 project, you'll change the protection bits of parts of the page table to be read-only, thus preventing such over-writes, and also be able to change them back.
* To do this, you'll be adding two system calls:  
    * `int mprotect(void *addr,int len)`
    * `int munprotect(void *addr, int len)`
* Calling `mprotect()` should change the protection bits of the page rangestarting at `addr` and of `len` pages to be read only. Thus, the program could still read the pages in this range after `mprotect()` finishes, but a write to this region should cause a trap (and thus kill the process). The `munprotect()` call does the opposite: sets the region back to both readable and writeable.
* Also required: the page protections should be inherited on `fork()`. Thus, if a process has mprotected some of its pages, when the process calls fork, the OS should copy those protections to the child process.
* There are some failure cases to consider: if `addr` is not page aligned, or `addr` points to a region that is not currently a part of the address space, or `len` is less than or equal to zero, **return -1** and do not change anything. Otherwise, **return 0** upon success.
* Hint: after changing a page-table entry, you need to make sure the hardware knows of the change. On 32-bit x86, this is readily accomplished by updating the `CR3` register (what we generically call the page-table base register in class). When the hardware sees that you overwrote `CR3` (even with the samevalue), it guarantees that your `PTE` updates will be used upon subsequent accesses. The `lcr3()` function will help you in this pursuit.

## Handling Illegal Accesses:
* In both the cases above, you should be able to demonstrate what happens when user code tries to **(a) access a null pointer** or **(b) overwrite an mprotectedregion of memory**. In both cases, xv6 should trap and kill the process (this will happen without too much trouble on your part, if you do the project in a sensible way).

## Solution:
* Modified **C** file: **`exec.c`**, **`proc.c`**, **`syscall.c`**, **`sysproc.c`**, **`vm.c`**
* Modified **header** file: **`defs.h`**, **`proc.h`**, **`syscall.h`**, **`user.h`**
* Modified **other** file: **`usys.S`**
* Add system call in xv6 [reference website](https://www.geeksforgeeks.org/xv6-operating-system-adding-a-new-system-call/).
    * need to modify **`syscall.h`**, **`syscall.c`**, **`sysproc.c`**, **`usys.S`**, **`user.h`**.
    * add system call `int settickets(int number)`,`int getpinfo(struct pstat *ps)`, `int mprotect(void *addr, int len)`,`int munprotect(void *addr, int len)`
    * modify or add some code to the rest of the modified file.


