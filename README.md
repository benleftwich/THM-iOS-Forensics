# TryHackMe - iOS Forensics created by [cmnatic](https://tryhackme.com/p/cmnatic)

![TryHackMe iOS Forensics front banner](/THM-iOS-Forensics/docs/assets/images/THM-ios-forensics-banner.png)

## What is Digital Forensics and how is it used today?
Digital forensics is the mere digitisation of the traditional use and applications of forensic investigation, for example, within police departments after a crime has been committed.

Artefacts such as blood, fingerprints or hair fibres are used in criminal investigations to paint a picture of the events that took place and who was involved. Digital forensics is the same collection of artefacts, however, these artefacts being events on a digital device.

Images are bit-for-bit replications of devices, aptly titled "images" giving a nod to photography. These images can derive from anything such as a SatNavs for a car, a smart TV, to a router and a full-on server.

As you'll come to discover, digital devices hold plethoras of information, and in turn, evidence about our activities. In this room we'll be analysing an iOS device.

What would look more suspicious? an empty hard drive or a full hard drive?
> An empty hard drive

What is the definition for an abstract view of a hard drive?
> Image


## Problems Facing Digital Forensic Analysts

1. Time Consumption & Resources
  - Digital forensics is, without doing it justice, an incredibly time-consuming process. Despite the toolkits and suites available, you find yourself having to analyse data bit-by-bit to find that one smoking gun. Now extend that to a 1TB drive - not so fun huh. With the very nature of forensic images being exact bit-for-bit copies of an acquired device or system, you need to have the facilities to be able to store this data before it can be processed. Take a file server full of data with 10 terabytes of data. You need 10 terabytes to store that on as well, slapping on another 10 terabytes required for the backups of the image that you make; suddenly you have at least 20 terabytes sitting around.
2. Understanding the Person
  - As a forensic analyst, you have to piece various parts of information together, into a formal and well-documented timeline of events for presentation.   For example, if you were to try to find a bit of text in a document on your computer, you'd know where to go. Now take away the desktop/GUI and terminal then ask your friend to try and find it through the means similar to the screenshot in Task 2. Pretty hard huh?
3. Encryption
  - As it stands, an effective and secure implementation of encryption poses as one of the biggest hurdles to forensic analysts. The problem mostly? People and/or devices themselves leave the decryption keys within the same platform. Such as in the case of the iPhone you are going to analyse. After all, it's that catch-22 of a complex password is a strong password until you need to write it down.
4. Steganography
  - We all know (and love) steganography here; Hiding data within data. And in some cases, is more secure then cryptography; seeing as cryptography makes the contents unreadable, steganography masquerades the entire existence of this data altogether. (Din et al., 2018)
5. Cost of Entry


## iOS File Systems

Apple have created their own sets of file system formattings: ***AFS and HFS+***

