# OS development on legacy-free x86 hardware

This repository contains resources for developing an OS on legacy-free x86 hardware.
The x86 platform was introduced in 1978 and still, after 40 years, maintains backwards compatibility.
This has left the platform riddled with legacy hardware interfaces, which are no longer used by modern OS.
However, many OS development resources and tutorials on x86 still discuss these legacy devices, leaving the reader with an incoherent view of the x86
 hardware.
The purpose of this living document is to show how to program the latest generation of hardware with an aim to provide readers with an overview of OS development on legacy-free x86 hardware.

If you find errors or missing details in this document, please send a pull request!

## Bootstrap

**Booting.** The x86 CPU is in legacy 8086 mode after CPU reset for backward compatibility reasons.
[Intel Minimal Boot Loader] documents the various steps needed to boot on x86 CPU.
The firmware or a bootloader performs many of the early steps, but the OS also needs to perform some of the later steps, such as interrupt configuration, timer set up, and so on.

**Bootloaders.** OS typically starts execution via a bootloader, which has already performed some bootstrap steps for us.
The [Multiboot2] specification defines how to lay out an OS image so that bootloaders, such as [GNU GRUB](https://www.gnu.org/software/grub/), can load them.
Please note that older versions of the Multiboot specification, discussed in many OS development resources, are _incompatible_ with Multiboot2.

## Interrupts

Interrupts are a mechanism to preempt the CPU to notify about events such as faults and I/O.
There are three types of interrupts: exceptions (*processor-generated interrupts*), traps (*software-generated interrupts*), and external interrupts (*hardware-generated interrupts*).

**Interrupt handlers.** On x86, you use the IDT data structure, discussed in Chapter 6 ("Interrupt and Exception Handling) of the [Intel SDM], on the CPU to map all types of interrupts to a software interrupt handler.

**Interrupt controller.** The interrupt controller, discussed in Chapter 10 ("Advanced Programmable Interrupt Controller (APIC)") of the [Intel SDM] and the [Intel x2APIC] specification, enables the CPU to receive external interrupts.

**Interrupt delivery.** For interrupt delivery, you use MSI-X, which is a message-signaled interrupt delivery mechanism.
The machine architecture independent parts of MSI-X are discussed in Section 6.8 ("Message Signaled Interrupts") of the [PCI] specification and the x86 specific parts in Section 10.11 ("Message Signalled Interrupts") of the [Intel SDM].

Interrupt handling on x86 is discussed, in the context of FreeBSD, in [Baldwin07].

## I/O Buses

I/O buses connect the CPU to hardware devices.

**PCI Express (PCIe).** The PCI Express bus, discussed in [PCIe], is the main high-speed I/O bus on x86. PCIe is the latest generation of the PCI bus variants.

## Virtualization

**I/O paravirtualization.** The default paravirtualized I/O devices on x86 are specified by [VIRTIO 1.0]. Please note that the VIRTIO 1.0 interfaces (referred to as *modern*) are incompatible with the older de facto standard VIRTIO interfaces (referred to as *legacy*).

## References

* Intel® 64 Architecture x2APIC Specification ([Intel x2APIC])
* Intel® 64 and IA-32 Architectures Software Developer’s Manual Volume 3 (3A, 3B, 3C & 3D): System Programming Guide ([Intel SDM])
* Minimal Intel Architecture Boot Loader ([Intel Boot])
* Multiboot2 Specification version 2.0 ([Multiboot2])
* PCI Express® Base Specification Revision 3.0 ([PCIe])
* PCI Interrupts for x86 Machines under FreeBSD by John H. Baldwin, BSDCan 2007 ([Baldwin07])
* PCI Local Bus Specification Revision 3.0 ([PCI])
* Virtual I/O Device (VIRTIO) Version 1.0 ([VIRTIO 1.0])

[Baldwin07]: https://people.freebsd.org/~jhb/papers/bsdcan/2007/article.pdf
[Intel Minimal Boot Loader]: https://www.intel.co.uk/content/www/uk/en/intelligent-systems/intel-boot-loader-development-kit/minimal-intel-architecture-boot-loader-paper.html
[Intel SDM]: https://software.intel.com/en-us/download/intel-64-and-ia-32-architectures-sdm-combined-volumes-3a-3b-3c-and-3d-system-programming-guide
[Intel x2APIC]: https://www.naic.edu/~phil/software/intel/318148.pdf
[Multiboot2]: https://www.gnu.org/software/grub/manual/multiboot2/multiboot.html
[PCI]: https://www.xilinx.com/Attachment/PCI_SPEV_V3_0.pdf
[PCIe]: http://composter.com.ua/documents/PCI_Express_Base_Specification_Revision_3.0.pdf
[VIRTIO 1.0]: http://docs.oasis-open.org/virtio/virtio/v1.0/virtio-v1.0.html

## License

This work is distributed under the [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).
