# ASUS-FX504GE-Hackintosh
Discussion, necessary configuration and instructions to get [this ASUS TUF FX504GE laptop](https://www.ultrabookreview.com/19725-asus-tuf-fx504ge-review/) working with macOS High Sierra 10.13.6. The following should also work with all ASUS FX504G.. laptop variants.

# Notes
* The OS partition is formatted using HFS+J rather than APFS (It might work too, though)
* macOS is installed to a 128GB SSD NVMe M.2
* Native UEFI in this laptop does not work with Clover, emulation needed
* You need an extra official kext just for Intel graphics to work

# Major Issue
Often, after the boot process finished, the screen will seem to be black, but actually it's that the brightness goes to the lowest and it is impossible to adjust the brightness by any means. However, the screen will eventually turn to the normal brightness after several minutes have passed.

# Creating macOS Installer
You need a 16+ GB macOS installer flash drive prepared using [macOS High Sierra Patcher](http://dosdude1.com/highsierra/) (on an exising mac computer running macOS High Sierra 10.13.6) with Clover r4674+ bootloader installed to the flash drive. Important Clover settings (via Clover Configurator) are:
1. Boot: flags: `dart=0 slide=0 -no_compat_check`
2. CPU: `QPI=100`
3. Graphics: Inject device properties for Intel
4. SMBIOS: MacBookPro15,2
5. UEFI Drivers: CsmVideoDxe64, **EmuVariableUefi-64**, OsxAptioFixDrv-64, UsbKbDxe-64, UsbMouseDxe-64, HFSPlus-64, NTFS-64 (if you need it)
 
Kexts (`/EFI/CLOVER/kexts`): **FakeSMC**, Lilu, VoodooPS2Controller, WhateverGreen

# BIOS Settings
1. Intel Virtualization Technology & VT-d: Disabled
2. Fast Boot & Secure Boot: Disabled
3. SATA mode: AHCI
4. DVMT-Preallocated: 64MB

# Post Configuration
1. Clover settings
    1. Acpi: Generate Options > `PluginType` enabled
    2. Boot: flags added: `-xcpm`, `HibernationFixup` enabled
    3. Kernel and Kext Patches: `Kernel LAPIC`, `KernelPm`, `AppleRTC` checked
    4. For more settings, see the provided `config.plist`
2. Kexts (`/EFI/CLOVER/kexts`) added: ACPIBatteryManager, VoodooHDA (2.9.0+), VoodooPS2Controller, WhateverGreen, Lilu, NoTouchID, FakeSMC, (XHCI-300-series-injector)
3. Karabiner (to remap your keyboard)
4. [AppleIntelCFLGraphicsFramebuffer.kext](https://www.tonymacx86.com/attachments/appleintelcflgraphicsframebuffer-kext-zip.341983/) installed to `/System/Library/Extensions` for Intel UHD 630 to work natively (See [here](https://www.tonymacx86.com/threads/guide-native-intel-uhd630-graphics-support-in-macos-10-13-6.256426/) for more information)
5. Brightness control via `SSDT-PNLF` and AppleBacklight kext patch
6. USB connection via `SSDT-UIAC-ALL` and USBInjectAll kext installed `/Library/Extensions`
7. LAN connection via RTL8111 kext

# What work
1. CPU native power management (Incompleted (?) according to console logging)
2. Battery power management
3. Intel graphics (UHD 630 1536 MB)
4. Internal speaker (with volume adjustment shortcut)
5. USB ports 2.0/3.0
6. LAN
7. Brightness control

# What somewhat work
1. Headphone jack (balance needs to be either left or right to make sound works properly)

# What don't work (yet)
1. Discrete graphics (NVIDIA GeForce 1050 Ti)
2. HDMI port
3. Intel Wi-FI (AC 9560)
4. I2C touchpad
5. Sleep/wake

![Screenshot](FX504GE-SS.png?raw=true)
