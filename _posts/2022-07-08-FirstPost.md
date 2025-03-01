---
layout: post
title: Practical Malware Analysis - Static Techniques
subtitle: Chapter 1 - Labs Walkthrough
#cover-img: /assets/img/path.jpg
readtime: true
#thumbnail-img: /assets/img/thumb.png
#share-img: /assets/img/path.jpg
tags: [books, test]
---

# Chapter 1

These series of blogs will act as a note-taking environment for me but also as a walkthroogh for the Practical Malware Analysis Book.  
Let's start off with the first labs.

## Lab 1-1
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

![DependencyWalker_Lab01-01.dll_CreateProcess][DepWalkerLab01-01.dll_CreateProcess]

Also, finding the IP address : 127.26.152.13, and the socket specific functions, such as _connect_,_bind_ imported from _WS2_32.DLL_ led us into thinking that it might connect to a C2 server in order to receive further commands, or to download other malware.

![DependencyWalker_Lab01-01.dll_Socket][DepWalkerLab01-01.dll_socket]

**Lab01-01.exe**  
The _.exe_ imports functions such as _FindNextFileA_, _FindFirstFileA_, _CreateFile_, _CopyFile_, indicate that the malware will search for specific files and also create and copy some of those.

![DependencyWalker_Lab01-01.exe Imports][DepWalkerLab01-01.exe_Imports]

> 5.Are there any other files or host-based indicators that you could look for
on infected systems?

Yes. If we look inside the **Lab01_01.exe** file, we find the string _C:\windows\system32\kerne132.dll_ which is an attempt to trick the user into thinking that it is the same with _Kernel32_. 

> 6.What network-based indicators could be used to find this malware on
infected machines?

The IP address _127.26.152.13_ found in the _.dll_ can be an indicator to find this malware on infected machines.

> 7.What would you guess is the purpose of these files?

The purpose of the _.exe_ file is to find and run the _.dll_ file, which in turn connects to the C2 server at _127.26.152.13_. From there, malicious files can be downloaded.


# Lab 1-2

> 1.Upload the Lab01-02.exe file to http://www.VirusTotal.com/. Does it match
any existing antivirus definitions?

Yes, it does. 55 vendors flagged this file as malicious.
![VirusTotal_Lab01-02.exe_Result][Lab01-02.exe_VirusTotal]

> 2.Are there any indications that this file is packed or obfuscated? If so,
what are these indicators? If the file is packed, unpack it if possible.

As it can be seen in PEiD, the entry point is UPX1. And, recalling the information in the book, the UPX packer changes the name of the sections in such strings.
![PEiD_UPX_Lab01-02.exe][PEiD_UPX_Lab01-02.exe]

