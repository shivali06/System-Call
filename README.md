# System-Call
## Shivali Choudhary

1. Modified arch/x86/entry/syscalls/syscall_64.tbl file and added the below lines.
  -	It is macro is defined for each system call. Added a definition for our new system call. 
  - ```	336     common     addsc     __x64_sys_addsc```
  - 	336 : All syscalls are identified by a unique number. In order to call a syscall, we tell the kernel to call the syscall by its number rather than by its name.
  -	common: The ABI, or application binary interface, to use. Either 64, x32, or common for both. I have used common.
  - Addsc: This is simply the name of the syscall.
  - __x64_sys_addsc: The entry point is the name of the function to call in order to handle the syscall. The naming convention for this function is the name of the syscall prefixed with sys.

2.  modify include/linux/syscalls.h : added below lines. We need to define the function prototype in the include/linux/syscalls.h file.
  - asmlinkage int sys_addsc(int, int);
  - asmlinkage is a modifier. This macro tells the compiler that the function should not expect to find any of its arguments in registers (a common optimization), but only on the CPU’s stack. 
  - int is the written type of the function sys_addsc and we have passes two arguments of type int because we want to add two numbers.

3. Create a new folder and inside file for implementation. For my case, addsc/addsc.c.
  ``` 
  #include <linux/syscalls.h> 
        #include <linux/kernel.h>  
        SYSCALL_DEFINE2(addsc, int, i, int, j) 
        {
          int s = 0;
          printk("Addsc doing its job! summands are  i and  j %d,%d\n", i, j);
          s = (i + j); 
          return s; 
        } 
  ```
        
  - It has near the top #include <linux/syscalls.h> and other #includes for needed headers so you can call kernel functions like printk
  - It uses one of the SYSCALL_DEFINE2 macros to generate the declaration of your system call service routine
  - The definitions of these SYSCALL_DEFINE... macros are in include/linux/syscalls.h. Hence, must #include <linux/syscalls.h>
  - It has within { ... } (after your SYSCALL_DEFINE...(...) ) the code of the body of to be run when syscall is called
  - In kernel/Makefile, added the name of one more object file (addsc.o) in the list of object files that this Makefile directs the kbuild to build and link into the fixed kernel.
  - ``` obj-y := addsc.o ```

4. Created file to Makefile in its directory. For my case, /Makefile.

  - ``` core-y += kernel/ certs/ mm/ fs/ ipc/ security/ crypto/ block/ addsc/ helloworld/ ```
  - This is to tell the compiler that the source files of our new system call are in present in the addsc directory.

5. Finally, compile and install the kernel. Now, you would be able to see new system calls working well.
```
#include <stdio.h> 
#include <linux/kernel.h> 
#include <sys/syscall.h> 
#include <unistd.h>  
int main()
{ 
int i,j; 
printf("Enter two numbers\n"); 
scanf("%d%d",&i,&j); 
long int s = syscall(336,i,j); // called the system call and specified the number.
printf("Adding is working with summand i %d\n and j %d\n with result :%ld\n",i,j,s);
return 0; 
}
```

dmesg command shows me that system calls are working well.

