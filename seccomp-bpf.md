### Seccomp
Seccomp stands for secure computing mode. It's a simple sandboxing tool in the Linux kernel, available since Linux version 2.6.12. When enabling seccomp, the process enters a "secure mode" where a very small number of system calls are available (exit(), read(), write(), sigreturn()). Writing code to work in this environment is difficult; for example, dynamic memory allocation (using brk() or mmap(), either directly or to implement malloc()) is not possible.

### Seccomp-bpf
Seccomp-BPF is a more recent extension to seccomp, which allows filtering system calls with BPF (Berkeley Packet Filter) programs. These filters can be used to allow or deny an arbitrary set of system calls, as well as filter on system call arguments (numeric values only; pointer arguments can't be dereferenced). Additionally, instead of simply terminating the process, the filter can raise a signal, which allows the signal handler to simulate the effect of a disallowed system call (or simply gather more information on the failure for debugging purposes). Seccomp-bpf is available since Linux version 3.5 and is usable on the ARM architecture since Linux version 3.10. Several backports are available for earlier kernel versions.


### How to call Seccomp-bpf?

 #include <sys/prctl.h>
 #include <linux/seccomp.h>
 [...]
 prctl(PR_SET_SECCOMP, SECCOMP_MODE_FILTER, &bpf_prog)