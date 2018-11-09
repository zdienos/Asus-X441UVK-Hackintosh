# Asus X441UVK Hackintosh

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
- Rename _DSM to XDSM
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
- Rename HECI > IMEI, SAT0 > SATA, and AC0 > ADP1 (Manual rename.. Please, be carefull!!)
- And for the Brightness key replace method Q0E & Q0F with this code :

```c
Method (_Q0E, 0, NotSerialized)  
{
    If (ATKP)
    {
        \_SB.ATKD.IANE (0x20)
    }
}
Method (_Q0F, 0, NotSerialized)
{
    If (ATKP)
    {
        \_SB.ATKD.IANE (0x10)
    }
}
```

---------------

**Release** (https://github.com/alfinauzikri/ASUS-X441UVK/releases)
---------------

* 11-9-2018
  * Clover_v2.4k_r4741
  * AsusSMC v1.0.1
