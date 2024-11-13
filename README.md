# Forensic-Analysis-of-Unauthorized-Access-on-Windows-VM

## Table of Contents
- [Introduction](#introduction)
- [Objectives](#objectives)
- [Lab Environment](#lab-environment)
- [Tools and Technologies Used](#tools-and-technologies-used)
- [Step-by-Step Process](#step-by-step-process)
- [Results and Analysis](#results-and-analysis)
- [Challenges Faced](#challenges-faced)
- [Conclusion](#conclusion)
- [References](#references)
- [Full Step-By-Step](https://github.com/LucasCodes8/Forensic-Analysis-of-Unauthorized-Access-on-Windows-VM/blob/main/Forensic%20Analysis%20of%20Unauthorized%20Access%20on%20Windows%20VM.pdf)

---

## Introduction
This project dives into investigating potential unauthorized access to a Windows VM using Windows Registry forensics. With tools like RegistryExplorer and other programs from Eric Zimmerman’s toolkit, we explore registry artifacts to check for suspicious user activity, extra user accounts, and details about network drives and USB devices that may have been connected.

---

## Objectives
The goal is to determine if someone unauthorized accessed this system. Specifically, we’ll:
- Identify any extra user accounts (ideally, there should only be one).
- Discover the last access date of the suspicious Changelog.txt file.
- Locate the path of a Python script that might've been used for persistence.
- Retrieve details about any USB devices that were connected.

---

## Lab Environment
The lab environment is a Windows VM provided via TryHackMe. After logging in, two main folders are located on the Desktop:
- **triage**: Contains registry data collected with KAPE, ready for analysis.
- **EZtools**: Contains Eric Zimmerman's tools, including RegistryExplorer and AppCompatCacheParser.

---

## Tools and Technologies Used
The tools used in this investigation:
- **RegistryExplorer**: A tool to navigate and analyze registry hives.
- **EZViewer**: For viewing logs and files.
- **AppCompatCacheParser**: Parses application compatibility cache data.
- **KAPE**: Pre-collected the registry data for analysis.

These tools allowed us to inspect Windows registry hives (SAM, SYSTEM, and SOFTWARE) to identify artifacts and events indicative of unauthorized access.

---

## Step-by-Step Process

### Step One: Load Registry Hives
To start, I loaded the necessary registry hives from the System32 folder in **RegistryExplorer**. The SAM, SYSTEM, and SOFTWARE hives contain critical user and system data. The hives were marked as “dirty,” so RegistryExplorer prompted me to include the .LOG1 and .LOG2 transaction logs, which helped create stable, clean copies for analysis.

### Step Two: Identify Unauthorized User Accounts
I examined the **SAM (Security Accounts Manager)** hive to check for any unexpected user accounts. This file holds details on each user. I found three user accounts named “THM-User,” and immediately noticed that “THM-User2” had no last login date, indicating it was created but never actually used—a potential backdoor account.

### Step Three: Determine Last Access of Malicious File (Changelog.txt)
To find the last accessed date of **Changelog.txt** (flagged as suspicious), I checked the **RecentDocs** key in the registry, which logs recent document activity. The entry revealed that Changelog.txt was last accessed on **November 24, 2021**. This gave us a timeline of the potential attack.

### Step Four: Locate the Path of the Executed Python Script
Next, I needed the full path of a Python script that might’ve been used to gain persistence. By reviewing **RecentApps** in the registry, I found logs for a folder named **RecentDocs**, where I could confirm the script’s path as **Z:\setups**. This helps us target the specific script and remove any lingering persistence.

### Step Five: Identify Last Connection Date of the USB Device
Finally, I investigated the USB drive named “USB” to determine when it was last connected. I found the device under **Windows Portable Devices** and matched the GUID with the **USBSTOR** key, which revealed the exact connection date. The logs confirmed that this USB drive was last connected on **November 24, 2021**, reinforcing our timeline for the suspicious activity.

---

## Results and Analysis
This investigation verified the existence of an unused user account (THM-User2) and confirmed that both the USB device and Changelog.txt file were last accessed on the same date. We also identified the exact path of the malicious Python script at Z:\setups, which will aid in removing persistence.

---

## Challenges Faced
Matching GUIDs and Disk IDs to confirm USB connection history was a challenge due to the somewhat cryptic naming conventions in registry logs. Additionally, loading the registry hives with the proper transaction logs required a bit of troubleshooting to ensure we had clean data.

---

## Conclusion
This project showed how registry artifacts can shed light on unauthorized access and provide a trail of clues to follow. By analyzing the SAM hive, RecentDocs, and USBSTOR, I confirmed extra user accounts, pinpointed file and device access dates, and found potential backdoors—all essential steps in tracking down how a system may have been compromised.

---

## References
- TryHackMe. “THM-4n6 Lab Environment.” URL: https://tryhackme.com/
- Zimmerman, Eric. “EZtools for Registry and Forensic Analysis.” URL: https://ericzimmerman.github.io/
- Microsoft Documentation. “Windows Registry Reference.” URL: https://docs.microsoft.com/en-us/windows/win32/sysinfo/registry
- Forensics Wiki. “Windows Registry Forensics.” URL: https://forensicswiki.org/wiki/Windows_Registry