Starting with the oldest, [HFS+ or Mac OS Extended](https://en.wikipedia.org/wiki/HFS_Plus) is the legacy file system used by Apple all the way in 1998 and is still supported today. The issue being that HFS was not future proof - given the fact it cannot support file timestamps past February 6th, 2040 (Vigo., 2018).

Whilst HFS+ didn't support encryption at its entirety (a win in our books as forensic analysts) any device such as iMac or iPhone past iOS 10.3 will have had their file system converted from HFS+ to AFS automatically.

[AFS or Apple File System](https://en.wikipedia.org/wiki/Apple_File_System) (creative right...!) boasts many features, including full disk encryption, worrisome for analysts considering all devices past iOS 10.3 will have this system structure. But from a design point of view, AFS introduces smarter data management such as in the screenshot below, where a file requiring 3 blocks worth of space when copied, would require another 3 blocks again.

![Copying a file HFS+ vs. AFS](/THM-iOS-Forensics/docs/assets/images/Copying a file HFS+ vs. AFS.png)

Instead of writing and storing the entire data again (taking up six blocks in our example), AFS simply creates another reference to the file (only taking up a total of four blocks in our example), similar to inodes in Linux.






## Modern iOS Security

Since 2018, Apple enforced a **Restricted Mode** on all iDevices running that version and above. This feature disables the input/output of data functionality from the lightning (charge) cable until the iPhone is unlocked with a passcode. Devices must be trusted before any data can be written - or so as by design.

![iPhone Restricted Mode](/THM-iOS-Forensics/docs/assets/images/iphone restricted mode.png)

In Restricted Mode, the iPhone will charge, but any data cannot be written or read in its current state.







## Data Acquisition & Trust Certificates

In modern-day digital forensics, there are four primary methods and procedures followed when trying to retrieve data from devices such as iPhones. How an analyst approaches a device is arguably the most important decision they'll make. If the wrong call was made, data that could have been retrieved may end up deleted, or just as worse, inadmissible as evidence due to incorrect technique or failure to follow policy.

In a court of law, any evidence submitted must be admissible. This complex process involves the "chain of custody". No matter how indicting a piece of evidence is, it can be dismissed if there is insufficient documentation and/or negligence in handling - all the way from the crime scene to the courtroom.

| Method | Use Case |
| --------------- | --------------- | 
| Direct acquisition | Interacting with the device itself if, for example, it was found unlocked. No need to bypass anything! |
| Logical/backup acquisition | Utilising the iTunes backup of a phone for file system entry, or the use of forensics software to analyse data found within these backups i.e. .plists  | 
| Advanced logical acquisition | Using the escalated privileges to an iPhones file system found when pairing an iOS device to a Computer using either iTunes or Xcode. | 
| Physical acquisition | The most direct approach, physical acquisition is the use of forensic imaging kits such as Cellebrite to take entire bit-for-bit copies of both the data and system partitions. Unsophisticated tools (such as those that don't launch the iPhone into a custom boot loader) will leave the data encrypted. |


#### Direct Acquisition:

Perhaps one of the only times where it could be argued that Cellebrite's "Physical Analyzer" has more bells and whistles then what's needed for the job. Non-forensically focused, and most importantly free, applications such as the iFunbox perform the same job in this scenario.

Direct acquisition covers three scenarios:

1. There is no password on the phone

2. There is a password but it is known to the analyst

3. The analyst has a "Lockdown Certificate" which is what we'll come onto in just a bit.

Whilst this sounds appealing, applications such as the iFunbox are capable of writing to the device being analysed. Because of this, the image made will now be inadmissible as evidence due to the fact that there's a possibility data was (over)written to the device that wasn't from the suspect - a defence attorney can argue the data could have been left by the forensic analyst.


#### Logical or Backup Acquisition:

Also applicable to the three scenarios above, the backup acquisition is the cheapest way of acquiring data from a device such as an iPhone. By using iTunes' backup facility, analysts can simply use a computer that has been paired with the iPhone before. However, Logical acquisition is where the big money starts to roll in. You're going to be hearing of Cellebrite a lot, they're quite the giant in a very specialist field because of their incredible kit, making the actual stage of accumulating data ten-fold.

After all, if you can't analyse a phone - just analyse the unlocked PC that has an entire backup upon it, right?


#### iTunes Backups & Trust Certificates

The analysis of iPhone backups made with iTunes is an interesting topic, to say the very least. When backing up an iPhone, iTunes accesses the iPhone in a privileged state - similar to using the sudo command on Linux to run a command with root privileges.

iPhones will only backup to trusted computers. When plugging into a new device, the iPhone will ask the user whether or not they wish to trust the computer - as seen in the screenshot below:
![iPhone Trust Popup](/THM-iOS-Forensics/docs/assets/images/iphone-trust.png)

"Trusting" a computer involves generating a pair certificate on both the iPhone and computer. If the certificate matches up on both devices, the iPhone can be backed up. This process is a security measure to prevent attacks such as "Juice Jacking".

A lockdown certificate stored within ```/private/var/db/lockdown``` on later iOS devices or ```/private/var/Lockdown``` on older iOS devices.
Note that Apple have locked this folder down and you are initially unable to alter permissions on it: 
![Lockdown folder is locked down](/THM-iOS-Forensics/docs/assets/images/lockdown-folder-permissions.png)
(The same issue occurs if you go to the folder via Finder, Command-Shift-G dialogue box).

[Adding an Administrator account on MacOS](https://www.fireebok.com/resource/how-to-fix-when-you-do-not-have-the-necessary-permission-on-mac.html)


In Linux/MacOS:
```
sudo find / -type f -name "*.plist" 2> /dev/null
```
In Windows:
```
Get-ChildItem -Path C:\ -Include *.plist -File -Recurse -ErrorAction SilentlyContinue
```
Open with ProperTree, Visual Studio or even a normal IDE/text editor.

In order to view backups on MacOS, you have to grant Full Disk Access to your terminal program: System Preferences > Security & Privacy > Privacy > Full Disk Access > Select Terminal

```
cd /Users/benleftwich/Library/Application Support/MobileSync/Backup/
```

