---
title: Windows Sandbox overview - Enabling, Features, Usability, Security
categories: [windows, sandbox]
tags: [operating systems, virtual machines]
---

Over the time, Windows 10 got some useful features that are not always obvious or even enabled. Many users are not even aware of their existence. It turns out that the Windows for some time now sports a fully-fledged virtualization engine (called Hyper-V) and an instant-sandbox called simply Windows Sandbox. In this article we will take a closer look at the Windows Sandbox, to see what it is all about.

## What it is

Windows Sandbox is an instance of a fresh Windows installation that starts within Windows itself. It is a clean Windows virtual machine that doesn’t need to be set up or preinstalled like traditional virtual machine engines (like beforementioned Hyper-V). It just runs from scratch, enabling the user fully-fledged Windows environment which is totally separated from host. Being a sandboxed, it does not remember its state.

## How to enable it

Windows Sandbox is not supported on Home versions of either Windows 10 or 11. Windows 10 supported versions (Pro, Enterprise, Education) need to be build 18305 or newer – Windows Sandbox was introduced in May 2019. so, every supported and updated Windows is good to go. 
Windows Sandbox is not enabled by default; therefore, it doesn’t show in the Start Menu. Prior to enabling it, the system needs to support a Virtualization option in BIOS: its state can be checked in the Task Manager:

![](https://sbozich.github.io/assets/13112201.jpg)
 
If the host is virtualized itself (i.e., run in the virtual machine itself), the Nested Virtualization is required to run the Windows Sandbox. Enabling it is a matter of starting PowerShell and entering following command:

```
Set-VMProcessor -VMName <VMName> -ExposeVirtualizationExtensions $true
```

Once the virtualization is enabled, the Windows Sandbox should be enabled by going to Control Panel > Programs > Turn Windows Features On or Off:

![](https://sbozich.github.io/assets/13112202.jpg)
 
After the system restarts, Windows Sandbox appears in the Start Menu and it can be started:

![](https://sbozich.github.io/assets/13112203.jpg)
 
 
## Features
The Windows Sandbox is a live, clean-slate image derived from the host operating system. That means it sports the same Windows version as host and it is already updated to the same state (in stark contrast to the virtualized Windows system which doesn’t share any traits to the underlying OS). Some important aspects of Windows Sandbox are:

*	<b>It is a part of the Windows</b> – no separate download is necessary (in respect to the Windows build requirement)
*	<b>It is a one-time session</b>, meaning that it does not remember the state after the restart (for instance installed programs and copied files will disappear after the restart or closing down the program),
*	<b>It is isolated from the host</b> – the changes done inside will not impact the host operating system (with some remarks later explained),
*	<b>It runs rather quick even on some older hardware</b>.

### Transferring content
There are two possibilities to copy files from the host to the Windows Sandbox. The first option is usual copying through Copy (Ctrl+C) and Paste (Ctrl+V). Note that the files’ drag-and-drop is not supported. Other way is configuring a <b>shared folder</b> between host PC and Windows Sandbox which will be covered in a separate article.

## Usability
1.	Software testing

    Windows Sandbox is perfect companion when it comes to software testing. Fire it up, install the program, run it and play with it, change parameters, evaluate it. Once the Sandbox is closed, nothing escapes it – no harm done. It is perfect for power users, developers, testers.
2.	Windows options exploration

    Inside Windows Sandbox, user can freely explore the myriad of Windows options (those options that do not require restart though), figure-out what they do, how they behave - all without fear of system failure. 
3.	Browsing the Web

    Since Microsoft Edge comes preinstalled, user can freely surf the Internet without having to worry about cookies, ads and related stuff that may impact the system. Inside Windows Sandbox, there is no need to clear cookies ever.
    Note that the Internet connectivity within Windows Sandbox does not work if VPN on host system enabled.

## Security

Using Windows Sandbox in most cases is isolated from the host, yet there are two potential security issues with it:
1.	Windows Sandbox by default utilizes Windows Defender antivirus, which offers decent protection against malware. Due to the temporary nature of Windows Sandbox itself, the proper antivirus suite can’t be installed so the user is limited to a Windows Defender, which lacks some security features like behavioral analysis needed to detect zero-day (unpatched) threats. In that way, user can wrongly assume that the file is safe and use it on the host system, thus endangering the host and the network. 

    <b>Advice:</b> use online virus scan, just to be sure.

2.	When it comes to networking, Windows Sandbox is protected by NAT (network address translation), preventing network devices to initiate access to it. However, in some cases there is possible to detect the subnet and therefore the network devices on the host system – enabling the scan for the opened ports on those devices, which pose a security risk. 

    <b>Hint:</b> for true isolated experience, disable the network adapter on Windows Sandbox when testing for malware.

## Conclusion
Windows Sandbox is a neat useful Windows feature which gives the look and feel of the fresh, unbloated system. Being very handy to use, it can give additional value to the user experience and a boost in productivity and security.
