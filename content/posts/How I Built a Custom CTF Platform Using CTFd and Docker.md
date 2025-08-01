---
title: "How I Built a Custom CTF Platform Using CTFd and Docker"
date: "2025-05-10"
time: "17:54"
draft: false
tags: 
- cybersecurity
- CTF
- Docker
---
# Overview
For the first week of May I hosted a small CTF scrimmage for my cohort/club at **Western Governors University**.   Everything ran on my self‑hosted **CTFd** instance, backed by a set of lightweight Docker containers so I could spin challenges up and down easily.   Below is a quick recap of how I built the event and a few lessons I picked up along the way. To see the mini-write up I did for most of the challenges, see [WGU CTF Scrimmage Spring 2025](/posts/2025/05/wgu-ctf-scrimmage-spring-2025/)

---
## Infrastructure

| Layer | What I used | 
|-------|-------------|
| **Front‑end** | CTFd (Docker image) |
| **Challenge network** | Docker Compose |

For the front-end of things where users log in and start up the challenges, download any necessary files, and see their scores, I used https://github.com/CTFd/CTFd. This was super easy to set up via an Ubuntu machine I spun up on Digital Ocean. Any of the static challenges like DFIR, Cryptology, etc. where uploaded here in the CTFd dashboard. For the live/web challenges, I spun up an additional Digital Ocean droplet and put all my vulnerable Docker containers on their.

This is was first time really getting into Docker and Docker Compose, and adding this into my workflow made everything extremely easy to implement. 

Here is the docker-compose.yml file I used to start up my custom challenges:
```yml
version: "3.8"

services:
  harrypotter:
    build: ./harrypotter
    container_name: hp1
    ports:
      - "5001:5000"

  xsswizard:
    build: ./xsswizard
    container_name: xss1
    ports:
      - "5002:5000"

  sqlgoblin:
    build: ./sqlgoblin
    container_name: sqli1
    ports:
      - "5003:5000"

  polyjuice:
    build: ./polyjuice
    container_name: poly1
    ports:
      - "5004:5000"

  shellofsecrets:
    build: ./shellofsecrets
    container_name: shell1
    ports:
      - "5005:5000"
    read_only: true
    tmpfs:
      - /tmp

  mirrorofmisfortune:
    build: ./mirrorofmisfortune
    container_name: mirror1
    ports:
      - "5006:5000"

```

To start and stop the challenges I used `docker-compose up -d` and `docker-compose down`, and anytime I made changes to the challenges I could run `docker-compose up -d build` to rebuild the containers.

I hosted two separate droplets total on Digital Ocean for this project. One for the CTFd front end, and one for the vulnerable machines. This kept things separate and more secure than putting this all together.

---

## Challenge Line‑Up
### Web Exploitation
| Challenge Name           | Category             | Short Description                                                                   |
| ------------------------ | -------------------- | ----------------------------------------------------------------------------------- |
| **Mischief Managed**     | Recon                | Discover the hidden subdomain path that unveils the flag.                           |
| **The Forbidden Script** | Cross-Site Scripting | Inject a `<script>` into the comments board to exfiltrate the secret flag.          |
| **Goblin Bank Heist**    | SQL Injection        | Bypass the login form via SQLi to break into the vault and grab the flag.           |
| **Polyjuice Portal**     | Authentication Flaw  | Tweak your `role` cookie to `admin` and unlock the hidden message.                  |
| **Shell of Secrets**     | Command Injection    | Inject shell commands into the spell lookup to `cat` the flag file.                 |
### DFIR

1. **Glory of the Garden** - Garden photo with the flag hidden in plain sight; hex‑viewing the PNG footer revealed it.
    
2. **Unknown**  - Another photo with the flag hidden inside of it. This one could be found by uploading to https://www.aperisolve.com/ or the like.
    
### Cryptography
1. **M4s0n** - This was a mixture of hex and base64 encodings

2. **2EZ and 2EZ v2** - A couple Caesar shifted flags

### General Skills
The majority of these were grabbed from past PicoCTF tournaments. More can be seen about these on [WGU CTF Scrimmage Spring 2025](/posts/2025/05/wgu-ctf-scrimmage-spring-2025/)

---

## Things to Improve Next Time
One issue I ran into was with two users pwning the same box at the same time. Since I was just running each of these as a single container, people could step on each others toes. Doing this all through Kubernetes would solve this issue and something I am going to learn for next time.

---

## Final Thoughts
This was a lot of fun and I found myself enjoying setting this sort of stuff up just as much as participating in CTFs myself. It's made me look into more things on the engineering side of security and sparked a new found interest that is carrying over to my home lab, which will be something I will post about soon, so keep an eye out for that!

If you made it this far, thanks a ton for reading and sticking around, I really appreciate it! 