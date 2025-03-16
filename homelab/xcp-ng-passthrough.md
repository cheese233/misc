---
title: XCP-ng PCI Passthrough
parent: HomeLab Setup
---

```bash
$ lspci
# ...
00:02.0 VGA compatible controller: Intel Corporation TigerLake-H GT1 [UHD Graphics] (rev 01) # find your device and copy the code ahead
# ...
$ xl pci-assignable-add 00:02.0

$ xl pci-assignable-list # check if the device reassignable
0000:00:02.0
$ xe vm-param-set other-config:pci=0/0000:00:02.0 uuid=... # and mount!
```
<a href="https://wiki.xenproject.org/wiki/Xen_PCI_Passthrough" target="_blank">^</a>

---