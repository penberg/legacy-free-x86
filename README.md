# OS development on legacy-free x86 hardware

This repository contains resources for developing an OS on legacy-free x86 hardware.
The x86 platform was introduced in 1978 and still, after 40 years, maintains backwards compatibility.
This has left the platform riddled with legacy hardware interfaces, which are no longer used by modern OS.
However, many OS development resources and tutorials on x86 still discuss these legacy devices, leaving the reader with an incoherent view of the x86
 hardware.
The purpose of this living document is to show how to program the latest generation of hardware with an aim to provide readers with an overview of OS development on legacy-free x86 hardware.

If you find errors or missing details in this document, please send a pull request!

## Interrupts

Interrupts are a mechanism to preempt the CPU to notify about events such as faults and I/O.
There are three types of interrupts: exceptions (*processor-generated interrupts*), traps (*software-generated interrupts*), and external interrupts (*hardware-generated interrupts*).

**Interrupt handlers.** On x86, you use the IDT data structure, discussed in Chapter 6 ("Interrupt and Exception Handling) of the [Intel SDM], on the CPU to map all types of interrupts to a software interrupt handler.

**Interrupt controller.** The interrupt controller, discussed in Chapter 10 ("Advanced Programmable Interrupt Controller (APIC)") of the [Intel SDM] and the [Intel xAPIC2] specification, enables the CPU to receive external interrupts.

**Interrupt delivery.** For interrupt delivery, you use MSI-X, which is a message-signaled interrupt delivery mechanism.
MSI-X is discussed in Section 10.11 ("Message Signalled Interrupts") of the [Intel SDM].

## References

* Intel® 64 and IA-32 Architectures Software Developer’s Manual Volume 3 (3A, 3B, 3C & 3D): System Programming Guide ([Intel SDM])
* Intel® 64 Architecture x2APIC Specification ([Intel x2APIC])

[Intel SDM]: https://software.intel.com/en-us/download/intel-64-and-ia-32-architectures-sdm-combined-volumes-3a-3b-3c-and-3d-system-programming-guide
[Intel x2APIC]: https://www.naic.edu/~phil/software/intel/318148.pdf

## License

This work is distributed under the [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).
