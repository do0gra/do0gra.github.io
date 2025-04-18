---
layout: post
---

[back](./)

# Deobfuscating Adwind 

This blogpost will be analysing the Adwind sample: https://www.virustotal.com/gui/file/00de57cf3c4496256c82f79b8c97136b434b9037408d94a3f0dc07123914fcf6

# Deobfuscating the strings

Decompiling the JAR file using JD-GUI yields the following:
![obf_1](/assets/images/adwind/obf_1.png)
For some reason, JD-GUI is not able to decompile the java code in the class files. The other class files show no working code.

Using a different JAR decompiler seem to work as the code are visible. There are also a number of resources embedded in the JAR.
![obf_2](/assets/images/adwind/obf_2.png)

This could be an anti-decompilation technique implemented in the malware? Anyways, now that the code is now visible, the next step was to trace the execution flow.

Strings are apparently deobfuscated on runtime with calls to the two functions ??j.??c and ??o.??c. 
![obf_3](/assets/images/adwind/obf_3.png)

??j.??c is defined as the follows:
![obf_4](/assets/images/adwind/obf_4.png)

One quick way to deobfuscate the strings was to create a JAR program, extract the deobfuscation functions and calls to the functions from the decompiled program and inspect the variables while debugging the program.
![deobf_1](/assets/images/adwind/deobf_1.png)

![deobf_2](/assets/images/adwind/deobf_2.png)
Now that the strings are deobfuscated, I then continued to analyse the decompiled program to see how the strings are used.

# Embedded resources

The strings are references back to the embedded resources: "/burn.om", "/snake.green" and "/dragon.fire". The program extracts and decodes the three resources. "/burn.om" and "/snake.green" are used as "secretkeyspec" in var5 to derive the "privatekey" with "/dragon.fire". Including these in the JAR program, it looks something like this.
![deobf_3](/assets/images/adwind/deobf_3.png)

![deobf_4](/assets/images/adwind/deobf_4.png)

The "privatekey" is used to create a new object populating the members "PRIVATE_PASSWORD", "PASSWORD_CRYPTED" and "SERVER_PATH GETPROPERTY"
![deobf_5](/assets/images/adwind/deobf_5.png)

It is not known how this object is used at this moment since the program doesn't reference it anymore.

# More embedded resources

Besides the three embedded resources, more resources are found within the folder path with the JAR "Y/tna" and "Zd/EdG". 
![obf_1_1](/assets/images/adwind/obf_1.png)

The resources are parsed and used similarly to the previous three. "pc.PJx" and "n.gDa" are used in var10 to derive var6 with "jEX.K". To note, var10 is then used to be loaded into memory, implying it to be an JAR executable. 
Using the same approach, I included them in my JAR program. 
![deobf_6](/assets/images/adwind/deobf_6.png)
And dump out var10 into a file.
![deobf_7](/assets/images/adwind/deobf_7.png)

Proceeding to decompile the dumped JAR, a different obfuscation technique is used here.
![deobf_8](/assets/images/adwind/deobf_8.png)

Specific code exists for different OS, the one below shows a function calling wscript in windows.
![deobf_9](/assets/images/adwind/deobf_9.png)

Function calls relating to windows registry.
![deobf_10](/assets/images/adwind/deobf_10.png)

# Deobfuscating the strings in the final payload

Researching on the different obfuscation technique used by Adwind, I came across this blog which seem to show the same kind of obfuscation technique used https://blogs.jpcert.or.jp/en/2016/05/decoding-obfuscated-strings-in-adwind.html

It is also explained that Adwind is typically packed and that its main jar is hidden in the artefacts jar file, which appears to be the same for this sample.

I used the tool provided in the blog https://github.com/JPCERTCC/aa-tools and successfully deobfuscated the strings.
![deobf_11](/assets/images/adwind/deobf_11.png)

![deobf_12](/assets/images/adwind/deobf_12.png)

# Final thoughts
Theres more work to be done, however, I'm just going to stop here. Next steps would include
- Continue analysing the functionality of the final payload (less complicated with the deobfuscated strings)
- Figuring out how the "privatekey" is used