The _.UPX0_ section has a raw size of _2048 bytes_ and a virtual size of _24756 bytes_, which indicates that a packer will unpack the executable code to the allocated _.text_ section.  
![PeStudio_Lab01-02_sections][PeStudio_Lab01-02_sections]  
So, observing the _UPX0_, _UPX1_, _UPX2_ sections, we can surely say that the file is packed with the open-source UPX packer.  
To unpack it, we can download the UPX tool from [github](https://upx.github.io/).  
Now, let's run the unpacker:
![Upx_command_Lab01-02][Lab01-02_UPX]    
After the unpacking process, we run _PeID_ again and find that this file was compiled using _Microsoft Visual C++ 6.0_.  
Opening the file in _PEStudio_, we find a lot more informations about the file's strings and imports.

> 3.Do any imports hint at this program’s functionality? If so, which imports
are they and what do they tell you?

The imports that are visible are:   

![PEStudio_Imports_Lab-01-02_Unpacked.exe][PEStudio_Imports_Lab-01-02_Unpacked.exe]    

We can assume that the malicious file will try to connect to the _http://www.malwareanalysisbook.com_ URL and bind it to a serice in order to maintain persistence. This might be a way to connect to the C2 server in order to receive further commands or download other malicious files.



> 4.What host- or network-based indicators could be used to identify this
malware on infected machines?

 In order to identify this malware on infected machines, we can look for a running service named _MalService_ and also check for connections to the URL _http://www.malwareanalysisbook.com_ .
 
## Lab 1-3
 
 > 1.Upload the Lab01-03.exe file to http://www.VirusTotal.com/. Does it match
any existing antivirus definitions?

As it can be seen, the files matches 62 of the existing antivirus definitions.
![Q1_VirusTotal_Lab01-03][Q1_VirusTotal_Lab01-03]

> 2.Are there any indications that this file is packed or obfuscated? If so,
what are these indicators? If the file is packed, unpack it if possible.

PEiD lets us know that FSG 1.0 packer was used for this file. 
![PEiD_Lab01-03][Q2_PEiD_Lab01-03]

Also, the virtual size of _20480 bytes_ is way larger than the raw size of _1164 bytes_.
![Q2_PeiD_Lab01-03_Sections][Q2_PeiD_Lab01-03_Sections]

> 3.Do any imports hint at this program’s functionality? If so, which imports
are they and what do they tell you?

The only imports that we can find in this file are _LoadLibraryA_ and _GetProcAddress_. 
![Q3_PEStudio_Functions][Q3_PEStudio_Functions]

The above specified functions can cause other modules to be loaded during the calling process. But, with the information we have right now, we can't say for certain which are those.


> 4.What host- or network-based indicators could be used to identify this
malware on infected machines?

As of this moment, there aren't any host- or network-based indicaters that we could use to identify this malware on infected machines.

## Lab 1-4

> 1. Upload the Lab01-04.exe file to http://www.VirusTotal.com/. Does it match
any existing antivirus definitions?

Yes, it does.

![Q1_VirusTotal_Lab01-04][Q1_VirusTotal_Lab01-04]

> 2.Are there any indications that this file is packed or obfuscated? If so,
what are these indicators? If the file is packed, unpack it if possible.

The file is not packed, or obfuscated, as we can find a lot of strings and a lot of the files that are being used.
All the sections, _.text_, _.rdata_, _.data_ and _.rsrc_ are visible.
Using PEiD, we find out that it is compiled using _Microsoft Visual C++ 6.0_.  

![Q2_PEiD_Lab01-04][Q2_PEiD_Lab01-04]


> 3.When was this program compiled?

This program was compiled on Fri Aug 30 22:26:59 2019 | UTC.   

![Q3_PEStudio_Compile-Time_Lab01-04][Q3_PEStudio_Compile-Time_Lab01-04]


> 4.Do any imports hint at this program’s functionality? If so, which imports
are they and what do they tell you?

Yes.

![Q4_PeStudio1_Lab01-04][Q4_PeStudio1_Lab01-04]

The malware may try to create the files _\system32\wupdmgrd.exe_, _\system32\wupdmgr.exe_, _\system32\wupdmgr.exe_,_\winup.exe_,  to mimic Windows Update Manager. More than this, it will download another executable from _http://www.practicalmalwareanalysis.com/updater.exe_.  
Also, it may use _psapi.dll_ to look for process statuses and most likely maintain persistence. Also, _sfc_os.dll_ is used to monitor system files for validity.  
The _LookupPrivelegeValue_ function is used to check the privilege level. Along with _AdjustTokenPrivileges_, it might try to change the privileges. 

![Q4.1_PeStudio1_Lab01-04][Q4.1_PeStudio1_Lab01-04]

_LoadResource_ and _SizeofResource_ is used to load the resource found in the file.


> 5.What host- or network-based indicators could be used to identify this
malware on infected machines?

The presence of the above mentiond executables and the connection to the URL can be indicators that could be used to identify this malware on infected machines.

> 6.This file has one resource in the resource section. Use Resource Hacker
to examine that resource, and then use it to extract the resource. What
can you learn from the resource?

The resource is an executable.

![Q5_Resource-Hacker_Lab01-04][Q5_Resource-Hacker_Lab01-04]

Using PEStudio we can observe that there we find the same strings as stated above. It also downloads and runs the _updater.exe_ file.


[PEiD]:/assets/img/Lab1-1/PeiD_MicrosoftVisualC++6.0.png
[DepWalkerLab01-01.dll_CreateProcess]:/assets/img/Lab1-1/DepWalker_Lab01-01.dll_CreateProcess.png
[DepWalkerLab01-01.dll_socket]:/assets/img/Lab1-1/DepWalker_Lab01-01.dll_socket.png
[DepWalkerLab01-01.exe_Imports]:/assets/img/Lab1-1/DepWalker_Lab01-01.exe_Imports.png

[Lab01-02.exe_VirusTotal]:/assets/img/Lab1-2/Question1_VirusTotal_Lab01-02.exe.png
[PEiD_UPX_Lab01-02.exe]:/assets/img/Lab1-2/Lab01-02.exe_PEiD.png
[PeStudio_Lab01-02_sections]:/assets/img/Lab1-2/Question2_PeStudio_Lab01-02.exe.png
[Lab01-02_UPX]:/assets/img/Lab1-2/Question2_UPX_Lab01-02.exe.png
[PEStudio_Imports_Lab-01-02_Unpacked.exe]:/assets/img/Lab1-2/Question3_PeStudio_Lab01-02.exe.png

[Q1_VirusTotal_Lab01-03]:/assets/img/Lab1-3/Q1_VirusTotal_Lab01-03.png
[Q2_PEiD_Lab01-03]:/assets/img/Lab1-3/[Q2_PEiD_Lab01-03].png
[Q2_PeiD_Lab01-03_Sections]:/assets/img/Lab1-3/[Q2_PeiD_Lab01-03_Sections].png
[Q3_PEStudio_Functions]:/assets/img/Lab1-3/[Q3_PEStudio_Functions].png

[Q1_VirusTotal_Lab01-04]:/assets/img/Lab1-4/Q1_VirusTotal_Lab01-04.png
[Q2_PEiD_Lab01-04]:/assets/img/Lab1-4/Q2_PEiD_Lab01-04.png
[Q3_PEStudio_Compile-Time_Lab01-04]:/assets/img/Lab1-4/Q3_PEStudio_Compile-Time_Lab01-04.png
[Q4_PeStudio1_Lab01-04]:/assets/img/Lab1-4/Q4_PeStudio1_Lab01-04.png
[Q4.1_PeStudio1_Lab01-04]:/assets/img/Lab1-4/Q4.1_PeStudio1_Lab01-04.png
[Q5_Resource-Hacker_Lab01-04]:/assets/img/Lab1-4/Q5_Resource-Hacker_Lab01-04.png
