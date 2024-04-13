<div align="center">
    <h1>WinProcessHide 应用进程隐藏</h1>
    <img src="https://img.shields.io/github/license/JasonYANG170/WinProcessHide?label=License&style=for-the-badge">
    <img src="https://img.shields.io/github/commit-activity/w/JasonYANG170/WinProcessHide?style=for-the-badge">
	<img src="https://img.shields.io/github/languages/count/JasonYANG170/WinProcessHide?logo=windows&style=for-the-badge">
	<br>
    	<a href="https://discord.com/invite/az3ceRmgVe"><img alt="Discord" src="https://img.shields.io/discord/978108215499816980?style=social&logo=discord&label=echosec"></a>
  <br>

这是一项基于Windows内核的应用进程隐藏，以至于包括系统软件在内的任何软件都无法检测到该应用，无需注入器，无需dll即可不被检测，适用于游戏黑客、电脑程序、外挂隐藏
  
<br>

</div>

## 适用于
WINDOWS 7  
WINDOWS 8  
WINDOWS 10  
WINDOWS 11  

## 原理
该隐藏思路是通过开启电脑内核调试功能，使用WinDbg从系统内核修改应用唯一的EPROCESS结构，从EPROCESS修改pid实现系统内核内隐藏应用进程，使得任何进程检测应用均无法检测。

## 使用教程
### 📌下载及开启调试模式
1.在微软应用商店下载WinDbg  

2.以**管理员权限**启动CMD或powershell，输入以下命令  

    - bcdedit -debug on
    - shutdown /r /t 0

此时电脑会开启内核调试模式并重启 
### 🔍查找16进制进程编号
3.以**管理员权限**启动WinDbg，按下**ctrl+k**选择**Local**继续  

4.顶部导航栏**View**选择**Command**，等待符号安装后即可进入内核调试命令行  

5.命令行输入!process 0 0 XXX.exe查找进程（XXX是你要隐藏的应用进程名，你可以在任务管理器查看）  

    - kd> !process 0 0  XXX.exe
    - PROCESS 8241d490  SessionId: none  Cid: 0178    Peb: 7ffdf000  ParentCid: 0004
    - DirBase: 02b40040  ObjectTable: e148a4a0  HandleCount:  19.
    - Image: XXX.exe

6.记下 **- PROCESS 8241d490** ，然后输入  

    - dt _eprocess 8241d490

此时你就可以看到XXX.exe应用的唯一的EPROCESS结构  

    - 0: kd> dt _eprocess
      nt!_EPROCESS
     +0x000 Pcb              : _KPROCESS
     +0x438 ProcessLock      : _EX_PUSH_LOCK
     +0x440 UniqueProcessId  : Ptr64 Void
     +0x448 ActiveProcessLinks : _LIST_ENTRY
     +0x458 RundownProtect   : _EX_RUNDOWN_REF
     ......
     
我们只用看 **+0x440 UniqueProcessId  : Ptr64 Void** 其中 **Void**前面的数字就是XXX.exe的16进制Pid号  
### 🕶️伪装
7.在任务管理器找一个**你想伪装的**应用，**右键**选择**转到详细信息**，记下该应用的Pid号，将该pid号从当前的10进制**转成16进制**，如abcd
   
8.输入 **ed 8241d490+0x440 abcd** 其中abcd是你想伪装的应用16进制Pid号，回车即可
### 🎖️验证
9.重复第6步查看是否修改成功，打开任务管理器检查，此时应用已经被隐藏，在任何检测软件中都是不可见的
