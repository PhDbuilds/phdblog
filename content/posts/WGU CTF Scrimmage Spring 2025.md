---
title: WGU CTF Scrimmage Spring 2025
date: 2025-05-02
time: 21:38
draft: false
---
## Overview

The following write‑up covers the WGU CTF scrimmage that wrapped up on **May 2, 2025**. Some challenges were home‑brewed, others borrowed (e.g., picoCTF). External write‑ups are linked where helpful.

---
## DFIR

### Glory of the Garden
Open the image in a hex editor such as [https://hexed.it/](https://hexed.it/).
You'll find the flag at the bottom:
!![Image Description](/images/Pasted%20image%2020250502212818.png)

---

### Unknown
1. Upload to [https://www.aperisolve.com/](https://www.aperisolve.com/) and wait for analysis.
2. You'll find a base64 message in the Strings section
3. Decode this to find the flag
    
!![Image Description](/images/Pasted%20image%2020250502212928.png)
!![Image Description](/images/Pasted%20image%2020250502212937.png)

---
### Viewing Events
Here's the write-up for this one: https://medium.com/@jojolucky221/picoctf-2025-forensics-event-viewing-34578235166b

---

## Cryptography

### m4s0n
This is a mixture of hex and base64 encodings. See the recipe on  https://cyberchef.org below:

!![Image Description](/images/Pasted%20image%2020250502213204.png)

---

### 2EZ and 2EZ v2

Plain Caesar shifts. Use [https://www.dcode.fr/caesar-cipher](https://www.dcode.fr/caesar-cipher).

---

### 3ncrypted

Full breakdown: [https://github.com/Cajac/picoCTF-Writeups/blob/main/picoCTF_2024/Cryptography/Custom_encryption.md](https://github.com/Cajac/picoCTF-Writeups/blob/main/picoCTF_2024/Cryptography/Custom_encryption.md)

---

## General Skills

### Monty

Change the following in the Python file you are given:
!![Image Description](/images/Pasted%20image%2020250502213329.png)

And then hit 'b' when prompted for the flag:
!![Image Description](/images/Pasted%20image%2020250502213401.png)


---

### Commitment Issues

[https://github.com/Cajac/picoCTF-Writeups/blob/main/picoCTF_2024/General_Skills/Commitment_Issues.md](https://github.com/Cajac/picoCTF-Writeups/blob/main/picoCTF_2024/General_Skills/Commitment_Issues.md)

### git good

[https://github.com/Cajac/picoCTF-Writeups/blob/main/picoCTF_2024/General_Skills/Time_Machine.md](https://github.com/Cajac/picoCTF-Writeups/blob/main/picoCTF_2024/General_Skills/Time_Machine.md)

---

## Web Exploitation

### The Marauder’s Map
Directory enumeration challenge. This would require knowledge of Harry Potter or proper OSINT research, as I don't believe this would be in a typical dictionary file. 

!![Image Description](/images/Pasted%20image%2020250502213420.png)

---

### Shell of Secrets
This was a simple command injection vulnerability:
!![Image Description](/images/Pasted%20image%2020250502213524.png)

---

### The Forbidden Script
This was a simple XSS vulnerability:

```html
<script>
fetch('/flag').then(r=>r.text()).then(alert);
</script>
```

---
## 
### Goblin Bank Heist
This was a simple SQLi vulnerability:
!![Image Description](/images/Pasted%20image%2020250502213613.png)

---

### Polyjuice Portal

Change the `role` cookie to `admin` in DevTools:

!![Image Description](/images/Pasted%20image%2020250502213645.png)


---

Thanks to all who participated! 