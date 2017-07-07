# Introduction to x64 Assembly


## CPU Infrastructure

* Definitions 
	* Byte 8 bits
	* Word 16 bits
	* Double Word 32 bits
	* Quadword 64 bits
	* Double Quadword 128 bits
* Compared to x32
	* Extend `EAX` etc. to `RAX`.
	* Add `R8`, `R9`, ..., `R15`.
	* Address space extends to `2^64-1`.
	* Add XMM registers `XMM8`, ... , `XMM15`.
* General Purpose registers
	* Quadword: `RAX`, `R8`
	* Lower dword: `EAX`, `R8D`
	* Lowest word: `AX`, `R8W`
	* Lowest byte: `AL`, `R8B`
	
> Note that there is no `R8H` cresponding to `AH`.
> 
> An instruction cannot reference `AH`, `BH`, `CH` or `DH` and one of the new byte registers (`R8B`, etc.) at the same time.

* RFLAGS
	* Extend EFLAGS to 64 bits, but the extended part is reserved.
	* CF: operation generates a carry
	* PF: last byte has even number of 1's
	* ZF: result is 0
	* SF: Most significant bit is 1
	* OF: Overflow on signed operation
* Float point unit
	* Each `FPR0` to `FPR7` can store 80-bit, 64-bit and 32-bit floating point number.
	* Share space with 64-bit MMX registers (which operates on integers in parallel).
* Parallel operations with MMX and XXM registers
	* MMX registers allow bytes, words and doublewords operated in paralle.
	* XMM registers allow parallel operations on four single or two double precision values per instruction.
	* XMM registers may also support integer operations.
	* Instructions in Streaming SIMD Extensions (SSE) work on packed byte, word, doubleword, and quadword integers.

* Assemblers
	* NASM
	* YASM (an NASM rewrite)
	* FASM
	* MASM (Microsoft, 64-bit version is ML64)
	* WinASM (Microsoft IDE)

## Instructions

* Indirection addressing
	* `MOV R8W, 1234[8*RAX+RCX]` move the word at address into R8W register
	* RCX: base address
	* RAX: index, multiplied by 1,2,4 or 8
	* 1234: immediate offset

