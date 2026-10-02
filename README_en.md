[简体中文](README.md) | [English](README_en.md)

<div align="center">
    <h1>WinProcessHide — application process hiding</h1>
    <img src="https://img.shields.io/github/license/JasonYANG170/WinProcessHide?label=License&style=for-the-badge">
    <img src="https://img.shields.io/github/commit-activity/w/JasonYANG170/WinProcessHide?style=for-the-badge">
	<img src="https://img.shields.io/github/languages/count/JasonYANG170/WinProcessHide?logo=windows&style=for-the-badge">
	<br>
    	<a href="https://discord.com/invite/az3ceRmgVe"><img alt="Discord" src="https://img.shields.io/discord/978108215499816980?style=social&logo=discord&label=echosec"></a>
  <br>

This is an application process hiding based on the Windows kernel, so that no software, including system software, can detect the application. No injector or dll is needed to avoid detection. It is suitable for game hackers, computer programs, and plug-in hiding.
  
<br>

</div>

## Applies to
WINDOWS 7  
WINDOWS 8  
WINDOWS 10  
WINDOWS 11  

## Principle
The hiding idea is to enable the computer kernel debugging function, use WinDbg to modify the application's unique EPROCESS structure from the system kernel, and modify the pid from EPROCESS to hide the application process in the system kernel, making it impossible to detect any process detection application.

## Tutorial
### 📌Download and enable debugging mode
1. Download WinDbg from Microsoft App Store

2. Start CMD or powershell with **administrator rights** and enter the following command

    - bcdedit -debug on
    - shutdown /r /t 0

At this time, the computer will turn on the kernel debugging mode and restart
### 🔍Find hexadecimal process number
3. Start WinDbg with **administrator rights**, press**ctrl+k** and select**Local** to continue

4. Select **Command** in the top navigation bar**View**, wait for the symbols to be installed, and then enter the kernel debugging command line.

5. Enter !process 0 0 XXX.exe on the command line to find the process (XXX is the name of the application process you want to hide, you can view it in the task manager)

    - kd> !process 0 0  XXX.exe
    - PROCESS 8241d490  SessionId: none  Cid: 0178    Peb: 7ffdf000  ParentCid: 0004
    - DirBase: 02b40040  ObjectTable: e148a4a0  HandleCount:  19.
    - Image: XXX.exe

6. Write down **- PROCESS 8241d490** and enter

    - dt _eprocess 8241d490

At this point you can see the only EPROCESS structure of the XXX.exe application

    - 0: kd> dt _eprocess
      nt!_EPROCESS
     +0x000 Pcb              : _KPROCESS
     +0x438 ProcessLock      : _EX_PUSH_LOCK
     +0x440 UniqueProcessId  : Ptr64 Void
     +0x448 ActiveProcessLinks : _LIST_ENTRY
     +0x458 RundownProtect   : _EX_RUNDOWN_REF
     ......
     
We only need to look at **+0x440 UniqueProcessId: Ptr64 Void** where the number in front of**Void** is the hexadecimal Pid number of XXX.exe
### 🕶️Disguise
7. Find an application you want to disguise in the task manager, right-click and select Go to details, write down the Pid number of the application, and convert the Pid number from the current decimal to hexadecimal, such as abcd
   
8. Enter **ed 8241d490+0x440 abcd** where abcd is the hexadecimal Pid number of the application you want to disguise, and press Enter.
### 🎖️Verification
9. Repeat step 6 to see if the modification is successful. Open the task manager to check. At this time, the application has been hidden and is invisible in any detection software.

## If you like this project, please give me a star ⭐

[![Star History Chart](https://api.star-history.com/svg?repos=JasonYANG170/WinProcessHide&type=Date)](https://star-history.com/#star-history/star-history&Date)
