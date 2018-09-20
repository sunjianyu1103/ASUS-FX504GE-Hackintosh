# ASUS-FX504GE-Hackintosh
Discussion, necessary configuration and instructions to get [this ASUS TUF FX504GE laptop](https://www.ultrabookreview.com/19725-asus-tuf-fx504ge-review/) working with macOS High Sierra 10.13.6. The following should also work with all ASUS FX504G.. laptop variants.

# Notes
* The OS partition is formatted using HFS+J rather than APFS (It might work too, though)
* macOS is installed to a 128GB SSD NVMe M.2
* Native UEFI in this laptop does not work with Clover, emulation needed

# Creating macOS Installer
You need a 16+ GB macOS installer flash drive prepared using [macOS High Sierra Patcher](http://dosdude1.com/highsierra/) (on an exising mac computer running macOS High Sierra 10.13.6) with Clover r4674+ bootloader installed to the flash drive. Important Clover settings (via Clover Configurator) are:
1. Acpi: `change HECI to IMEI`, `change GFX0 to IGPU`, `change HDAS to HDEF`
1. Boot: flags: `dart=0 slide=0 -disablegfxfirmware -no_compat_check`
1. CPU: `QPI=100`
1. Devices: Inject USB = Yes, IntelGFX FakeID = 0x59128086
1. Graphics: Inject Intel = Yes `ig-platform-id` = 0x3E9B000
1. SMBIOS: MacBookPro15,1
1. UEFI Drivers: CsmVideoDxe64, **EmuVariableUefi-64**, OsxAptioFixDrv-64, UsbKbDxe-64, UsbMouseDxe-64, HFSPlus-64, NTFS-64 (if you need it)
 
Kexts (`/EFI/CLOVER/kexts`): **FakeSMC**, Lilu, VoodooPS2Controller, WhateverGreen

# BIOS Settings
1. Intel Virtualization Technology & VT-d: Disabled
2. Fast Boot & Secure Boot: Disabled
3. SATA mode: AHCI
4. DVMT-Preallocated: 64MB

# Post Configuration
1. Clover settings
    1. Acpi: `AddPNLF` checked, Generate Options > `PluginType` enabled
    1. Boot: flags added: `-xcpm`, `HibernationFixup` enabled
    1. Devices: IntelGFX FakeID = 0x3E9B8086, `SetIntelBacklight` enabled
    1. Graphics: `ig-platform-id` = 0x3E9B0006
    1. Kernel and Kext Patches: `Kernel LAPIC`, `KernelPm`, `AppleRTC` checked
1. Kexts (`/EFI/CLOVER/kexts`) added: ACPIBatteryManager, VoodooHDA (2.9.0+)
1. Karabiner (to remap your keyboard)

# What work
1. CPU native power management (Incompleted (?) according to console logging)
1. Battery power management
1. Intel graphics (UHD 630 1536 MB)
1. Internal speaker (with volume adjustment shortcut)
1. USB ports 2.0/3.0
1. Bluetooth

# What don't work (yet)
1. Discrete graphics (NVIDIA GeForce 1050 Ti)
1. HDMI port
1. Intel Wi-FI (AC 9560)
1. Touchpad
1. Headphone jack

# Untested
1. Realtek LAN
1. Sleep/wake
