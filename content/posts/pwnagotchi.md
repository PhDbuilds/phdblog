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

To be honest, I did not know a whole lot about this device before looking into it. I knew it was a cool little hacking device with a face on it sort of like the Flipper Zero that I have, but I really liked the idea of building something like that for myself. I had a bit of knowledge about WPA/WPA2 protocols from my CompTIA studies and the WiFi Hacking course that I took a while back, but other than that I went into this kind of blind. After digging into the docs and looking into some of the technologies it uses, here are some keys things I learned

#### Bettercap
This is a framework written in Go that is essentially the Swiss-army knife of WiFi, BLE, and other wireless attacks. Key features we are using include - WiFi scanning, deauth, clientless PMKID association attack, and automatic WPA/WPA2 client handshake capture

#### A2C in the pwnagotchi

In reinforcement learning, an _agent_ moves through _states_ in an environment by taking _actions_, all the while trying to maximize _rewards_ throughout the process. The goal is to teach the agent a policy: a strategy to decide the best action in any given situation (state). For the pwnagotchi, these _states_ are things like the signal strength of the AP, the number of visible devices, the frequency channel being monitored, and the WiFi packets. After the _agent_ (pwnagotchi) takes in those states, it will generate _2 outputs_:

- **Critic**: The first output, the _state value_ $V(S)$, is an estimate of how many rewards it can get from that point onwards. This is an estimate of the expected cumulative reward starting from the current state.
- **Actor**: The second output, known as _the policy_ is a recommendation of what action it should take. This is the probability distribution over actions. It doesn't directly pick an action but gives a set of probabilities, and the action is sampled based on those probabilities.

**Just remember:**

- The actor tries to maximize rewards by improving its policy
- The critic helps guide the actor by telling it how good or bad its actions were
- To train the actor, we need to know how much better or worse an action was compared to what we expected.

The pwnagotchi interacts with the WiFi environment by scanning for nearby WiFi environments, deciding whether to target a specific network or wait, and trying to capture WPA/WPA2 handshakes by listening to or actively de-authenticating clients from networks. _It's main goal is to maximize the number of successful handshakes it collects over time while minimizing wasted energy or detection risk_
- State ($s$): The current Wi-Fi environment
- Action ($a$): What the pwnagotchi chooses to do (target a network, wait, etc.)
- Reward ($r$): Feedback based on the success of its action
	- Postitive - captured a handshake
	- Negative - Failed to capture a handshake or wasted time

The main thing to remember here is the environment is _very_ dynamic. WiFi networks come and go, signal strength fluctuates, and human activity affects availability. The pwnagotchi must be able to adapt to this.

Recap:

- The Actor-Critic Framework in Pwnagotchi:
    - Actor (Policy): The pwnagotchi's decision-making strategy. "If I see network A with signal strength $x$, target it with action $y$"
    - The Actor outputs probabilities for different actions given the current state
    - Critic (Value Function): This estimates how "good" the current WiFi environment is.
	- The **Advantage Function** helps guide the pwnagotchi by evaluating whether a specific action in the current state led to a _better_ or _worse_ result than expected.
- Rewards in pwnagotchi:
    - Positive reward: capturing a handshake
    - Negative reward: spending energy without success, waiting too long, or targeting low-value networks
- Parallelism in pwnagotchi:
    - It can operate in different locations or scenarios over time. Each new environment contributes data that helps refine this policy. This is even more so if you are connected to the pwnagotchi community network, where even more data can be shared amongst each other.
- Learning over time:
    - At first, the pwnagotchi is going to do some random stuff and explore different strategies for targeting networks
    - Over time it learns what works and what doesn't, and even more so refines on what really works as a high-reward action
    - It adapts its behavior if the environment changes (fewer networks, encryption types, etc.). It adjusts this behavior based on past experiences.

#### How does it work?

Pwnagotchi is using an `LSTM with MLP feature extractor` as its policy network for the `A2C agent` that it is using. Let's break that down.
##### A2C

- **Policy gradient** - These are a way of teaching an agent to learn directly how to choose actions. They mathematically compute how to adjust the policy based on feedback. The main idea is this -- If an action gives a high reward, make it more likely in the future
- **Value Functions** - These help evaluate how good states and actions are, improving decision-making. Here is an analogy that helped me understand this:
	- Let's say you are trying to decide whether to attend a party:
		- State Value $V(S)$  - Your estimate of how much fun the party will be overall, like "I think this party will be a 7/10 overall"
		- Action Value $Q(s,a)$ - Your estimate of how much fun the party will be depending on specific actions you take. For example, if I talk to new people it will be a 9/10, if I sit in the corner on my phone it will be a 5/10.
	- The Advantage Function compares the action value to the state value:
