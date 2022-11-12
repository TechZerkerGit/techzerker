---
title: "Running i3 Desktop with WSL on Windows 10"
#subtitle: ""
date: 2020-04-19
draft: false
# images: []
tags: ["Linux","WSL","i3","i3wm","Windows 10","Windows Subsystem for Linux"]
categories: ["Tech","Linux"]
resources:
#- name: featured-image
#  src: bmwtest.jpg
#- name: featured-image-preview
#  src: bmwtest.jpg
---

While all my personal systems are exclusively running Linux, as is the nature of working in most IT Support roles, the base of my shared company workstation in the office is *Windows 10*. 

After a bunch of article reading, research and testing, this is a quick summary of what I use to have what has worked for me as a fully functioning [i3](https://i3wm.org/) graphical desktop, running via WSL (Windows Subsystem for Linux) on a functioning X-Server. For me at least, I've found it works much better than when I tried to have a VM running on the workstation, as it's far from new or high performance.
<!--more-->
I won't re-hash instructions that are widely available for getting the base WSL system installed and running, as it's pretty straight forward to get WSL feature enabled, and then go through the process to in my case, setup the Ubuntu WSL version straight from the Windows Store.

Once you have that up and running, so that you can get to your basic WSL command prompt...

![WSL Command Prompt](https://pixelfed.techzerker.com/storage/m/d0931bf747b992a1c83e055753526516f2706111/f47ca2c04ca946119f9a60462ac37f1097982fcc/qQt96GHElJuUdKs6Z5OAPumy2VwjBc9yiRvW0K1q.jpeg)

Obviously in my example, I've added a few things to my *.bashrc*, and in windows I've told it to let this window be a bit transparent.

Next up, you need a functioning X Server on windows that we can use to create the display. I tried a few, but in the end had the best luck with [VcXsrv](https://sourceforge.net/projects/vcxsrv/), which you can download from that link at *SourceForge*. No special instructions needed beyond getting it installed, ideally in the default paths.

Once that is installed, you can also go ahead and install your DE (Desktop Environment) in your WSL install. In my case, because it's my preference and it's light weight, I stuck with **i3**, so that's what I've tested here. That means your mileage may vary with other DE's. No special considerations for the install, in this case it was a standard `apt install i3` on a fully updated system.

Now comes the fun part of pulling it all together, these scripts are dirty and simple, I guarantee someone could write them to be cleaner or better looking, but they were written just for myself originally to this end. On my desktop I have:

**wsl.vbs**
```
' This script is meant to be launched from the Windows side, to start up a decorationless
' VcXsrv container for the environment.
'
' You may need to change this to reflect your VcXsrv install path as well as screen resolution.
' Then after the VcXsrv container is running, it pulls the WSL Ubuntu into it, along with a launch script.

Set shell = CreateObject("WScript.Shell" ) 
shell.Run """C:\Program Files\VcXsrv\vcxsrv.exe"" :0 -screen 0 @1 -ac +xinerama -engine 1 -nodecoration -wgl"
WScript.Sleep 200
shell.Run "ubuntu -c ""~/.scripts/wlaunch""", 0
```

Following that, before this actually works, as you can see, inside my WSL Ubuntu home directory, I'm calling a script called *wlaunch*:

**wlaunch**
```
#!/bin/bash

# meant to be run with `bash -c "/path/to/wlaunch"` when running from e.g. a Windows shortcut

# explicitly needed when launching with bash -c from Windows
source ~/.bashrc
export DISPLAY=:0
i3
```

Obviously, replace the last line calling i3 with something else if you are using a different environment, like XFCE, which I have not tested.

When that's all in place, all things being equal, you should be able to run *wsl.vbs*, and after a few seconds, be staring at your desktop environment! In my case, because its i3, I have beyond that heavily setup my own i3 config files, polybar, etc. for the look I want:

![i3 on WSL on Windows 10!](https://pixelfed.techzerker.com/storage/m/d0931bf747b992a1c83e055753526516f2706111/f47ca2c04ca946119f9a60462ac37f1097982fcc/cmB5QNEP72I7I7rsSc8hyPJPyIrvzwExHGVnogpC.jpeg)

After more than a year of this setup, I have yet to have any issues with any program. I've run things like Sublime Text, Firefox and Spacemacs all within this desktop environment without any issues, and with way better performance than I had on a VM. For easy workflow between the base Windows 10 system and this WSL i3, I've simply created symlinks for my core folders like *Documents* and my mapped network drive, given all drives in WSL1 are available at **/mnt/driveletter**

Hopefully this article was helpful to those that need it, it's certainly made my day to day usage on a Windows 10 machine be that much less...well...Windows!

If this article was helpful, and you're looking for more tools to help you work from home, even if you have to use Windows 10, why not check out [Humble Bundles *Remote Work* Bundle](https://www.humblebundle.com/software/work-remote-software?partner=techzerker), or take the rest of the day off, and catch up on some [Warhammer 40k Black Library](https://www.humblebundle.com/software/work-remote-software?partner=techzerker) reading.