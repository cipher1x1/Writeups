Previous: ![Homelab/01 - Proxmox Homelab.md](01 - Proxmox Homelab)
Next: [[03 - Secure Remote Access with Tailscale]]

> **Note**: Threat modeling is a consistently evolving practice. 
> As technology advances, such as quantum computing or AI-assisted threats, this writeup will be updated periodically from time to time to reflect current security standards. 

When defending or designing a system, understanding your threat model is an essential task.

In this writeup, I will explain what a threat model is, why it is an important part of designing a secure system, and how you can create one that is suitable for your environment.

## What is a threat model?
A threat model is essentially a blueprint for identifying and mitigating threats.

Threat modeling is common in both digital and real world security. For example; when designing a lock, before its even actually made, a group of people take a look at the schematic and ask various questions such as "What happens if someone does X? Will Y happen, allowing for them to unlock the door?"

Essentially, threat modeling allows us to identify potential attack methods and to implement defenses to reduce the impact and likelihood (very important because most cyberattacks are opportunistic) of an attack. 

## The Core Four
A good majority of threat modeling frameworks usually answers the following 4 questions:

| #   | Question                                       | Example                                                                                                                                                                                                                                |
| --- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | What are we building?                          | A really cool website about motorcycles with tons of features and a forum hosted on a VPS server                                                                                                                                       |
| 2   | What could go wrong?                           | An attacker, exploits an unpatched vulnerability/brute-forces credentials and moves laterally throughout the network.                                                                                                                  |
| 3   | How can we prevent the "wrong" from happening? | Keep attack surface low by removing unneeded features, ensure UFW's rules are set to automatically deny services unless approved, periodically update the VPS automatically through the `unattended-upgrades` package on Debian/Ubuntu |
| 4   | Did we do a good job?                          | Consistently reassess threat model as new features are added, verify that only intended services are accessible globally, monitor logs and periodically make backups of important data                                                 |
## The hidden "fifth" rule
Additionally, I like to add a 5th rule: Who am I protecting this from and what are they capable of?
By asking who you are defending against, it allows you to harden against known attack methods.

For example, lets say you are a business preparing to be specifically targeted by Scattered Spider:

| Threat Actor     | Initial Access                                                                                                                                                                                                                                                                                     | Persistence                                                                                                                                   | Exfiltration                                                                                                                    | Actions on Objective                                                                                                                                                                                                                                                                                                                                                    |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Scattered Spider | Primarily phishing, including SMS, voice and email (T1566/T1684). Occasionally uses credentials stolen via infostealers (T1078/T1539). Known to scam telecom workers into performing simjacking attacks (T1451). Rarely exploits public facing applications (T1190).                               | Registering new accounts or devices (T1098), Manipulates individuals over the phone into or manually installs remote access software (T1113). | Archives stolen data and transfers it to attacker-controlled cloud storage or external file-sharing services (T1560, T1567.002) | Encrypts systems, threatens to publish stolen data, and intimidates victims or their families through targeted calls, threats, and unwanted deliveries (T1486, T1490; non-ATT&CK physical intimidation behavior)                                                                                                                                                        |
| **Mitigations**  | Avoid SMS-based MFA (use hardware or OTP MFA instead), train staff on voice phishing and inform them to do an out of band call if someone claims to be a staff member, avoid exposing applications to the open internet if they do not require it (use a VPN like WireGuard or Tailscale instead). | Restrict account creation/device enrollment, apply least privilege to each account.                                                           | Alert on bulk downloads or uploads, apply data loss prevention and egress filtering.                                            | Possess numerous, offline, cold backups; test restoration regularly; isolate critical systems from non-critical ones; request to be removed from people list sites and avoid posting images online that could be used to locate or identify you (If not possible, call local police department and inform them that you are at risk of false SWAT calls and harassment) |

In the case of my homelab, it is primarily random script kiddies and opportunistic attackers so mitigation is pretty easy, just update every once in a while and make sure things are patched.

## Not all threats are people (or bots)

While it's easy to think of all threats being threat actors, that's often not the case.

Let's take a look at my lab for example:

| #   | Question                                       | Example                                                                                                                                                                                      |
| --- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | What are we building?                          | A self-hosted homelab containing various services that assist with storage, entertainment, remote access and automation.                                                                     |
| 2   | What could go wrong?                           | Opportunistic attackers discover a port-forwarded application, exploit an unpatched vulnerability/brute-force credentials and move laterally throughout the network.                         |
| 3   | How can we prevent the "wrong" from happening? | Keep attack surface low, port-forwarding only unless absolutely necessary, with the port-forwarded services behind a DMZ or inside their own VLAN and following principle of least privilege |
| 4   | Did we do a good job?                          | Consistently reassess threat model as new services or nodes are added, verify that only intended services are accessible globally, and monitor logs                                          |
| 5   | Who is my primary adversary?                   | Opportunistic attackers/bots that scan the internet for out-of-date devices. Mitigate using above instructions                                                                               |

This looks pretty clean and easy to follow, right? Well, it is missing something important. ***What if the power goes out and corrupts the data? What if the SSD or HDD starts having bad sectors and can't hold data anymore? What if a tornado comes through and destroys the server?***


A lot of these may sound ridiculous on paper for a homelab, but they are very possible threats to the homelab. That's why its also important to label potential non-human threats.


In this case, for mitigation we only have to backup our data using the 3-2-1 rule; use the original copy of the data along with 2 separate backups.
In my case, everything really important is backed up on a USB flash drive and an external HDD, with some of it in the cloud encrypted with GPG.
