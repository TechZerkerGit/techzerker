---
title: "I Want To Like WSL2"
#subtitle: ""
date: 2020-11-05
draft: true
# images: []
tags: ["WSL","Windows"]
categories: ["Tech","Windows"]
resources:
#- name: featured-image
#  src: file.jpg
#- name: featured-image-preview
#  src: file.jpg
---

As the title says, I *want to like* WSL2. 
I have written in the past a bit about what I setup with WSL1 and i3 on Windows 10, given the work environments are predominatly Windows, and it works well most of the time. It's not perfect, and it's not a full Linux system, but it works in a *Microsoft Shop* type environment. Given how well it's worked, one would expect all the advancements in WSL2 would grant a better experience and more compatibility, especially with the proper Linux kernel baked in.

<!--more-->

## My WSL2 Experience

I started attempting to replicate my setup under WSL2 when I recently changed jobs and was setting up a new workstation for this new support role. The core terminal tools and operations all worked the same, although I didn't notice any performance differences.

Where I first noticed issues, was a test install where I used X410 and Pengwin WSL Linux to load a Linux build of FireFox. Granted, this is not an actual planned usage, as Firefox could just be run in windows, but I was testing out performance differences and X410 window options. While FireFox loaded quick and easy, it was failing to load any website. Initially I expected this was simply an issue with Firefox not playing nice with WSL2 and how X410 was displaying the X Display. Unfortunatly, it was not. I followed up this test with Chrome and w3m, with the same results. This problem continued to expand into the inability to add any new APT Repository (even though the base set were pulling updates fine).

My testing produced the same results with Ubuntu 20.04 and Fedora on WSL2, ruling out Pengwin or a bad install as the cause. After realitivly little searching, I found a long history of help and forum posts around network issues in WSL2, with it's VM(ish) container approach. I was not taking notes at the time to detail all the fixes I tried, but I went through various lists of fixes internal to WSL2, as well as via Hyper-V settings and vSwitches. All to no avail and much fustration. 