$$A(s,a) = Q(s,a) - V(s)$$
	- It answers: "How much better or worse is this action compared to just being in this state"
- **Gradient-based Optimization** - This is how we adjust the policy and value estimates using _gradients_. Here is the analogy that helped me understand this:
    - Imagine you are walking around blind on the side of a mountain trying to find the peak:
        - You feel around to estimate if the ground is sloping up or down
        - If it's up, you walk that way (this is the gradient)
        - Repeat until you are at the top
    - A "loss function" tells us how far we are from the goal (the peak here)
    - Gradients tell us the direction to step to reduce the loss.

##### LSTM with MLP feature extractor

###### LSTM
Regular neural networks process inputs independently, and they are not very good at understanding sequences or context. For the pwnagotchi, WiFi networks appear, disappear, or change over time, and capturing handshakes depend not only on the current environment, but also on previous actions and observations. A standard neural network would not handle this very well. Long Short-Term Memory (LSTM) is a type of Recurrent Neural Network that is made to handle this type of stuff. It keeps track of this type of information over time by maintaining a memory. Imagine you are trying to follow a treasure map. It may depend on previous steps, such as "Turn left at the second tree". This type of instruction would only make sense if you remember where the first tree was. This is where LSTMs help out. This relates to the pwnagotchi because it needs to remember patterns like "how often is a network active" or "how does the client activity fluctuate" or "what were the results of the previous actions"

###### MLP Feature Extractor
Multi-Layer Perception can be thought of like a filter that processes messy junk input data into information that is useful. Remember, data and information are not the same thing. The input data may be something like raw Wifi data like signals and encryption types, and the output may be a decision or feature like "This network is likely to have an active client"

###### Using them together
The MLP processes the junk data into something meaningful, and then that useful information is used by the LSTM to maintain a memory of past actions, observations, and rewards to make better decisions.

Example - If a network consistently fails to yield handshakes, the LSTM learns to deprioritize it. If a pattern emerges (11am has more active clients), the LSTM remembers and exploits it.

**Why combine them?**

- WiFi networks are _sequential_
- Raw WiFi data is _complex_
- The A2C algorithm explored above needs a clear understanding of the current state and how it evolves over time. The MLP and the LSTM provide this understanding.

---

#### TL;DR

1. The pwnagotchi scans for WiFi networks and collects raw data
2. The raw data is processed by the MLP into a compact representation (features)
3. The LSTM takes these features over time, maintains a memory of past observations and actions, and outputs a state representation
4. Reinforcement Learning (A2C) uses the processed data to decide which network to target and whether to wait, move, de-auth, etc.
5. The rewards from those actions help refine both the LSTM and MLP over time
---

## Setting up Pwnagotchi
### Hardware Assembly
This was very straightforward so I will not go into a ton of detail here. First, the PiSugar 3 Portable 1200 mAh Battery is fixed to the back of the Raspberry Pi using the pogo connectors. This was super cool and the first time that I've used these before. Then, the Waveshare 2.13 in E-ink Display was connected through the GPIO pins on the front of the Raspberry Pi. I used a SanDisk 64GB Extreme microSD card, and I printed the case that I used. I have a FlashForge Adventurer Pro 4 3d printer that I used for this. 


**IMPORTANT** -> That ribbon cable on the back of the screen is ultra sensitive and fragile. I accidentally barely nicked it when putting it in the case that I 3d printed, which has caused my display to look wonky. I've ordered a replacement because it's annoying to look at now and a constant reminder that I did not pay enough attention after I literally heard "beware of hitting the ribbon cable on the back, it break easily" seconds before shoving it into the case....

