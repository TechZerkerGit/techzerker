---
title: "Arch Linux: Blind Updating, PostgreSQL and MiniFlux"
#subtitle: ""
date: 2019-11-27
draft: false
# images: []
tags: ["Linux","Arch","Arch Linux","MiniFlux","PostgreSQL"]
categories: ["Tech","Linux"]
resources:
#- name: featured-image
#  src: bmwtest.jpg
#- name: featured-image-preview
#  src: bmwtest.jpg
---

Among the #self-hosted projects that I run just for my own usage, I have a [VPS server running Arch Linux](https://www.vultr.com/?ref=7975116-4F). (Yes, I'm running an Arch server, instead of Ubuntu/Debian)

That little VPS runs a few different services: 
- Minecraft server for a group of friends, which is the heavy memory user
- [Write Freely](https://writefreely.org) instances for a few subjects
- Subsonic Music streaming server
- Resilio Sync encrypted storage target
- MiniFlux RSS Server/Reader
<!--more-->
In general, it ticks along without much need for attention. Usually it's biggest time demands are after each major Minecraft version update (11.x, 12.x, etc.) to adjust settings as it gets more and more resource hungry. 

> FYI: The best solution on 14.x now has been [PaperMC](https://papermc.io/)

Given I work in tech and personally work with Arch Linux plenty, I make sure to run updates on the server weekly, and they almost always are pretty basic updates without much significance. Generally update with my AUR Helper, check the applications, and off I go.

This time around this past weekend, but only discovered last night, the updates included an update of PostgreSQL from 11.6 to 12.x. On this server, MiniFlux RSS uses PostgreSQL for it's backend, and it works well. However, in my past experience I have never had anything use PostgreSQL to have had much awareness about it's sensitivity in upgrading versions. As such, the update itself did not fail, but when I went to check my Android app which pulls from MiniFlux (via Fever API), it showed it had not pulled for several days.

A quick investigation showed that MiniFlux itself did not have an update, but PostgreSQL had indeed updated and the service would not start, according to systemd. A quick check in journalctl -xe, and the source was found as incompatible database versions for PostgreSQL.

From that point, it was thankfully not a hard fix, likely because the database for the RSS system is not that large or complex. Referring to the faithful [Arch Wiki](https://wiki.archlinux.org/index.php/PostgreSQL#Upgrading_PostgreSQL) sent me through a few commands to migrate the data to a backup folder, initialize a new database set under version 12, and then use the `pg_upgrade` function to upgrade the database to that version. PG_Upgrade did it's job without fail on the first run, and I was able to then start the PostgreSQL service, followed by a restart of the MiniFlux service.

All in all, not a hard process or experience, just a good reminder to keep an eye on packages updating. It's ideal to be aware of what you are updating, but sometimes an innocent looking update breaks things. When it's a hobby server, keep good backups and be willing to research the fix, and you'll learn a bit along the way.

I'm not sure what my next project will be to add to my #Self-Hosted systems, but I've started catching up on the [Self-Hosted Podcast](https://selfhosted.show/) from Jupiter Broadcasting, so I'm sure I'll discover something new!

If you don't yet #Self-Host and are looking for a project, I run my projects currently on Vultr, and sticking with plain [direct referral link](https://www.vultr.com/?ref=7975116-4F), you can help me out by giving [Vultr](https://www.vultr.com/?ref=7975116-4F) a try with $50 worth of hosting, and it helps me out as well.

> I have a About / Privacy page linked at the top where I talk about my privacy rules: Text only referral links, no cookies/trackers, no ugly banners or graphics interrupting the writing. 