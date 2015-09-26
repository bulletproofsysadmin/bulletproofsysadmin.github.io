---
author: bulletproofsysadmin
type: image
featimg: 3.jpg
title: Winning in 8 Steps
tags: [sysadmin, linux, rhcsa, rhel7, cent7]
category: [rhcsa7]
---

Root password recovery is one of the skills that should be in the tool belt of any Linux Systems Administrator out there.  This and setting interfaces addresses are probably among the top two skills that it is simply unacceptable to not know.  In the most recent release of RedHat Enterprise Linux and CentOS version 7, RHEL7 and Cent7 respectively this procedure has changed slightly.  There seems to be a lot of **misinformation** on how to complete this once basic task.

<br>

Even RedHat's own article is a much greater number of steps that has the admin using features like sysroot jails and SELinux labels to accomplish the task at hand. RedHat's recommended instructions can be found here:

<br>

[RedHat Systems Administration Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Terminal_Menu_Editing_During_Boot.html#proc-Resetting_the_Root_Password_Using_rd.break)

<br>

Next I'll show you _my_ Bullet Proof way to recover and reset root's password the easy way.
<br>
Step 1.
<br>
Reboot the box using the `/sbin/reboot` command or `init 6`.
<br>
In the grub menu press the tab key to interrupt boot and the `e` key to directly edit the grub stanza.  Pay close attention and search for the line that contains linux or linux16.
<br>

Step 2.
<br>
Scroll until you find the part of the stanza with `rhgb quiet`.  
<br>
Replace that part of the stanza with `init=/bin/bash`.

Press the `ctrl + x` key to continue booting.
<br>

Step 3.
<br>
Now you should have a prompt that looks like this:
<br>

    #bash-4.2
    
<br>
Type mount to see that /root is mount "read-only".
<br>
Step 4.
Perform a remount of the root partition by typing:
<br>

    mount -o remount,rw /

<br>
This will remount the root partition read/write.
<br>
You can validate by using the mount command to double-check.
<br>
Step 5.
<br>
Set root's password using passwd .
<br>

    passwd

<br>
Type the "new" password twice.
<br>
Step 6.
<br>
**This is very important**
<br>
If this system is running SELinux you will need to trigger a relabel.
<br>

    touch / .autorelabel

<br>
Step 7.
<br>
Trigger systemd to boot the rest of the way and relabel.
<br>

    exec /sbin/init

<br>
>Note that this may take a while depending on the size of disk and number of files.
<br>
Step 8.
<br>
Sign in with your new root user and WIN!
<br>
Please send feedback or questions to the@bulletproofsysadmin.com or tweet @bltpr00fadm1n.
<br>
Thanks for following along.



---
