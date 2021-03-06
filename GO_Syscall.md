The syscall package provides access to the raw system call
interface of the underlying operating system.  Porting Go to
a new architecture/operating system combination requires
some manual effort, though there are tools that automate
much of the process.  The auto-generated files have names
beginning with z.




* asm_${GOOS}_${GOARCH}.s

This hand-written assembly file implements system call dispatch.
There are three entry points:




	func Syscall(trap, a1, a2, a3 uintptr) (r1, r2, err uintptr);
	func Syscall6(trap, a1, a2, a3, a4, a5, a6 uintptr) (r1, r2, err uintptr);
	func RawSyscall(trap, a1, a2, a3 uintptr) (r1, r2, err uintptr);

The first and second are the standard ones; they differ only in
how many arguments can be passed to the kernel.
The third is for low-level use by the ForkExec wrapper;
unlike the first two, it does not call into the scheduler to
let it know that a system call is running.




* syscall_${GOOS}.go

This hand-written Go file implements system calls that need
special handling and lists "//sys" comments giving prototypes
for ones that can be auto-generated.  Mksyscall reads those
comments to generate the stubs.




* syscall_${GOOS}_${GOARCH}.go

Same as syscall_${GOOS}.go except that it contains code specific
to ${GOOS} on one particular architecture.




* types_${GOOS}.c

This hand-written C file includes standard C headers and then
creates typedef or enum names beginning with a dollar sign
(use of $ in variable names is a gcc extension).  The hardest
part about preparing this file is figuring out which headers to
include and which symbols need to be #defined to get the
actual data structures that pass through to the kernel system calls.
Some C libraries present alternate versions for binary compatibility
and translate them on the way in and out of system calls, but
there is almost always a #define that can get the real ones.
See types_darwin.c and types_linux.c for examples.




* zerror_${GOOS}_${GOARCH}.go

This machine-generated file defines the system's error numbers,
error strings, and signal numbers.  The generator is "mkerrors.sh".
Usually no arguments are needed, but mkerrors.sh will pass its
arguments on to godefs.




* zsyscall_${GOOS}_${GOARCH}.go

Generated by mksyscall.pl; see syscall_${GOOS}.go above.




* zsysnum_${GOOS}_${GOARCH}.go

Generated by mksysnum_${GOOS}.




* ztypes_${GOOS}_${GOARCH}.go

Generated by godefs; see types_${GOOS}.c above.
