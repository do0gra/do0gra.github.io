---
layout: post
---

[back](./)

# Delivery and installation of a AsyncRAT 
In this post, we analyze a multi-stage malware delivery chain that begins with a malicious HTML file and ultimately leads to the deployment of **AsyncRAT**, a known remote access trojan. We’ll break down each stage—from initial lure to final payload execution—and demonstrate how script-based loaders are used to bypass detection mechanisms.

Source: [VirusTotal](https://www.virustotal.com/gui/file/8e872d7047689c2e6a3880f6a3f4627544148f43e4aa5e167c1d518c4beb9c3a)
SHA1:b37d96bf1552affcdf367641fb2b541f34cc26a1


## Stage 1: HTML Dropper Analysis

Opening the HTML reveals a password prompt (in French). Entering a string and clicking the button triggers a file download named `Avis de paiement.PDF.zip`. Each input results in different file bytes, suggesting that the correct password dynamically decrypts or generates a valid ZIP file.

![ss1](/assets/images/delivery_async/ss1.png)

Inspecting the HTML source reveals a large base64-encoded blob, likely representing encrypted data.

![ss2](/assets/images/delivery_async/ss2.png)

This blob is then base64-decoded and AES-decrypted using `CryptoJS` functions, after which the output is written as `Avis de paiement.PDF.zip`.

![ss3](/assets/images/delivery_async/ss3.png)

The decrypted ZIP archive contains a VBS script—the next component in the infection chain.

## Stage 2: VBS Script Analysis

The VBS script contains French comments and multiple large hexadecimal strings. Translating the comments helps clarify the script’s behavior.

**Original** vs. **Translated**:
![ss4_1](/assets/images/delivery_async/ss4_1.png)
![ss4_2](/assets/images/delivery_async/ss4_2.png)

Hexadecimal values embedded in the script represent binary content to be written or decoded at runtime.

![ss5](/assets/images/delivery_async/ss5.png)

![ss6](/assets/images/delivery_async/ss6.png)

Intuitively, these hexadecimal values seem to binary data. To find out how the hexadecimal values are used, we continue examining the rest of the script.

### Entry Point: `Initialiser` Function

The `Initialiser` function orchestrates execution via two main subroutines:
* `SaveInRegistry`
* `CreateAndWriteFileScript`

![ss7](/assets/images/delivery_async/ss7.png)

### `SaveInRegistry` Function

![ss8](/assets/images/delivery_async/ss8.png)

Functions called:
![ss8_1](/assets/images/delivery_async/ss8_1.png)
![ss8_2](/assets/images/delivery_async/ss8_2.png)
![ss8_3](/assets/images/delivery_async/ss8_3.png)

This function writes multiple values to the registry:
* Contents of `dataRegister` to subkey `v`
* The value `0` to subkey `b`
* The string `650594173` to subkey `path`
* The filename `AddInProcess32.exe` to subkey `i`
* A reversed hex string, split into segments, saved under `Software\650594173\Segments`
* Binary blobs from predefined hex values are written to `r` and `s`, depending on the .NET version installed

### `CreateAndWriteFileScript` Function

![ss9](/assets/images/delivery_async/ss9.png)

Functions called:
![ss10](/assets/images/delivery_async/ss10.png)

This subroutine sets up persistence:
* Creates a scheduled task named `650594173`, pointing to a JavaScript file located at `%UserProfile%\650594173.js`
* Writes another embedded hexadecimal blob (representing JavaScript code) to that path
* Schedules the task to run every minute starting `2024-02-20T00:00:00`

**Confirmation**: Executing the VBS in a virtual machine shows successful registry entries and task creation.

Registry Entries

![ss11](/assets/images/delivery_async/ss11.png)
![ss12](/assets/images/delivery_async/ss12.png)

Scheduled Task Creation

![ss12_1](/assets/images/delivery_async/ss12_1.png)
![ss12_2](/assets/images/delivery_async/ss12_2.png)


## Stage 3: JavaScript Loader Analysis

The JavaScript file `650594173.js` also contains French variable names and comments. By tracing key operations, we reconstruct its logic:
1. Reads the `i` registry key (`AddInProcess32.exe`) and checks if the process is running via WMI.
2. If not running, launches PowerShell and passes commands to decode and load a payload from registry.

![ss13](/assets/images/delivery_async/ss13.png)
<br>
![ss14](/assets/images/delivery_async/ss14.png)

Executes Powershell, searches for the powershell process before calling the next function `envoyerCommandesPourArreterConhost`
<br>
![ss15](/assets/images/delivery_async/ss15.png)

Read the value from registry subkey `v` (created by VBS script), passes to the powershell process, enters and sleeps. The `v` value contains powershell commands the powershell command 
```ps
{[}AppDomain{]}::CurrentDomain.Load{(}[Convert{]}::FromBase64String{(}{(}-join {(}Get-ItemProperty -LiteralPath 'HKCU:\Software\650594173' -Name 's'{)}.s | ForEach-Object {$_{[}-1..-{(}$_.Length{)}{]}}{)}{)}{)}; {[}a.a{]}::a{(}'650594173'{)}".
``` 

After that, the string `Stop-Process -Name conhost -Force` is passed to the powershell process and enters. 
<br>
![ss16](/assets/images/delivery_async/ss16.png)

The powerhsell pulls base64-encoded, reversed data from subkey `s`, decodes it, loads it as a .NET assembly, and calls a method `a.a::a("650594173")`.


## Stage 4: .NET Payload - Registry Subkey `s`

Using the same PowerShell logic, we extract and reverse the `s` value from the registry, then base64-decode it to get a .NET binary.

![ss17](/assets/images/delivery_async/ss17.png)

PE detective shows the binary as a .NET file, now we can decompile it using a .NET decompiler (eg. dnSpy) and examine the source code. We now know that the powershell script actually calls the `a` function from the `a` namespace after loading it, passing a string `650594173`.

![ss18_1](/assets/images/delivery_async/ss18_1.png)

![ss18](/assets/images/delivery_async/ss18.png)

"etape 1 passed lol" translates to "step 1 passed lol"

The method `a.a()` loading additional data from subkey `r` and invoking `o.o()` from a new assembly.


## Stage 5: .NET Payload - Registry Subkey `r`

Repeating the decoding process for `r`, we retrieve another .NET binary.

![ss19](/assets/images/delivery_async/ss19.png)

![ss20](/assets/images/delivery_async/ss20.png)

Function `GetBytes` retrieve data from the `segments` key, concatenates the `segment` subkey and returns the result. 

![ss21](/assets/images/delivery_async/ss21.png)

Data from `segments` key in the registry as shown here.

![ss22](/assets/images/delivery_async/ss22.png)

Within this next-stage payload:
* The script identifies the highest available .NET version.
* Locates `AddInProcess32.exe` in the .NET directory.
* Uses `EPP.EP()` to inject and execute a PE file in-memory.
* The executable bytes are retrieved from the `Segments` registry key by concatenating its subkeys.


## Final Stage: AsyncRAT Deployment

Decompiling the injected PE reveals characteristics of **AsyncRAT**.
<br>
![ss23](/assets/images/delivery_async/ss23.png)

A quick search confirms the match:  
https://github.com/NYAN-x-CAT/AsyncRAT-C-Sharp/tree/master

The source code and structure in the repository align with what we observed during reverse engineering.

![ss23_1](/assets/images/delivery_async/ss23_1.png)

The malware uses AES decryption at runtime to unpack its configuration.
![ss24](/assets/images/delivery_async/ss24.png)

![ss25](/assets/images/delivery_async/ss25.png)

The default settings as it appear in the repository
<br>
![ss26](/assets/images/delivery_async/ss26.png)

Final Payload Hash:  
**SHA1**: `760e4c092ea836527d7e87ab4cf5c1b9ff8c91672840d1365109da149a984efe`  
**Source**: [VirusTotal](https://www.virustotal.com/gui/file/760e4c092ea836527d7e87ab4cf5c1b9ff8c91672840d1365109da149a984efe)


## Conclusion

This AsyncRAT campaign employs a multi-layered delivery mechanism involving:

1. **HTML Dropper** – Encrypted ZIP payload dynamically generated via AES and base64 decoding.
2. **VBS Script Loader** – Writes registry entries, deploys a JavaScript loader, and sets up persistence.
3. **JavaScript Loader** – Reads and decodes registry-stored binaries, invokes PowerShell.
4. **In-Memory .NET Loaders** – Multi-stage binary execution from registry-encoded data.
5. **Final Payload** – AsyncRAT executed via in-memory PE injection using native .NET components.

The use of registry-stored payloads, obfuscated scripting languages, and native Windows tools highlights a highly evasive and modular malware deployment chain designed for stealth and persistence.