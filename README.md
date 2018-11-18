# Asus X441UVK Hackintosh

Hackintosh resources for Asus X441UVK, maybe support for Intel Core i5-7200U or i7-7500U (Kabylake)

-------
**Specification :**

 - Processor : Intel Core i3 7100U 2.4Ghz (Kabylake)
 - IGPU : Intel HD Graphics 620
 - RAM : 4GB DDR4 2133MHz 
 - Built-in Audio : ALC255 Revision 2
 - Wifi : Qualcomm Atheros AR9565
 - Ethernet : Realtek RTL8106E
 - Trackpad : ELAN1200
 - Bootloader: Clover (UEFI)
 - BIOS Version : X441UVK.320
 

**Working :**

 - Full QE/CI + Native Power Management
 - Intel HD Graphics 620
 - Ethernet
 - USB ports 3.0/2.0
 - Battery status
 - Keyboard + FN button keys
 - TrackPad + Multitouch Support
 - Audio
 - Wifi AR9565
 - Brightness
 - Backlight Control
 - HDMI Ports
 - Shutdown / Reboot
 - Sleep
 - iMessage
 - FaceTime
 - WebCam
 

----------

**Setup guide**
-----------

**BIOS Configuration**

Bios Config | Setting 
:---:| :---:
Security -> Secure Boot | Disabled
Intel Virtualization    | Disabled
VT-d | Disabled
Intel AES | Disabled
Graphics Configuration -> DVMT Pre-Allocation | 64M
USB Configuration -> XHCI Pre-Boot Mode | Smart Auto / Enabled
SATA Mode | AHCI


**REFERENCE**
--------------

| Features  |   Fixes      |
| :------------: | :------------: |
|     Intel HD Graphics 620 QE/CI           |   Inject Intel  ig-platform-id **0x591b0000**   & SMBIOS MBP14.1 + WhateverGreen.kext       |   
| Audio |        AppleALC.kext & Lilu.kext + LayoutID 32 + FixALC255v2.app        |  
| Wifi AR9565 | AirPortAtheros40.kext
| Brightness |  AppleBacklightFixup.kext + SSDT-PNLF.aml
| FN + Brightness Control Key | DSDT Patch + AsusSMC.kext | 
| Touchpad & Keyboard  | VoodooI2C.kext and VoodooI2CHID.kext + VoodooPS2Controller.kext
| Battery Status | VirtualSMC.kext (SMCBatteryManager.kext) & patch DSDT

----------------

**DSDT Patches**
---------------

Patches are from Rehabman Laptop Patches repo Source :  http://raw.github.com/RehabMan/Laptop-DSDT-Patch/master

- Fix PARSEOP_ZERO Error (aggresive)
- Asus N55SL/VivoBook
- ADBG Error (Only use it if u get ADBG Error)
- AC Adapter Fix
- Add MCHC
- Fix Mutex
- Shutdown Fix v2
- fix _WAK  Arg0 v2
- fix HPET
- SMBUS fix
- IRQ fix
- RTC fix
- OS Check Fix (Windows 10)
- Rename GFX0 to IGPU
- GPIO Controller Enable [SKL+]
- Insert DTGP
- Rename HDAS > HDEF, HECI > IMEI, SAT0 > SATA, and AC0 > ADP1 (Manual rename.. Please, be carefull!!)
- And for the Brightness key and FN keys :

