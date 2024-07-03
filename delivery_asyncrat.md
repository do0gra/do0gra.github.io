---
layout: post
---

[back](./)

# Delivery and installation of a AsyncRAT 

In this blog post, I demonstrate how I analyse a html file and understand how a AsyncRAT tool would be loaded from it.

The sample can be obtained from [VirusTotal](https://www.virustotal.com/gui/file/8e872d7047689c2e6a3880f6a3f4627544148f43e4aa5e167c1d518c4beb9c3a). SHA1:b37d96bf1552affcdf367641fb2b541f34cc26a1

## Analysis of HTML file

Opening up the HTML file shows a prompt for a password. Most of the messages are in French. Keying in any string and clicking on the button downloads a file named "Avis de paiement.PDF.zip". The bytes of the file differs for different strings as the password, with the "right" password producing a zip file as inferred by the output file name.

![ss1](/assets/images/delivery_async/ss1.png)

Examining the source code of the HTML, theres a large blob of data that seems to be base64 encoded and also hinted by the variable name. 

![ss2](/assets/images/delivery_async/ss2.png)

The next part shows that the base64 data is decoded and AES decrypted via function calls in the CryptoJS library before finally writing out to the file "Avis de paiement.PDF.zip".

![ss3](/assets/images/delivery_async/ss3.png)

The right zip file contains a vbs script, which will be analysed next.

## Analysis of VBS script

There are comments written in french, perhaps translating these to english makes it easier for us to understand the functionality of the script.

Before translation:
![ss4_1](/assets/images/delivery_async/ss4_1.png)

After translation:
![ss4_2](/assets/images/delivery_async/ss4_2.png)

A number of large hexadecimal strings are found in the script. 

![ss5](/assets/images/delivery_async/ss5.png)

![ss6](/assets/images/delivery_async/ss6.png)

Intuitively, these hexadecimal values seem to binary data. To find out how the hexadecimal values are used, we continue examining the rest of the script.

The entry point is the call to the "Initialiser" function, which contain calls to two functions: "SaveInRegistry" and "CreateANdWriteFileScript"

![ss7](/assets/images/delivery_async/ss7.png)

### SaveInRegistry Function

![ss8](/assets/images/delivery_async/ss8.png)

Zooming in functions called:
![ss8_1](/assets/images/delivery_async/ss8_1.png)
![ss8_2](/assets/images/delivery_async/ss8_2.png)
![ss8_3](/assets/images/delivery_async/ss8_3.png)

From the comments and function name, we roughly understand that this function saves values to the registry. A detailed description below - 
Writes the following data to the respective subkeys:
* Contents of dataRegister to subkey "v"
* 0 to subkey "b" 
* "650594173" to subkey "path"
* "AddInProcess32.exe" to subkey "i"
* Reverses one of the hexadecimal values defined earlier, breaks it into segments and save into "Software\\650594173\\Segments"
* Depending on .NET framework version detected, writes to subkey "r" and "s" hexdecimal values defined earlier

At this point, we see how the hexadecimal values defined before are used here. Since we've know that these values are written to the registry.

### CreateAndWriteFileScript Function

![ss9](/assets/images/delivery_async/ss9.png)

Zooming in functions called:
![ss10](/assets/images/delivery_async/ss10.png)

From the comments and function name, this function creates and writes to a script. A detailed description below - 
* Creates a scheduled task with task name "650594173" and action pointing to "%UserProfile%\\650594173.js", to repeat action every 1 minute with starting time "2024-02-20T00:00:00"
* If file exist, delete it
* Writes the hexadecimal defined in the function and write to the location, "%UserProfile%\\650594173.js"

It is revealed here that the hexadecimal value defined in the function is a hexadecimal representative of a javascript, which we will look at later.

### Summary of VBS script

1. Writes to registry a number of strings, including binary data encoded as hexadecimal values
2. Creates a javascript and scheduled task that runs the script

We can confirm the above by executing the vbs script in a virtual machine.

Registry Entries
![ss11](/assets/images/delivery_async/ss11.png)
![ss12](/assets/images/delivery_async/ss12.png)

Scheduled Task Creation
![ss12_1](/assets/images/delivery_async/ss12_1.png)
![ss12_2](/assets/images/delivery_async/ss12_2.png)

## Analysis of Javascript

Moving on to the javascript that was created "650594173.js", notice that the comments, function and variable names are also written in french. The functionality of the script can be understood by looking at keywords. 

Tracing the execution flow of the script:

Entry point with first function call. Reads value from registry subkey "i" that was created by VBS script and passes this in to "verifierProcessus" function. This value was the string "AddInProcess32.exe".
![ss13](/assets/images/delivery_async/ss13.png)

Uses WMI to check if process "AddInProcess32.exe" is running. Returns true if running, otherwise, false.
![ss14](/assets/images/delivery_async/ss14.png)

Executes Powershell, searches for the powershell process before calling the next function "envoyerCommandesPourArreterConhost"
![ss15](/assets/images/delivery_async/ss15.png)

Read the value from registry subkey "v" (created by VBS script), passes to the powershell process, enters and sleeps. The "v" value contains powershell commands the powershell command 
```ps
{[}AppDomain{]}::CurrentDomain.Load{(}[Convert{]}::FromBase64String{(}{(}-join {(}Get-ItemProperty -LiteralPath 'HKCU:\Software\650594173' -Name 's'{)}.s | ForEach-Object {$_{[}-1..-{(}$_.Length{)}{]}}{)}{)}{)}; {[}a.a{]}::a{(}'650594173'{)}".
``` 

After that, the string "Stop-Process -Name conhost -Force" is passed to the powershell process and enters. 
![ss16](/assets/images/delivery_async/ss16.png)

Notice that the powershell "loads" the base64-decoded reversed value from the 's' subkey (One of the hexadecimal strings defined in VBS script). It then calls a function in 'a'. But what is 'a'? The next step would be to dump out the data that was loaded for analysis.

### Summary of Javascript
The script checks if a "AddInProcess32.exe" process is running, if it is sleep for 10 seconds before checking again. Otherwise, run powershell with commands that decodes data from registry subkey 's' and loads the result in memory. Calls function 'a'.

## Analysis of "s" Binary Data
The decoded "s" binary data can be extracted out by following the operation performed in the powershell command. That is taking the value in the "s" registry subkey, reversing the value and base64 decode it. 

![ss17](/assets/images/delivery_async/ss17.png)

PE detective shows the binary as a .NET file, now we can decompile it using a .NET decompiler (eg. dnSpy) and examine the source code. We now know that the powershell script actually calls the 'a' function from the 'a' namespace after loading it, passing a string '650594173'.
![ss18_1](/assets/images/delivery_async/ss18_1.png)

![ss18](/assets/images/delivery_async/ss18.png)
"etape 1 passed lol" translates to "step 1 passed lol"
The function loads another binary data that is read from the subkey 'r', and proceeds to call the function 'o' in the 'o' namespace. Herein, we shall name this as stage1.

## Analysis of "r" Binary Data
Drawing experience from analysis of "s" binary data, the decoded "r" binary data can be obtained by following the operation taken in stage1 - Reverse the data in "r" subkey and convert from hexadecimal and save it as a file. Thowing this file into dnSpy reveals this to be another .NET binary.

![ss19](/assets/images/delivery_async/ss19.png)

Function 'o' defined here. Checks for the .NET version present, taking the latest version (v4.0) if multiple versions exist and finds the "AddInProcess.exe" in the .NET folder. The function EPP.EP is a call to perform in-memory loading and running of PE executable files. So zooming in the parameters of the function call, we have the process path of 'AddInProcess32.exe' from the .NET folder and 'bytes' returned by a function 'GetBytes' which is left unknown.
![ss20](/assets/images/delivery_async/ss20.png)

Function 'GetBytes' retrieve data from the 'segments' key, concatenates the 'segment' subkey and returns the result. 
![ss21](/assets/images/delivery_async/ss21.png)

Data from 'segments' key in the registry as shown here.
![ss22](/assets/images/delivery_async/ss22.png)

Concatenating the segment data gives the PE executable that is loaded.

## Last Steps
Decompiling this shows signs of a RAT tool. 
![ss23](/assets/images/delivery_async/ss23.png)

Googling the name "Async RAT", the first result brought my attention.
https://github.com/NYAN-x-CAT/AsyncRAT-C-Sharp/tree/master

The folder structure in the github repository matches the decompiled structure.
![ss23_1](/assets/images/delivery_async/ss23_1.png)

The configured settings are AES decrypted during runtime.
![ss24](/assets/images/delivery_async/ss24.png)

The encrypted settings from the sample.
![ss25](/assets/images/delivery_async/ss25.png)

The default settings as it appear in the repository
![ss26](/assets/images/delivery_async/ss26.png)

This AsyncRAT sample can also be found on [VirusTotal](https://www.virustotal.com/gui/file/760e4c092ea836527d7e87ab4cf5c1b9ff8c91672840d1365109da149a984efe) SHA1:760e4c092ea836527d7e87ab4cf5c1b9ff8c91672840d1365109da149a984efe