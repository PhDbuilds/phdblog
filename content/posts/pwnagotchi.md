---
title: pwnagotchi
date: 2024-12-09
time: 07:26
draft: false
---
## What is pwnagotchi?
**Pwnagotchi: The Tamagotchi for WiFi Hackers**  
"[Pwnagotchi](https://twitter.com/pwnagotchi) is an [A2C](https://hackernoon.com/intuitive-rl-intro-to-advantage-actor-critic-a2c-4ff545978752)-based “AI” powered by [bettercap](https://www.bettercap.org/) that learns from its surrounding WiFi environment in order to maximize the [crackable WPA key material it captures](https://pwnagotchi.ai/intro/#wifi-handshakes-101) (either through passive sniffing or by performing deauthentication and association attacks). This material is collected on disk as PCAP files containing any form of crackable handshake supported by [hashcat](https://hashcat.net/hashcat/), including full and half WPA handshakes as well as [PMKIDs](https://www.evilsocket.net/2019/02/13/Pwning-WiFi-networks-with-bettercap-and-the-PMKID-client-less-attack/)." (https://pwnagotchi.ai/intro/)

Pwnagotchi is a fun tool for anyone into WiFi hacking. It's like a Tamagotchi, but instead of needing food and attention, it feeds on WiFi handshakes. Running on affordable hardware like a Raspberry Pi Zero W, Pwnagotchi *passively* (more on this later) captures WPA/WPA2 handshakes, helping you build wordlists for cracking networks. What makes it cool is that it learns and adapts, getting smarter as it goes. Even better, it interacts with other Pwnagotchis if they cross paths, sharing knowledge and growing together. 

**I chose this project for a few reasons:**
- I like projects that I can build with my hands
- I'm interested in Wi-Fi network hacking and have used tools like `Aircrack-ng` in the past. I thought this would be a good way to build on that knowledge
- I'm pretty new to AI and different AI algorithms and such, and thought this would be a good way to go down this rabbit hole a bit. [Here](https://hackernoon.com/intuitive-rl-intro-to-advantage-actor-critic-a2c-4ff545978752) is a good explanation of the Advantage-Actor-Critic A2C) model that is being used in this project.

!![Image Description](/images/Screenshot%202024-12-10%20at%207.07.05%20AM.png)


## Planning and Prep
### Software and Hardware Requirements

**Here is the hardware that I used to create this project:**
- Waveshare 2.13 in E-ink Display
- Raspberry Pi Zero W
- PiSugar 3 Portable 1200 mAh Battery
- SanDisk 64GB Extreme
- STL found at https://pwnagotchi.org/3d-printable-cases/index.html

**The software that I used was from the following:**
- https://github.com/jayofelony/pwnagotchi
### Initial Research
## Setting up Pwnagotchi
### Hardware Assembly
### Installing the Firmware
### Network Setup
!![Image Description](/images/Pasted%20image%2020241209195647.png)
## Pwnagotchi in Action
## Challenges and Lessons Learned
## Ethical Considerations
## Future Enhancements
## Conclusion
## Supporting Content
### Photo Gallery

!![Image Description](/images/Screenshot%202024-12-10%20at%207.23.47%20AM.png)

!![Image Description](/images/Screenshot%202024-12-10%20at%207.07.31%20AM.png)

!![Image Description](/images/Screenshot%202024-12-10%20at%207.24.14%20AM.png)

!![Image Description](/images/Screenshot%202024-12-10%20at%207.24.37%20AM.png)

### Code and Config
### References and Resources
Check out  https://www.evilsocket.net/2019/10/19/Weaponizing-and-Gamifying-AI-for-WiFi-Hacking-Presenting-Pwnagotchi-1-0-0/ for full details on the project and https://pwnagotchi.ai/installation/ for a tutorial on how to build it too.

---