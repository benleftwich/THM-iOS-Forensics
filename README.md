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

AFS or Apple File System (creative right...!) boasts many features, including full disk encryption, worrisome for analysts considering all devices past iOS 10.3 will have this system structure. But from a design point of view, AFS introduces smarter data management such as in the screenshot below, where a file requiring 3 blocks worth of space when copied, would require another 3 blocks again.

![Copying a file HFS+ vs. AFS](/THM-iOS-Forensics/docs/assets/images/Copying a file HFS+ vs. AFS.png)
