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
>   1.Upload the files to http://www.VirusTotal.com/ and view the reports. Does
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
for sha256.

Using the above mentioned information, the hashes for the files are as follows:

**Lab01-01.dll**

**MD5**: 290934C61DE9176AD682FFDD65F0A669  
**SHA256**: F50E42C8DFAAB649BDE0398867E930B86C2A599E8DB83B8260393082268F2DBA

**Lab01-01.exe**

**MD5**: BB7425B82141A1C0F7D60E5106676BB1  
**SHA256**: 58898BD42C5BD3BF9B1389F0EEE5B39CD59180E8370EB9EA838A0B327BD6FE47

Now, let's get back to our questions.

**Yes**, both of the files match the existing antivirus signatures   
The result for Lab01-01.exe:   
![Virus total analysis of Lab01-01.exe](/assets/img/Lab01-01.exe%20virustotal.png)

> 2.When were these files compiled?


In order to answer this question, we can use two methods:    

- We can either check the information from the details tab on VirusTotal, the History section, for the "Creation Time".
- Or, we can check the **IMAGE_FILE_HEADER** in __PEview__ and look for the Time Date Stamp:
   ![PEStudio view of Lab01-01.dll showing compiler-timetstamp](/assets/img/Lab1-1/Lab1-1_lab01-01.dll_PEStudio.png)

For **Lab01-01.dll**, the creation time is : 2010/12/19 Sun 16:16:38 UTC  
For **Lab01-01.exe**, the creation time is : 2010/12/19 Sun 16:16:19 UTC  

> 3.Are there any indications that either of these files is packed or obfuscated?
If so, what are these indicators?

No, there are not any indicators that either of those files are packed or obfuscated.   
As it can be seen in the PEiD, both of them are compiled using *Microsoft Visual C++ 6.0 DLL*.
![PEiD - Compiled using Microsoft Visual C++ 6.0 DLL][PEiD]

> 4.Do any imports hint at what this malware does? If so, which imports
are they?

**Lab01-01.dll**
Taking into consideration that the functions _CloseHandle_, _CreateMutexA_, _CreateProcessA_, _OpenMutexA_ and _Sleep_ are imported from _KERNEL32.DLL_, we can assume that the _.dll_ will start a process, and sleep (pause its execution) for a certain amount of time, most likely as an evasive measure.
[DependencyWalker_Lab01-01.dll_CreateProcess][DepWalkerLab01-01.dll_CreateProcess]
Also, finding the IP address : 127.26.152.13, and the socket specific functions, such as _connect_,_bind_ imported from _WS2_32.DLL_ lead us to think that it might connect to a C2 server in order to receive further commands, or to download other malware.
[DependencyWalker_Lab01-01.dll_Socket][DepWalkerLab01-01.dll_socket]

**Lab01-01.exe**
The _.exe_ imports functions such as _FindNextFileA_, _FindFirstFileA_, _CreateFile_, _CopyFile_, indicate that the malware will search for specific files and also create and compy some of them.
[DependencyWalker_Lab01-01.exe Imports][DepWalkerLab01-01.exe_Imports]

> 5.Are there any other files or host-based indicators that you could look for
on infected systems?

Yes. If we look inside the **Lab01_01.exe** file, we find the string _C:\windows\system32\kerne132.dll_ which is an attempt to trick the user into thinking that it is the same with _Kernel32_. 

> 6.What network-based indicators could be used to find this malware on
infected machines?

The IP address _127.26.152.13_ found in the _.dll_ can be an indicator to find this malware on infected machines.

> 7.What would you guess is the purpose of these files?

The purpose of the _.exe_ file is to find and run the _.dll_ file, which in turn connects to the C2 server at _127.26.152.13_. From there, malicious files can be downloaded.










[PEiD]:
[DepWalkerLab01-01.dll_CreateProcess]:
[DepWalkerLab01-01.exe_Imports]:
