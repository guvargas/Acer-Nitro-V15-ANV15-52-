I've been a part of this community since 2022 and did my first ever mackintosh on a i5-7400 and it was pretty much horrible to use. This one it has been my go to work OS (I am a dev), it works fine for a daily driver, I still have a dual boot with windows (there is no way I install a linux distro), so I can keep my gaming capabilities and still use the macOS system. 

Thanks for everyone in the community posting your SUCESS and Failures, I was waiting until someone with a similar config to show up and give me the full walkthrough, but then I realized that I could be that someone. Looking forward to updating to Golden Gate. Special thanks to NullMoth, doing god's work there keep up champ.


\--------------

# 0. Generated the initial version with nullmoth application

That's the first step fr

# 1. Hardware specifications

|Component|Specification|
|:-|:-|
|Laptop|Acer Nitro V15 ANV15-52|
|CPU|Intel Core i7-13620H|
|Dedicated GPU|NVIDIA GeForce RTX 4060 Laptop GPU|
|RAM|16 GB|
|Wi-Fi|MediaTek MT7921|
|Ethernet|Realtek RTL8168|
|External monitor|AOC Q27G4, 2560×1440|
|External display connection|HDMI (USB-C to DisplayPort not working)|
|macOS|Tahoe 26.7.3 (25G342)|

The internal display uses the Intel graphics path. 

# 2. BIOS settings

These are the main settings I used for the installation.

|Setting|Configuration|
|:-|:-|
|Secure Boot|Disabled|
|Boot mode|UEFI|
|*Intel VMD*|Disabled|
|Above 4G Decoding|Enabled|
|Resizable BAR|Enabled|
|Fast Boot|Disabled / recommended|
|CSM|Not available in BIOS|



 OpenCore configs:

    Booter -> Quirks
      ResizeAppleGpuBars = 0
    
    UEFI -> Quirks
      ResizeGpuBars = -1
    
    Misc -> Security
      SecureBootModel = Disabled
    
    Kernel -> Quirks
      AppleXcpmCfgLock = True
      ProvideCurrentCpuInfo = True
      DisableIoMapper = True
      CustomSMBIOSGuid = True
      DisableLinkeditJettison = True

# 3. boot args

    -v debug=0x100 keepsyms=1 -amfipassbeta -allow3d -disablegfxfirmware -igfxdvmt -vi2c-force-polling ctrsmt=full revpatch=sbvmm nvfb=1 nvaccel=1 nvfbheads=4 -nvkmsnosmooth amfi_get_out_of_my_way=0x1 amfi=0x80 ipc_control_port_options=0 gfxrst=4 -no160 -mtknoamsdu

My current USB EFI uses OpenCore 1.0.9. For Wi-Fi, I also set `mtk-wifi-band=5` in OpenCore NVRAM, which limits this config to 5 GHz.

WhateverXe:

    VEN_8086
    DEV_A7A8

# 4. External monitor — AOC Q27G4 over HDMI

My monitor is an AOC Q27G4 with a native resolution of 2560×1440 and goes up to 180hz via display port (not working cause needs the usb-c for that) and 144hz with the HDMI

I managed to get the monitor working at:

**2560×1440 @ 144 Hz**

I used `displayplacer` to inspect the available modes and configure the displays.

I’m now running [NullMoth’s NVIDIA driver 3.0](https://github.com/nullmoth/nvidia-macos-driver/releases/tag/v3.0) on the RTX 4060.

# 4.1 Disabling the laptop screen

Couldn't make the laptop screen work with the external one, mainly bc the laptop uses intel and not the RTX (that's what I suspect) It works ok without the external monitor but its laggy and not a good use overall. If the external monitor is connected it stops on a black screen with the verbose of the boot. I turned it off thinking it would impact my performance and... IT REALLY DID!!! Using display placer to turn it off my macOS feels faster now. 

I created a .command script to double click and deactivate the internal monitor just for ease of access

I also tried the experimental [Hybrid Laptop mode](https://github.com/nullmoth/nvidia-macos-driver/blob/v3.0/docs/HYBRID-LAPTOPS.md) in NullMoth’s 1401 app. Copy mode (`nmhybrid=1`) left both screens black. I also tried Scan out in place (`nmhybrid=2`), and the normal boot with the AOC connected went to a black screen again. I turned Hybrid off and went back to using the AOC over HDMI.

# 5. Camera

It was only working on safari, not on Photo Booth or Chrome or any other apps, and the apps would not ask for permission on settings. 

After troubleshooting with Codex, we identified a possible conflict between AMFIPass.kext 1.4.1 and the AMFI-related OpenCore boot arguments.

The problematic configuration included:

    amfi_get_out_of_my_way=0x1
    amfi=0x80

The proposed fix was to remove both arguments and retain:

    -amfipassbeta ipc_control_port_options=0

The proposed follow-up after rebooting was to reset the macOS privacy permissions using:

    tccutil reset Camera
    tccutil reset Microphone

What actually worked for me was granting camera access to the apps and reopening them. The two AMFI arguments above are still in my current EFI.

# 6. Ethernet and Wi-Fi

# Ethernet — working

No problem, as fast as windows

# Wi-Fi — working

The integrated wireless card is:

    MediaTek MT7921

It used to disconnect after about a minute. After updating to [AirportMtkWlan 2.1.6 for Tahoe](https://github.com/Arxusion/AirportMtkWlan/releases/tag/v2.1.6) and setting the EFI to 5 GHz, Wi-Fi is now working properly on the original card.

# 7. Audio, Bluetooth, Performance and Sleep

I really wanted to use Spatial Audio here and discovered that it only works on M1 chips :( 

The internal speakers audio didn't work at first, now it works. Sleep works. Bluetooth is now working properly on the same MediaTek card with [SimpleMtkBtFw and SimpleMtkBtPatcher 1.1.0](https://github.com/flxxxxddd/SimpleMtkBtFw) and BlueToolFixup 2.7.2. Airdrop no. Messages surprisingly yes.

Performance I suggest you to turn on Performance mode on the Acer on Windows, or atleast the Balanced mode, that way it can use more power and be faster overall. 

# 8. Things I still want to fix

My main remaining issues are:

1. **Internal display:** I still want to get the laptop screen working smoothly alongside the external monitor.
2. **Microphones:** The internal mic and the mic on a wired headset still don’t show up as inputs. The headphone output works.
3. **AirDrop:** Still not working. I installed LocalSend as an alternative for local file transfers.

# 9. Use of AI

I asked for codex ASTRA medium for solve all problems and it took several tries, restarts and tokens until it was finally ok to use, but its a really good way of quick diagnosing, I do advise you all to use something like that to help. 

# 10. A review of MY HACKINTOSH

Ok so ive spent 1 full week of debugging and trying until i get everything in a ok state for me to use as a daily driver, how is it now? Well, its laggy, when i need to do heavy tasks i much prefer to use windows for programming. But when i want to browse the web and just do some research i like the macOS interface better. I am still finding what i prefer to use in each platform, and it got me really interested on the Macbook Neo, as my laptop battery performance and weight are something relevant for me when i travel. So far its just like any other hobby, I now have to think which system should i use for determined task, how do i do that on some system, what can i improve in my workflow and so on. I really wanted to people on r/hackintosh to say more about their personal experiences, bc at the end of the day most of us are doing this for the fun of doing it and learning something in the way