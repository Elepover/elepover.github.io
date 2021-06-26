---
title: Bypassing Android FRP and Setup Wizard with TWRP and Magisk
date: 2018-06-16 09:43:13
tags:
- twrp
- frp
- root
- recovery
- bypass
- magisk
- setupwizard
thumbnail: /pic/bypass-frp.png
---

This passage will guide you through the steps to bypass your phone's FRP.

<!-- more -->
**If you want to go directly to the solutions, click [here](#Solution).**

**This post is not for those who are unfamiliar with modding their phones.**

**The author is not responsible for ANY sort of abuse of such method.**

# Foreword

I've got a Redmi Note 5 (a.k.a.: Redmi Note 5 Pro (India), codename `whyred`) recently.

I used a special Mi Account and directly unlocked the bootloader without 360h wait time.

Flash TWRP Recovery with fastboot, reboot to recovery, dual-wipe, flash: RR -> GApps -> Magisk.

But this version or ResurrectionRemix is not perfect: USB OTG is not detecting anything, random freezing...

Soon after that, I decided to flash back to MIUI Global again.

Dual-wipe, flash, reboot, finish the setup wizard.

But one thing is beyond my expectation:

>! Not signed in
>
>To use this phone, you first have to sign in to an owner's Google Account in use on this phone when it was last reset.
>
>Because you haven't done so, you have to set up the phone again.
>
>[SET UP PHONE]

Most of the instructions about "Bypassing Google FRP" on the Internet is generally:

1. Trying to open a browser in the setup wizard.
2. Opening the Google app.
3. Entering settings.

But there's no "Vision Settings" on MIUI. So I decided to remove the setup wizard.

# Solution

How to do, then?

Following the old way into recovery, I found that the TWRP that I flashed in before, was overriden by MIUI.

## Flash Back the TWRP

Get TWRP for your device at [here](https://twrp.me/Devices/).

When you finished downloading, perform these following commands:

```
fastboot flash twrp.img
fastboot boot twrp.img
```

## Protect the TWRP

After entering the TWRP, select the `Mount` menu, and mount the `System` partition.

Back to the `Advanced` menu, select `File Manager`.

Enter this folder:

`/system/bin/`

Find the file called `install-recovery.sh`, rename it to `install-recovery.sh.bak` (of course, you can delete it safely if you like).

Now the system wouldn't be able to replace our lovely TWRP.

## Kill the Setup Wizard

After that, back to the parent folder, and go to this folder:

`/system/priv-app/SetupWizard/`

Rename `SetupWizard.apk` to `SetupWizard.apk.bak`.

**Please make sure that MagiskSU has been flashed on your device. Or the following instructions cannot be completed.**

(You can get MagiskSU flash package [here](https://forum.xda-developers.com/apps/magisk))

When you're all done, reboot to system.

## Modify Settings

We could find something different when we boot into the system: because the Setup Wizard was killed by us, you cannot slide down the status bar (AOSP), or use neither the home key nor the multitask key.

Let's solve the problem now.

We need to install a `Terminal Emulator`.

You can find the application package at [APKMirror](https://www.apkmirror.com/apk/jack-palevich/terminal-emulator/terminal-emulator-1-0-70-release/terminal-emulator-1-0-70-android-apk-download/download/).

(If the link no longer works, Google "Terminal Emulator by Jack Palevich")

Enter the `Terminal Emulator`, type in this command.

`su`

Confirm the pop-up of the Magisk Manager.

And do these following commands in order:

```
setenforce 0
settings put secure user_setup_complete 1
settings put global device_provisioned 1
setenforce 1
reboot
```

After that, your phone will reboot itself.

And it's done!

Enjoy your journey!