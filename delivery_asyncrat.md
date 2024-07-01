---
layout: post
---

[back](./)

# Delivery and installation of a AsyncRAT 

In this blog post, I demonstrate how I analyse a html file and understand how a AsyncRAT tool would be loaded from it.

The sample can be obtained from [VirusTotal](https://www.virustotal.com/gui/file/8e872d7047689c2e6a3880f6a3f4627544148f43e4aa5e167c1d518c4beb9c3a). 

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

![ss4](/assets/images/delivery_async/ss4.png)

A number of large hexadecimal strings are found in the script.

![ss5](/assets/images/delivery_async/ss5.png)

![ss6](/assets/images/delivery_async/ss6.png)
