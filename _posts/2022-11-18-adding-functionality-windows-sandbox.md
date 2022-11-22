---
title: Adding functionality to Windows Sandbox
categories: [windows, sandbox]
tags: [operating systems, virtual machines]
---

## Adding functionality to Windows Sandbox
Windows Sandbox is a fast, functional and effective virtual environment introduced in 2019. It is mostly delivered as-is, albeit Microsoft added some configurability in 2020, with Windows 10 build 18342 and newer, or Windows 11.

## Configuration files
Configuration files of Windows Sandbox have .wsb file extension, they are formatted as XML and enable control of the following sections:

*	Audio input: Shares the host's microphone input into the sandbox.
*	Clipboard redirection: Shares the host clipboard with the sandbox so that text and files can be pasted back and forth.
*	Logon command: A command that's executed when Windows Sandbox starts.
*	Mapped folders: Share folders from the host with read or write permissions. Exposing host directories may allow malicious software to affect the system or steal data.
*	Memory in MB: The amount of memory, in megabytes, to assign to the sandbox.
*	Networking: Enable or disable network access within the sandbox.
*	Printer redirection: Shares printers from the host into the sandbox.
*	Protected client: Places increased security settings on the RDP session to the sandbox.
*	vGPU (virtualized GPU): Enable or disable the virtualized GPU. If vGPU is disabled, the sandbox will use Windows Advanced Rasterization Platform (WARP).
*	Video input: Shares the host's webcam input into the sandbox.

## Creating Shared Folders
Among the customizable options, most useful one is probably the possibility of direct access to the folders on the host within the Windows Sandbox itself. Basically it is a matter of creating a textual file with extension named as .wsb with following content:

```
<Configuration>
	<MappedFolders>
	  <MappedFolder> 
		<HostFolder>absolute path to the host folder</HostFolder> 
		<SandboxFolder>absolute path to the sandbox folder</SandboxFolder> 
		<ReadOnly>value</ReadOnly> 
	  </MappedFolder>
	</MappedFolders>
</Configuration>
```

After that, just run the created file and in desktop on virtual machine you’ll find the mounted shared folder. There are some additional options which are presented in detail <a href="https://learn.microsoft.com/en-us/windows/security/threat-protection/windows-sandbox/windows-sandbox-configure-using-wsb-file"  target="_blank">here</a>.

There is possibility to configure some settings in Windows Sandbox by using other means like adding the files manually and editing the registry, as described <a href="https://www.systanddeploy.com/2021/08/how-to-customize-default-windows.html" target="_blank">here</a>.

## Conclusion

Although it is possible to add some functionality to Windows Sandbox by using aforementioned means, its configurability is still very limited especially when compared to some other third party solutions, like <a href="https://sandboxie-plus.com/" target="_blank">Sandboxie</a>. Yet, since Windows Sandbox is included in the Windows itself, it is worth modding it a bit to better suit user's needs.