!![Image Description](/images/Pasted%20image%2020241211144107.png)
### Installing the Firmware/OS
This was pretty simple and straight forward. I used https://github.com/jayofelony/pwnagotchi `Pwnagotchi 2.9.2` which was the latest release at the time of writing this. *If you are doing this on a Raspberry Pi Zero W, make sure you choose the 32-bit version.* This OS is based on RasPiOS. I used the Raspberry Pi Imager to flash the MicroSD card. Make sure if using the RPi Imager, you hit 'NO, CLEAR SETTINGS' when prompted to use your saved config. 
### Network  and Other Config Setup 
For the first time connecting to your pwnagotchi on Windows, you'll need to download and install the RNDIS driver found [here](https://github.com/jayofelony/pwnagotchi/wiki/mod-rndis-driver-windows.zip). After this was installed and my pwnagotchi was connected through it's data port (the middle one) to my PC, I had to go to my adapter settings in the Network and Internet Control Panel pane and choose the device named something like `USB Ethernet/RNDIS Gadget`. From here change the network properties to the following:
```
IP: 10.0.0.1
Subnet: 255.255.255.0
#LEAVE EVERYTHING ELSE BLANK
```
After this is done, you should be able to `ssh pi@10.0.0.2` and you will get the following setup wizard to continue with the configuration:
!![Image Description](/images/Pasted%20image%2020241209195647.png)
After this is done, in theory, you should be good to go to use it as a standalone device to get all the packets and everything that you are collecting, then you can plug it back into your PC, connect to it through SSH, and get all the data and whatnot that you collected with the pwnagotchi. This is where I am currently having trouble.

- I have tried downloading the drivers again and finding my device within the Change Adapter Settings pane, but the device never shows up.
- I have tried connecting to my macbook, with no luck. I am able to see it under network devices, however after changing the network settings to what is mentioned earlier, I am unable to SSH to `pi@10.0.0.2`
- I have tried going through this in a Kali VM, using the Linux install script and making sure I have USB device set up properly to work with the VM. I used the `linux_connection_share.sh` script to set everything up with no luck. I am unable to ping the pwnagotchi or anything.

Maybe I have the wrong driver or something? That doesn't make sense since it worked before but https://archive.org/details/pwnagotchi_1.5.5_WSV3Patched may get this working
[Here](https://pwnagotchi.org/getting-started/first-run-mac/index.html) is the guide that I'm following for getting it to work on mac -- will check back in afterwards because I did not set my DNS settings and router settings like this guide describes...



## Pwnagotchi in Action
TBD...
## Challenges and Lessons Learned
Very many...
## Ethical Considerations
Insert 'This is for educational purposes only' disclamer here..

But seriously, deauth attacks are illegal to perform on networks that you do not own/have permission to do this on.

But as GotMyOrangeCrush put so well on a reddit post in reference to this, "Unless you deauth grandma's pacemaker or interfere with the navigation of nearby aircraft, you're unlikely to get a love letter from a three letter agency.

Since I am currently writing a paper on the legal/ethics in cybersecurity for one of my college courses, I feel like it may actually benefit to go into this a little bit here.

The FCC states under Section 333 of the Communications Act that sending these deauthentication or disassociation frames intentionally is considered a form of "Wi-Fi blocking" and therefore illegal. (FCC, 2015)

What this act essentially states in relation to this is that it is illegal to interfere with or disrupt Wi-Fi by any means, which would include sending deauthentication packets. Sending these deauth packets are considered a form of jamming and are strictly prohibited, because they prevent users from accessing lawful communications services. 

That being said, I believe these deauth packets are sent by default, so if you plan on having the pwnagotchi powered on when you are anywhere in reach of other networks, you should turn this feature off.
## Future Enhancements
Things I would like to add:
 - Encryption in case I lose it 
 - I'd like to do some hardware upgrades like adding GPS and a high-gain antenna to this for wardriving
	 - This would benefit from adding some 3rd party modules like `f0xtr0t` or `Wardriver` 
- There are some cool 3rd party UI plug-ins I've been looking at. Some of interest include:
	- memtemp-plus
	- Pwnmenu
	- Experience and Age Plugins
	- Pisugar 3 plug to show battery information in the UI
- Other 3rd party plugins I thought were interesting:
	- handshakes-dl-hashie
	- better_quickdic

I've also been looking into `fancigotchi` which looks like it may be a cool rabbit trail to go down.
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

Other mentioned:
https://github.com/V0r-T3x/Fancygotchi
https://github.com/aluminum-ice/pwnagotchi/releases
https://pwnagotchi.org/index.html
https://github.com/jayofelony/pwnagotchi/wiki
https://hackernoon.imgix.net/hn-images/1*JJ3Dx4O3blc_haCUjv5Y5A.jpeg

Reference for FCC discussion:
FCC. (2015). _FCC Enforcement Advisory: Communications Act Prohibition Against Jamming, Blocking, or Interfering with Wi-Fi and Other Communications Networks._ Federal Communications Commission. Retrieved from [https://transition.fcc.gov/eb/Orders/2015/FCC-15-146A1.html](https://transition.fcc.gov/eb/Orders/2015/FCC-15-146A1.html)

---