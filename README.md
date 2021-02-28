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

## Firmware and configuration

The UEFI specification defines the interface between firmware and the OS. The ACPI specification defines interface for machine configuration discovery.

## Interrupts

### Overview

Interrupts are a mechanism to preempt the CPU to notify about events such as faults and I/O.
There are three types of interrupts: exceptions (*processor-generated interrupts*), traps (*software-generated interrupts*), and external interrupts (*hardware-generated interrupts*).
For a summary of interrupt handling on x86 is discussed, in the context of FreeBSD, see [Baldwin07].

**Interrupt handlers.** On x86, you use the IDT data structure, discussed in Chapter 6 ("Interrupt and Exception Handling) of the [Intel SDM], on the CPU to map all types of interrupts to a software interrupt handler.

**Interrupt controller.** The interrupt controller, discussed in Chapter 10 ("Advanced Programmable Interrupt Controller (APIC)") of the [Intel SDM] and the [Intel x2APIC] specification, enables the CPU to receive external interrupts.

**Interrupt delivery.** For interrupt delivery, you use MSI-X, which is a message-signaled interrupt delivery mechanism.
The machine architecture independent parts of MSI-X are discussed in Section 6.8 ("Message Signaled Interrupts") of the [PCI] specification and the x86 specific parts in Section 10.11 ("Message Signalled Interrupts") of the [Intel SDM].

### MSI-X

The OS probes and configures MSI-X support for a PCI function via the PCI configuration space using the MSI-X capability structure.
The MSI-X PCI capability structure is identified by capability ID `0x11` specified by Section 6.8.2.1 ("Capability ID for MSI-X ") in [PCI].
The capability structure has a Message Control register and pointers to MSI-X Table and Pending Bit Array (PBA) structures.
The Message Control register, specified by Section 6.8.2.3 ("Message Control for MSI-X") of [PCI],  has fields for enabling MSI-X support, masking/unmasking all function interrupt vectors and determining the size of MSI-X table.
The MSI-X Table entries configure per-interrupt vector message address and, data and masking.
The format of the message address and data fields are specific to a machine architecture.
For x86, the [Intel SDM] specifies their format as part of x2APIC.
The masking field enables or disables a specific interrupt vector.
Finally, the PBA structure provides information on what messages are pending for a given interrupt vector.

## I/O Buses

I/O buses connect the CPU to hardware devices.

**PCI Express (PCIe).** The PCI Express bus, discussed in [PCIe], is the main high-speed I/O bus on x86. PCIe is the latest generation of the PCI bus variants.

**Input-output memory management unit (IOMMU).** On Intel platform, IOMMU is part of the [Virtualization Directed I/O] capability.

## Virtualization

**I/O paravirtualization.** The default paravirtualized I/O devices on x86 are specified by [VIRTIO 1.0]. Please note that the VIRTIO 1.0 interfaces (referred to as *modern*) are incompatible with the older de facto standard VIRTIO interfaces (referred to as *legacy*).

## References

* [ACPI Specification](https://uefi.org/specifications)
* Intel® 64 Architecture x2APIC Specification ([Intel x2APIC])
* Intel® 64 and IA-32 Architectures Software Developer’s Manual Volume 3 (3A, 3B, 3C & 3D): System Programming Guide ([Intel SDM])
* Minimal Intel Architecture Boot Loader ([Intel Boot])
* Multiboot2 Specification version 2.0 ([Multiboot2])
* PCI Express® Base Specification Revision 3.0 ([PCIe])
* PCI Interrupts for x86 Machines under FreeBSD by John H. Baldwin, BSDCan 2007 ([Baldwin07])
* PCI Local Bus Specification Revision 3.0 ([PCI])
* Virtual I/O Device (VIRTIO) Version 1.0 ([VIRTIO 1.0])
* [UEFI Specification](https://uefi.org/sites/default/files/resources/UEFI%20Spec%202.8B%20May%202020.pdf)

[Baldwin07]: https://people.freebsd.org/~jhb/papers/bsdcan/2007/article.pdf
[Intel Minimal Boot Loader]: https://www.intel.co.uk/content/www/uk/en/intelligent-systems/intel-boot-loader-development-kit/minimal-intel-architecture-boot-loader-paper.html
[Intel SDM]: https://software.intel.com/en-us/download/intel-64-and-ia-32-architectures-sdm-combined-volumes-3a-3b-3c-and-3d-system-programming-guide
[Intel x2APIC]: https://www.naic.edu/~phil/software/intel/318148.pdf
[Multiboot2]: https://www.gnu.org/software/grub/manual/multiboot2/multiboot.html
[PCI]: https://www.xilinx.com/Attachment/PCI_SPEV_V3_0.pdf
[PCIe]: http://composter.com.ua/documents/PCI_Express_Base_Specification_Revision_3.0.pdf
[VIRTIO 1.0]: http://docs.oasis-open.org/virtio/virtio/v1.0/virtio-v1.0.html
[Virtualization Directed I/O]: https://software.intel.com/sites/default/files/managed/c5/15/vt-directed-io-spec.pdf

## License

This work is distributed under the [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).
