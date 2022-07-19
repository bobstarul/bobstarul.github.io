---
layout: post
title: Practical Malware Analysis - Chapter_1L
subtitle: Labs Walkthrough
#cover-img: /assets/img/path.jpg
readtime: true
#thumbnail-img: /assets/img/thumb.png
#share-img: /assets/img/path.jpg
tags: [books, test]
---

# Chapter 1

These series of blogs will act as a note-taking environment for me but also as a walkthroogh for the Practical Malware Analysis Book.
Let's start of with the first labs.

As stated in the book, the purpose of the labs is to give the reader the opportunity to practice the skills that are taught in the chapters.

# Lab 1-1
The following block represents the exact assignment from the book:
>This lab uses the files Lab01-01.exe and Lab01-01.dll. Use the tools and techniques
described in the chapter to gain information about the files and
answer the questions below.

## Questions
>   1. Upload the files to http://www.VirusTotal.com/ and view the reports. Does
either file match any existing antivirus signatures?

In order to calculate the hash of the file, we will use the powershell *Get-FileHash* command:

```powershell
Get-FileHash
   [-Path] <String[]>
   [[-Algorithm] <String>]
   [<CommonParameters>]
```

Applied to our case, the command (run in cmd) will be : 

```powershell
powershell Get-FileHash -algorithm md5 Lab01-01.dll
```
for the md5 hash, and:

```powershell
powershell Get-FileHash -algorithm SHA256 Lab01-01.dll
```
for the sha256.

Using the above mentioned information, the hashes for the files are as follows:

**Lab01-01.dll**

**MD5**: 290934C61DE9176AD682FFDD65F0A669  
**SHA256**: F50E42C8DFAAB649BDE0398867E930B86C2A599E8DB83B8260393082268F2DBA

**Lab01-01.exe**

**MD5**: BB7425B82141A1C0F7D60E5106676BB1  
**SHA256**: 58898BD42C5BD3BF9B1389F0EEE5B39CD59180E8370EB9EA838A0B327BD6FE47

Now, let's get back to our questions.

**Yes**, the both of the files match the existing antivirus signatures   
The result for Lab01-01.exe:   
![](assets/img/Lab01-01.exe virustotal.png "Is this working?")

> 2. When were these files compiled?


In order to answer to this question, we can use two methods:    

- We can check the information from the details tab on VirusTotal, the History section, for the "Creation Time".
- We can check the **IMAGE_FILE_HEADER** in __PEview__ and look for the Time Date Stamp.

For **Lab01-01.exe**, the creation time is : 2010/12/19 Sun 16:16:19 UTC   
For **Lab01-01.dll**, the creation time is : 2010/12/19 Sun 16:16:38 UTC

> 3. Are there any indications that either of these files is packed or obfuscated?
If so, what are these indicators?

No, there are not any indicators that either of those files are packed or obfuscated.   
As it can be seen in the PEiD, both of them are compiled using *Microsoft Visual C++ 6.0 DLL*.


