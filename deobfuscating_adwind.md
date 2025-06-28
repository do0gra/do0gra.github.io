---
layout: post
---

[back](./)

# Inside Adwind: Deobfuscation and Payload Analysis

This post details the deobfuscation and behavioral analysis of an Adwind RAT sample. The sample can be found on VirusTotal:  
 https://www.virustotal.com/gui/file/00de57cf3c4496256c82f79b8c97136b434b9037408d94a3f0dc07123914fcf6

## Initial Observations: JAR Decompilation

Decompiling the JAR file with JD-GUI revealed several classes, but most failed to load properly or returned empty results:

![obf_1](/assets/images/adwind/obf_1.png)

Switching to an alternative JAR decompiler yielded better results, revealing actual executable logic and embedded resources:

![obf_2](/assets/images/adwind/obf_2.png)

This behavior may be a basic anti-decompilation measure used to hinder static analysis.


## Runtime String Deobfuscation

Analysis of the code showed that strings are obfuscated and decoded at runtime using two functions: `??j.??c` and `??o.??c`.

![obf_3](/assets/images/adwind/obf_3.png)

For example, `??j.??c` is implemented as follows:
![obf_4](/assets/images/adwind/obf_4.png)

To automate string decryption, I extracted the deobfuscation logic into a custom JAR, allowing dynamic inspection of values using a debugger.
![deobf_1](/assets/images/adwind/deobf_1.png)

![deobf_2](/assets/images/adwind/deobf_2.png)

Once the strings were deobfuscated, I was able to follow the execution flow more clearly.


## Embedded resources

Several decrypted strings pointed to embedded resources: `/burn.om`, `/snake.green`, and `/dragon.fire`. These were extracted and used as explained below:
* `/dragon.fire` - used as RSA Private key to decrypt `/snake.green`
* `/snake.green` - is an encrypted AES key
* `/burn.om` - Encrypted data that would be decrypted by the AES key

```java
(var2 = (new Cipher.getInstance).??L("RSA")).init(2, (RSAPrivateKey)"/dragon.fire");
      Object var3 = (new Cipher.getInstance).??L("AES");
      byte[] var6 = var2.doFinal("/snake.green");
      SecretKeySpec var7 = new SecretKeySpec(var6, "AES");
      boolean var10005 = true;
      var3.init(2, var7);
      Object var4 = var3.doFinal("/burn.om"); 
```

The decrypted is then used to initialize a new object `var1000` with the follow strings passed in as arguments to the object's methods.
* `PRIVATE_PASSWORD`
* `PASSWORD_CRYPTED`
* `SERVER_PATH`

![deobf_5](/assets/images/adwind/deobf_5.png)

The method `loadFromXML` is called on the decrypted result.

```java
public __j/* $FF was: ??j*/(InputStream var1) throws IOException {
      super.loadFromXML(var1);
   }
```

With a debugger, the XML object contains the following key and value pairs.
![deobf_4](/assets/images/adwind/deobf_5_2).png)

The values of the keys are extracted and stored into `var8`,`var9` and `var11`.
![deobf_4](/assets/images/adwind/deobf_5_3).png)


## Additional Embedded JARs and Payload Unpacking

Further inspection revealed additional resources located in paths such as `Y/tna` and `Zd/EdG`:

![obf_1_1](/assets/images/adwind/obf_1.png)

Following similar patterns as before, `pc.PJx` and `n.gDa` were used to derive another encrypted payload, `jEX.K`, which is dynamically loaded at runtime. This payload is likely a second-stage component.

Using the same approach, I included them in my JAR program. 

![deobf_6](/assets/images/adwind/deobf_6.png)

And dump out var10 into a file.

![deobf_7](/assets/images/adwind/deobf_7.png)

Decompiling this second-stage JAR revealed another layer of obfuscation:

![deobf_8](/assets/images/adwind/deobf_8.png)

OS-specific execution paths are present. For example, Windows-specific logic invokes `wscript`:

![deobf_9](/assets/images/adwind/deobf_9.png)

And includes Windows Registry interactions:

![deobf_10](/assets/images/adwind/deobf_10.png)


## Deobfuscating Final Stage Strings

While researching the obfuscation technique, I found a [JPCERT/CC blog post](https://blogs.jpcert.or.jp/en/2016/05/decoding-obfuscated-strings-in-adwind.html) describing a similar approach used by Adwind.

It is also explained that Adwind is typically packed and that its main jar is hidden in the artefacts jar file, which appears to be the same for this sample.

Using their tool [`aa-tools`](https://github.com/JPCERTCC/aa-tools), I successfully decrypted the final payload strings:

![deobf_11](/assets/images/adwind/deobf_11.png)

![deobf_12](/assets/images/adwind/deobf_12.png)

The structure confirms the malware's modular nature and use of runtime unpacking, consistent with previously reported Adwind samples.


## Conclusion & Next Steps

This analysis highlights Adwind’s multi-layered obfuscation and use of embedded resources to evade detection and delay analysis.

**Next steps may include:**

- Performing full behavioral analysis of the second-stage payload using sandboxing or dynamic tracing.
- Investigating persistence and communication mechanisms.

Adwind demonstrates a high level of obfuscation and modularity, making it a persistent threat that requires layered analysis techniques.