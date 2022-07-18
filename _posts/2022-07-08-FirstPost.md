---
layout: post
title: Practical Malware Analysis - Chapter_1L
subtitle: Labs Walkthrough
cover-img: /assets/img/path.jpg
readtime: true
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
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
>   Upload the files to http://www.VirusTotal.com/ and view the reports. Does
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

# **Lab01-01.dll**

**MD5**: 290934C61DE9176AD682FFDD65F0A669  
**SHA256**: F50E42C8DFAAB649BDE0398867E930B86C2A599E8DB83B8260393082268F2DBA

# **Lab01-01.exe**

**MD5**: BB7425B82141A1C0F7D60E5106676BB1  
**SHA256**: 58898BD42C5BD3BF9B1389F0EEE5B39CD59180E8370EB9EA838A0B327BD6FE47

