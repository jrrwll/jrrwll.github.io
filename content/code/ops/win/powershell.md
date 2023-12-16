
---
title: "Windows Powershell"
---

## config programmably

**lock screen via cli on win**

```powershell
Process.Start(@"C:\WINDOWS\system32\rundll32.exe", "user32.dll,LockWorkStation");
```

```csharp
using System.Runtime.InteropServices;
[DllImport("user32.dll")]
public static extern bool LockWorkStation();

[DllImport("user32.dll")]
public static extern int ExitWindowsEx(int uFlags, int dwReserved);

ExitWindowsEx(0, 0);
```