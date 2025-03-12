**Lab2 Work1 System call tracing**
在本作业中，您将添加一个系统调用跟踪功能，该功能可能会在以后调试实验时对您有所帮助。您将创建一个新的`trace`系统调用来控制跟踪。它应该有一个参数，这个参数是一个整数“掩码”（mask），它的比特位指定要跟踪的系统调用。例如，要跟踪`fork`系统调用，程序调用`trace(1 << SYS_fork)`，其中`SYS_fork`是`kernel/syscall.h`中的系统调用编号。如果在掩码中设置了系统调用的编号，则必须修改xv6内核，以便在每个系统调用即将返回时打印出一行。该行应该包含进程id、系统调用的名称和返回值；您不需要打印系统调用参数。trace系统调用应启用对调用它的进程及其随后派生的任何子进程的跟踪，但不应影响其他进程。
hints
- 在Makefile的UPROGS中添加$U/_trace
- 运行make qemu，您将看到编译器无法编译user/trace.c，因为系统调用的用户空间存根还不存在：将系统调用的原型添加到user/user.h，存根添加到user/usys.pl，以及将系统调用编号添加到kernel/syscall.h，Makefile调用perl脚本user/usys.pl，它生成实际的系统调用存根user/usys.S，这个文件中的汇编代码使用RISC-V的ecall指令转换到内核。一旦修复了编译问题（注：如果编译还未通过，尝试先make clean，再执行make qemu），就运行trace 32 grep hello README；但由于您还没有在内核中实现系统调用，执行将失败。
- 在kernel/sysproc.c中添加一个sys_trace()函数，它通过将参数保存到proc结构体（请参见kernel/proc.h）里的一个新变量中来实现新的系统调用。从用户空间检索系统调用参数的函数在kernel/syscall.c中，您可以在kernel/sysproc.c中看到它们的使用示例。
- 修改fork()（请参阅kernel/proc.c）将跟踪掩码从父进程复制到子进程。
- 修改kernel/syscall.c中的syscall()函数以打印跟踪输出。您将需要添加一个系统调用名称数组以建立索引。

**Lab2 Work2 Sysinfo（moderate）**
在这个作业中，您将添加一个系统调用sysinfo，它收集有关正在运行的系统的信息。系统调用采用一个参数：一个指向struct sysinfo的指针（参见kernel/sysinfo.h）。内核应该填写这个结构的字段：freemem字段应该设置为空闲内存的字节数，nproc字段应该设置为state字段不为UNUSED的进程数。我们提供了一个测试程序sysinfotest；如果输出“sysinfotest: OK”则通过。
hints
- 在Makefile的UPROGS中添加$U/_sysinfotest
- 当运行make qemu时，user/sysinfotest.c将会编译失败，遵循和上一个作业一样的步骤添加sysinfo系统调用。要在user/user.h中声明sysinfo()的原型，需要预先声明struct sysinfo的存在：
struct sysinfo;
int sysinfo(struct sysinfo *);
- 一旦修复了编译问题，就运行sysinfotest；但由于您还没有在内核中实现系统调用，执行将失败。
- sysinfo需要将一个struct sysinfo复制回用户空间；请参阅sys_fstat()(kernel/sysfile.c)和filestat()(kernel/file.c)以获取如何使用copyout()执行此操作的示例。
- 要获取空闲内存量，请在kernel/kalloc.c中添加一个函数
- 要获取进程数，请在kernel/proc.c中添加一个函数