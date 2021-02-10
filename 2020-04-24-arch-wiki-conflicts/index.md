# Arch Linux – Conflicting Files and the Arch Wiki


It is no secret that my favorite distro for Linux after much trial and error, landed on [Arch Linux](https://archlinux.org). 

I found I prefer the rolling release model vs major version upgrades and the AUR (Arch User Repository) is incredible for finding and installing packages. That being said, it's biggest win is the [Arch Wiki](https://wiki.archlinux.org/). I find however, that no matter how often that is repeated in the Arch circles, you still find forums full of solutions that the Arch Wiki covers better, or even conflict the Wiki.
<!--more-->
I wrote this today, because I saw that example again in searching, where probably due to some previous mistake, when I went to update my system today, I was getting a failure for a pair of conflicting packages, which prevents updating as part of Arch giving you a chance to make sure you know what your doing...

A search for [*Arch Pacman + Conflicting Files*](https://duckduckgo.com/?q=arch+pacman+%2B+conflicting+files) returns all sorts of results, from various BBS Sites, Reddit, and Forums, with the Arch Wiki nestled tightly among them.

However, in a rather large number of the forums I took a quick browse at, were suggestions like: `pacman -Syuf` and `pacman -Suf`, among others, which most importantly are encouraging the `f` *force* flag. In some cases, other responses speak up right away much as I am, and suggest not taking that approach, as it's a very strong-handed and dangerous approach to updating an Arch system.

Sure enough, in a bunch of cases (not a scientific sampling), there are follow-up posts of panic stricken users, who range from *system won't boot* to *pacman is completely broken*, and other results. Where a quick search for this same problem to the Arch Wiki, leads you down the *Pacman* page to it's [troubleshooting section](https://wiki.archlinux.org/index.php/Pacman#%22Failed_to_commit_transaction_(conflicting_files)%22_error).

The core solution detailed:

> This is happening because pacman has detected a file conflict, and by design, will not overwrite files for you. This is by design, not a flaw.
>
>The problem is usually trivial to solve. A safe way is to first check if another package owns the file (`pacman -Qo /path/to/file`). If the file is owned by another package, file a bug report. If the file is not owned by another package, rename the file which 'exists in filesystem' and re-issue the update command. If all goes well, the file may then be removed.

Sure enough, in my case, I ran `pacman -Qo /usr/lib/thefileinquestion`, and it returned that no package owned that file. So following the wiki, I did a quick rename of that file (not delete, in case it does end up being needed!). I re-ran my update process, no conflicts, and everything appears to be working! Simple :)

In the case of Arch, when in doubt, *start* with the [Arch Wiki](https://wiki.archlinux.org/), and then ask for elaborations on those solutions if they are not working, or not solving your problem, and you likely will save yourself a lot of headache!

I even went so far for a while, as to run one of my self-hosted servers on [Vultr](https://www.vultr.com/?ref=7975115) on Arch, given they allowed custom ISOs! It is not always the best solution for a server, depending on the applications running, but its worth evaluating, and again, it's hard to fault the documentation power of the Wiki.
