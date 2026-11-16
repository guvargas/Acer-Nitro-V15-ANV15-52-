I've been a part of this community since 2022 and did my first ever mackintosh on a i5-7400 and it was pretty much horrible to use. This one it has been my go to work OS (I am a dev), it works fine for a daily driver, I still have a dual boot with windows (there is no way I install a linux distro), so I can keep my gaming capabilities and still use the macOS system. 

Thanks for everyone in the community posting your SUCESS and Failures, I was waiting until someone with a similar config to show up and give me the full walkthrough, but then I realized that I could be that someone. Looking forward to updating to Golden Gate. Special thanks to NullMoth, doing god's work there keep up champ.

My config on Github [https://github.com/guvargas/Acer-Nitro-V15-ANV15-52-](https://github.com/guvargas/Acer-Nitro-V15-ANV15-52-)

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
|Intel VMD|Disabled|
|Above 4G Decoding|Enabled|
|Resizable BAR|Enabled|
|Fast Boot|Disabled / recommended|
|CSM|Not available in BIOS|

A few observations:

* Disabling VMD was important for storage compatibility

My OpenCore configuration also includes the following settings:

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

    -v debug=0x100 keepsyms=1 -amfipassbeta -allow3d -disablegfxfirmware -igfxdvmt -vi2c-force-polling ctrsmt=full revpatch=sbvmm nvfb=1 nvaccel=1 nvfbheads=4 -nvkmsnosmooth amfi_get_out_of_my_way=0x1 amfi=0x80 ipc_control_port_options=0 gfxrst=4 -no160 -mtknoamsdu -novht -noht40

WhateverXe:

    VEN_8086
    DEV_A7A8

# 4. External monitor — AOC Q27G4 over HDMI

My monitor is an AOC Q27G4 with a native resolution of 2560×1440 and goes up to 180hz via display port (not working cause needs the usb-c for that) and 144hz with the HDMI

I managed to get the monitor working at:

**2560×1440 @ 144 Hz**

I used `displayplacer` to inspect the available modes and configure the displays.

# 4.1 Disabling the laptop screen

Couldn't make the laptop screen work with the external one, mainly bc the laptop uses intel and not the RTX (that's what I suspect) It works ok without the external monitor but its laggy and not a good use overall. If the external monitor is connected it stops on a black screen with the verbose of the boot. I turned it off thinking it would impact my performance and... IT REALLY DID!!! Using display placer to turn it off my macOS feels faster now. 

I created a .command script to double click and deactivate the internal monitor just for ease of access

# 5. Camera

It was only working on safari, not on Photo Booth or Chrome or any other apps, and the apps would not ask for permission on settings. 

After troubleshooting with Codex, we identified a possible conflict between AMFIPass.kext 1.4.1 and the AMFI-related OpenCore boot arguments.

The problematic configuration included:

    amfi_get_out_of_my_way=0x1
    amfi=0x80

The proposed fix was to remove both arguments and retain:

    -amfipassbeta ipc_control_port_options=0

After updating the boot configuration and rebooting, the macOS privacy permissions could be reset using:

    tccutil reset Camera
    tccutil reset Microphone

# 6. Ethernet and Wi-Fi

# Ethernet — working

No problem, as fast as windows

# Wi-Fi — not working

The integrated wireless card is:

    MediaTek MT7921

I have not found a working macOS solution for this card.

To be fair it works for like 1 minute and then it disconnects. And when it does work it makes the computer lags for a little bit and the wifi (5ghz) isn't as fast as it should be

# 7. Audio, Bluetooth, Performance and Sleep

I really wanted to use Spatial Audio here and discovered that it only works on M1 chips :( 

The internal speakers audio didn't work at first, now it works. Sleep works. Bluetooth too. Airdrop no. Messages surprisingly yes.

Performance I suggest you to turn on Performance mode on the Acer on Windows, or atleast the Balanced mode, that way it can use more power and be faster overall. 

# 8. Things I still want to fix

My main remaining issues are:

1. **MediaTek MT7921 Wi-Fi:** Is there any working driver or experimental implementation for Tahoe, or is replacing the card the only realistic solution?

# 9. Use of AI

I asked for codex ASTRA medium for solve all problems and it took several tries, restarts and tokens until it was finally ok to use, but its a really good way of quick diagnosing, I do advise you all to use something like that to help. 
