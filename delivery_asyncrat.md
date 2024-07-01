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

It is revealed here that the hexadecimal value defined in the function is a hexidecimal representative of a javascript, which we will look at later.

### Summary of VBS script

## Analysis of Javascript

### Summary of Javascript

## Analysis of Binary files

### Summary of Binary files