```c
into_all device label ALS0 remove_entry;
into scope label _SB insert begin
Device(ALS0)\n
{\n
    Name(_HID, "ACPI0008")\n
    Name(_CID, "smc-als")\n
    Name(_ALI, 150)\n
    Name(_ALR, Package()\n
    {\n
        Package() { 100, 150 },\n
    })\n
}
end;
into method label ALSS parent_label ATKD remove_entry;
into method label ALSC parent_label ATKD remove_entry;
into device label ATKD insert begin
Method (ALSS, 0, NotSerialized)\n
{\n
    Return (^^ALS0._ALI)\n
}
end;
into device label ATKD insert begin
Method (ALSC, 1, NotSerialized)\n
{\n
    // This method does nothing\n
}
end;

# Replacing method _Q0A for F1 key to work
into Method label _Q0A replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x5E)\n
    }
end;

# Replacing method _Q0B for F2 key to work
into Method label _Q0B replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x7D)\n
    }
end;

# Replacing method _Q0E for F5 key to work
into Method label _Q0E replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x20)\n
    }
end;

# Replacing method _Q0F for F6 key to work
into Method label _Q0F replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x10)\n
    }
end;

# Replacing method _Q10 for F7 key to work
into Method label _Q10 replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x35)\n
    }
end;

# Replacing method _Q11 for F8 key to work
into Method label _Q11 replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x61)\n
    }
end;

# Replacing method _Q12 for F9 key to work
into Method label _Q12 replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x6B)\n
    }
end;

# Replacing method _Q13 for F10 key to work
into Method label _Q13 replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x32)\n
    }
end;

# Replacing method _Q14 for F11 key to work
into Method label _Q14 replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x31)\n
    }
end;

# Replacing method _Q15 for F12 key to work
into Method label _Q15 replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x30)\n
    }
end;

# Replacing method _Q76 for ALS toggle A key to work
into Method label _Q76 replace_content begin 
    If (ATKP)\n
    {\n
        \_SB.ATKD.IANE (0x7A)\n
    }
end;
```

---------------

**How to install kext and Fix internal mic problem**
---------------

1. Put all kexts files on EFI/CLOVER/Kexts/Other

- Put edited AirPortAtheros40.kext to IO80211Family.kext (IO80211Family.kext/Contents/Plugins) and install using Kext       Utility.app (Replace vanilla AirPortAtheros40.kext).
- Run script install_daemon.sh from AsusSMC bundle as root (to fix Sleep and Wifi On/Off Button)

2. Fix Audio ALC255 for Asus X441U Series (Internal mic)

- Put FixALC255v2.app to Application/Utilities folder.
- Open System Preferences > Users & Groups > Login Items and Add FixALC255v2.app and check "hide"
- Reboot

***UPDATE***

- For ALC255 use this patch inside HDEF

```c
    Scope (_SB.PCI0)
    {
        Device (HDEF)
        {
            //
            //INSERT HERE
            //
            
            Name (_ADR, 0x001F0003)  // _ADR: Address
            OperationRegion (HDAR, PCI_Config, Zero, 0x0100)
          .
          .
          .
```

Patch for ALC255 : 
```c
Name (RMCF, Package()
    {
                "CodecCommander", 
                Package (0x0A)
                {
                    "Custom Commands", 
                    Package (0x04)
                    {
                        Package (0x00){}, 
                        Package (0x08)
                        {
                            "Command", 
                            Buffer (0x04)
                            {
                                 0x01, 0x97, 0x07, 0x24                           // ...$
                            }, 

                            "On Init", 
                            ">y", 
                            "On Sleep", 
                            ">n", 
                            "On Wake", 
                            ">y"
                        }, 

                        Package (0x08)
                        {
                            "Command", 
                            Buffer (0x04)
                            {
                                 0x01, 0xA7, 0x07, 0x24                           // ...$
                            }, 

                            "On Init", 
                            ">y", 
                            "On Sleep", 
                            ">n", 
                            "On Wake", 
                            ">y"
                        }, 

                        Package (0x08)
                        {
                            "Command", 
                            Buffer (0x04)
                            {
                                 0x02, 0x17, 0x08, 0x83                           // ....
                            }, 

                            "On Init", 
                            ">y", 
                            "On Sleep", 
                            ">n", 
                            "On Wake", 
                            ">y"
                        }
                    }, 

                    "Perform Reset", 
                    ">n", 
                    "Perform Reset on External Wake", 
                    ">n", 
                    "Send Delay", 
                    0x0A, 
                    "Sleep Nodes", 
                    ">n"
                }
            })
```

**Contact Us**

Facebook : fb.me/alfinauzikri <br />
Telegram : https://t.me/HackintoshIndonesia
