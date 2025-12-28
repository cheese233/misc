---
title: XCP-ng PCI Passthrough
parent: HomeLab Setup
---

```bash
$ xe pci-list
# ...
uuid ( RO)           : 17b62764-f979-7226-111c-deea6f9b530e
    vendor-name ( RO): Intel Corporation
    device-name ( RO): TigerLake-H GT1 [UHD Graphics]
         pci-id ( RO): 0000:00:02.0
# ...

# !!! On XCP-ng 8.2:
# $ xl pci-assignable-add 00:02.0
# upd: on XCP-ng 8.3:
$ xe pci-disable-dom0-access uuid=17b62764-f979-7226-111c-deea6f9b530e

$ xl pci-assignable-list # check if the device reassignable
0000:00:02.0

$ xe vm-param-set other-config:pci=0/0000:00:02.0 uuid=... # and mount!

```
[^1] [^2]

---

[^1]: https://wiki.xenproject.org/wiki/Xen_PCI_Passthrough

[^2]: https://docs.xcp-ng.org/compute/