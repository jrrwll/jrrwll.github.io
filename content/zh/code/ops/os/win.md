
---
title: "Windows"
---

## 虚拟机

### 去掉虚拟机标识

```bat
:echo rename PRLS__ to NOBOX_
REG COPY HKLM\HARDWARE\ACPI\DSDT\PRLS__ HKLM\HARDWARE\ACPI\DSDT\NOBOX_ /s
REG DELETE HKLM\HARDWARE\ACPI\DSDT\PRLS__ /f

:modify SystemBiosVersion
REG ADD HKLM\HARDWARE\DESCRIPTION\System /v SystemBiosVersion /t REG_MULTI_SZ /d "NOBOX   - 1\018.0.2 (53077)\0Some EFI x64 18.0.2-53077 - 12CF55\0" /f
REG ADD HKLM\HARDWARE\DESCRIPTION\System /v VideoBiosVersion /t REG_MULTI_SZ /d "" /f
pause
```

## 