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

In order to calculate the hash of the file, we will use this command:

```powershell
Get-FileHash
   [-Path] <String[]>
   [[-Algorithm] <String>]
   [<CommonParameters>]
```
