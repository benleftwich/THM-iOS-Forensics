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
![iOS lockdown certificate](/THM-iOS-Forensics/docs/assets/images/iOSlockdowncertificate.png)

On the computer, in this case Windows, this certificate is stored in ```C:\ProgramData\Apple\Lockdown```
![Windows lockdown certificate](/THM-iOS-Forensics/docs/assets/images/Windowslockdowncertificate.png)

The certificate within the computer contains the private keys used to encrypt and decrypt against the iPhones public key.

#### Trust Certificates Explained

Trust certificates aren't permanent by design and will eventually require you to re-trust the device. Trust certificates, at an abstract work in the following way:

iTunes will generate a certificate using the iPhone's unique identifier once data read/write has been allowed by trusting the computer on the iPhone.
This certificate will be stored on the trusted computer for 30 days. Afterwhich you will need to re-trust the device. However, the certificate that is generated can only be used for 48 hours since the user has last unlocked their iPhone. Let's break this down:

If the iPhone has been connected to a trusted computer but the iPhone hasn't been unlocked in a week, the certificate won't be used although it is still valid. Once the iPhone is unlocked, the iPhone will automatically allow read/write access by the trusted computer without the "Trust This Computer" popup. However, if you were to connect the iPhone to the trusted computer 6 hours since it was last unlocked, the iPhone will allow read/write access straight away.

#### How can We Utilise These Trust Certificates?

First, we need to understand the backups that iTunes creates. iTunes allows for two types of backups resulting in different amounts of data being backed up onto the computer and ultimately how it should be analysed: "Unencrypted" and "Encrypted"


Unencrypted backups are simply that - unencrypted. Perfect! We'll have a copy of photos that aren't synced to iCloud, a copy of browsing history and the likes. However, no passwords or health and Homekit data - these are only backed up if the "Encrypted" option is set by the user.

Remembering that iTunes accesses the iPhone with elevated privileges using lockdown certificates, we can extract data from the iPhone such as the keychain. This keychain includes (but isn't limited) to passwords such as:

Wi-Fi Passwords
Internet Account Credentials from " Autofill Password"
VPN
Root certificates for applications
Exchange / Mail credentials

Suddenly, you have the credentials to the suspect's accounts and the likes.

I thoroughly encourage you to read this [excellent article](https://farleyforensics.com/2019/04/14/forensic-analysis-of-itunes-backups/) on how iTunes backups can be forensically analysed with relative ease.

What is the name of a forensics tool that couldn't be used in a court of law, because data could be written to the device being analysed?
> iFunbox

You've found an iPhone with no passcode lock, what acquisition method would you use?    
> Direct Acquisition 

What is the name of the certificate that gets stored on a computer when it becomes trusted?
> Trust/Lockdown Certificate

# Analysing iOS Files
#### Plists
Apple, in true Apple fashion, have their own standardisation for files within their file systems. Presenting in the extension of plist, these files are property files who consist of data from anything such as preferences to application settings and data.

For example, in this example iPhone dump, there is a log file named ```ResetCounter.plist```
![ResetCounter.plist](/THM-iOS-Forensics/docs/assets/images/ResetCounterPlist.png)

When opening the file, we can see it is of the formatting of an XML document. All that's contained within this specific file is the number of times the device has been "Hard Reset". On the iPhone specifically, this counter increments when you force restart the phone by holding down "Home" and "Power Button". Information like this is kept for diagnostics, however, if you think you've bought a new iPhone - it's worth taking a look!

Whilst this file, in particular, was XML formatted, you may come across some that cannot be opened with a text editor. Take for example com.apple.preferences.datetime.plist:
![Binary plist example](/THM-iOS-Forensics/docs/assets/images/com.apple.preferences.datetime.plist.png)

We'd need to use a hex editor such as HxD to view the information stored within this plist:
![Binary plist example, HxD](/THM-iOS-Forensics/docs/assets/images/HxD.png)

Where we now discover that the data encoded within this plist reveals the timezone of the iPhone, in this case, the device is set to the Europe/London timezone.

#### Finding plist files on your computer
In Linux/MacOS:
```
sudo find / -type f -name "*.plist" 2> /dev/null
```
In Windows:
```
Get-ChildItem -Path C:\ -Include *.plist -File -Recurse -ErrorAction SilentlyContinue
```
Open with any text editor, in some cases you will need to use a hex editor to view the ones which have binary data.

#### iOS Backups

In order to view backups on MacOS, you have to grant Full Disk Access to your terminal program: System Preferences > Security & Privacy > Privacy > Full Disk Access > Select Terminal

```
cd /Users/benleftwich/Library/Application Support/MobileSync/Backup/
```

# Task 9: Scenario: Operation JustEncase (Deploy)
Who was the recepient of the SMS message sent on 23rd of August 2020?
> Lewis Randall

What did the SMS message say?
> Did you get the goods?

Looking at the address book, what is the first name of the other person in the contacts?
> Jenny

Following on from Question #3, what is their listed "Organization"
> Transportation

Investigate their browsing history, what is the address of the website that they have bookmarked?    
> https://blog.cmnatic.co.uk

The suspected received an email, what is the remote_id of the sender?    
> 51.32.56.12

What is the name of the company on one of the images stored on the suspects phone?
> TryHackMe
 
What is the value of the cookie that was left behind?
> THM{COOKIES!!!}
