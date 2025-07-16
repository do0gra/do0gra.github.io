---
layout: post
---

[back](./)

# Hack way to extract Cobalt Strike Configuration

While reversing a Cobalt Strike payload delivered as raw shellcode, I needed a fast way to pull out its configuration.SentinelOne’s CobaltStrikeParser (https://github.com/Sentinel-One/CobaltStrikeParser) initially failed with the following error:\
![parse_1](/assets/images/hack_cobalt_strike/parse_1.PNG)

Experience told me that the beacon was probably wrapped in an additional obfuscation layer. If I could locate—and dump—the decrypted beacon in memory, the parser should work as expected.

Decompiling the shellcode in IDA showed a routine that builds a new buffer (labelled `v35`) and then calls into it:

![ida_screenshot](/assets/images/hack_cobalt_strike/ida_screenshot.PNG)

I confirmed this behaviour in x64dbg, setting a breakpoint on the call to `v35` and single‑stepping into the newly generated region:
![x64bdg_1](/assets/images/hack_cobalt_strike/x64bdg_1.PNG)

![x64bdg_2](/assets/images/hack_cobalt_strike/x64bdg_2.PNG)

Using x64dbg's memory map view, I dumped the buffer to disk:

![x64bdg_3](/assets/images/hack_cobalt_strike/x64bdg_3.PNG)

Feeding the dumped blob back into CobaltStrikeParser now succeeds, revealing the full beacon configuration:
![parse_2](/assets/images/hack_cobalt_strike/parse_2.PNG)

By isolating the decrypted payload in memory and bypassing the obfuscation layer, you can quickly extract CobaltStrike configurations—even when the initial parse fails.