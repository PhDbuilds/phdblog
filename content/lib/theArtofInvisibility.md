---
title: "The Art of Invisibility"
date: "2024-11-25"
author: "Kevin Mitnick"
---
## What are the main takeaways of the book?
In a world of everything being digitized, everything is becoming less and less private. Companies give us the illusion of privacy, but if they have our data, we can almost certainly expect they will either sell it or lose it. Apps like Gmail and other mainstream email/media sell your data and will release it if asked. There are more security-centered solutions out there but they are likely paid services. 

There were quite a few guides on what to do to actually become somewhat digitally invisible. This included stuff like:
- Use Tor with VPN
	- Leave Tor up always to leave less of a trace (sounds backwards but it's true)
	- This is because they can correlated you bringing Tor up with what you visit, so if it's always up, that correlation is gone
- Only use public Wi-Fi and change your MAC address before logging in every time
- Have all burner devices for the privacy stuff, and if using those burner devices, do not have your personal one's anywhere near you

### **1. Protecting Your Personal Devices**
 **Theme**: Devices are the primary entry point for hackers and surveillance, so securing them is critical.
 **Applications**:
- **Use Full-Disk Encryption**: Tools like **BitLocker** (Windows) or **FileVault** (Mac) encrypt your hard drive, ensuring data remains inaccessible even if your device is stolen. On mobile devices, enable encryption via settings (usually automatic on iOS and Android).
    - **How to Apply**:
        - On Windows: Enable BitLocker via `Control Panel > System and Security > BitLocker Drive Encryption`.
        - On Mac: Go to `System Preferences > Security & Privacy > FileVault` and turn it on.
- **Install a Password Manager**: Use tools like **LastPass**, **1Password**, or **Bitwarden** to create and store unique, complex passwords.
    - **How to Apply**: Generate passwords of at least 16 characters with symbols, numbers, and no dictionary words. Change passwords for critical accounts immediately.
- **Regular Updates**: Always update your OS and applications to patch vulnerabilities.
    - **How to Apply**: Enable automatic updates and regularly check for updates, particularly for browsers and antivirus software.
 
### **2. Safe Communication**
**Theme**: Communication tools are often monitored or intercepted. Encrypting communication ensures privacy.
**Applications**:
- **Use Encrypted Messaging Apps**: Applications like **Signal** or **WhatsApp** use end-to-end encryption, making messages readable only by the sender and recipient.
    - **How to Apply**: Download Signal, verify contacts' keys to ensure messages aren't intercepted, and enable disappearing messages for sensitive conversations.
- **Avoid Public Wi-Fi**: Use a **VPN** like **NordVPN** or **ProtonVPN** to encrypt your traffic on public networks.
    - **How to Apply**: Install a VPN, turn it on before connecting to public Wi-Fi, and verify your IP has changed using `whatismyipaddress.com`.
- **Encrypt Emails**: Use tools like **ProtonMail** or install **PGP (Pretty Good Privacy)** for encrypting sensitive email communication.
    - **How to Apply**: Sign up for ProtonMail and use it for critical conversations. For PGP, install GPG tools (e.g., Gpg4win) and share public keys with recipients.

### **3. Anonymity Online**
**Theme**: Maintaining anonymity prevents profiling, tracking, and malicious targeting.
**Applications**:
- **Use the Tor Browser**: Tor routes your internet traffic through multiple servers, making it nearly impossible to trace.
    - **How to Apply**: Download the Tor Browser from the official website (torproject.org). Only access HTTPS websites to ensure encryption, and avoid logging into personal accounts to maintain anonymity.
- **Avoid Search Engine Tracking**: Use **DuckDuckGo** or **Startpage** instead of Google to prevent search-based profiling.
    - **How to Apply**: Set DuckDuckGo as your default search engine in browser settings.
- **Fake Your IP**: Use a VPN or connect to public Wi-Fi when anonymity is critical.
    - **How to Apply**: Combine Tor with a VPN for maximum anonymity, routing your VPN traffic through the Tor network.
 
### **4. Financial Privacy**
**Theme**: Financial transactions often leave a trail that can be exploited or tracked.
**Applications**:
- **Use Cryptocurrency for Transactions**: Cryptocurrencies like Bitcoin or Monero offer pseudonymous or fully anonymous payments.
    - **How to Apply**:
        - Purchase Bitcoin using a platform that doesn’t require KYC verification (if legal in your area).
        - Use mixing services like **Wasabi Wallet** for Bitcoin or opt for privacy-focused coins like Monero for anonymity.
- **Avoid Using Credit/Debit Cards Online**: Instead, use disposable virtual cards (offered by services like **Privacy.com**) or prepaid gift cards.
    - **How to Apply**: Sign up for Privacy.com, link your bank account, and generate unique virtual card numbers for each transaction.
- **Monitor Data Breaches**: Use tools like **Have I Been Pwned?** to see if your financial information has been compromised.
    - **How to Apply**: Search your email on the site, and if breached, immediately change the associated passwords and monitor your accounts for unauthorized activity.
 
### **5. Reducing Your Digital Footprint**
**Theme**: The more personal data available about you online, the easier it is for companies, hackers, and governments to track or exploit you.
**Applications**:
- **Remove Personal Information from Data Brokers**: Data brokers like Spokeo, Whitepages, and Intelius collect and sell your information.
    - **How to Apply**:
        - Use free tools like **OptOutPrescreen** or services like **DeleteMe** to automate removals.
        - Manually request removal from popular brokers by visiting their opt-out pages.
- **Limit Social Media Sharing**: Use pseudonyms for personal accounts and avoid posting identifiable details like location, employer, or birthdate.
    - **How to Apply**: Audit your social media privacy settings, restrict profile visibility to friends only, and avoid tagging locations in real time.
- **Use a Burner Phone or Secondary Email for Registrations**: Sign up for services using temporary emails (e.g., **Guerrilla Mail**) or virtual phone numbers (e.g., **Google Voice**).
    - **How to Apply**: Use the burner services whenever signing up for websites or apps that don't require long-term use.
