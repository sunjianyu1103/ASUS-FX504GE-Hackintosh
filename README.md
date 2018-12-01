# ASUS-FX504GE-Hackintosh
Discussion, necessary configurations and instructions to get [ASUS TUF FX504GE laptop](https://www.ultrabookreview.com/19725-asus-tuf-fx504ge-review/) working with macOS Mojave 10.14.x. The following should also work with all ASUS FX504G.. laptop variants.

# Notes
1. 128 GB NVMe SSD is used for installing macOS
2. APFS partition format has to be used
3. If you are upgrading from the previous version and your partition is HFS+, better boot the installer, unmount the partition and convert it to APFS

# Pre-installation
Get yourself a Mojave USB installer with Clover installed. Important Clover settings (via Clover Configurator) are:
1. Acpi SSDT `PluginType` checked
2. Graphics: Inject Intel checked
3. Kernel Patches `Kernel LAPIC`, `KernelPM` and `AppleRTC` enabled
4. SMBIOS: MacBookPro15,2
5. UEFI Drivers: **EmuVariableUefi-64**, **ApfsDriverLoader-64**, **PartitionDxe-64**, CsmVideoDxe64, OsxAptioFixDrv-64, UsbKbDxe-64, UsbMouseDxe-64, NvmExpressDxe-64.efi, HFSPlus-64
 
Kexts installed to `/EFI/CLOVER/kexts/Other`: **FakeSMC**, **VoodooPS2Controller**

# BIOS Settings
1. Secure Boot: Disabled
2. SATA mode: AHCI
3. DVMT-Preallocated: 64MB

# Post Configuration
Any changes made to kexts are to be followed by `sudo kextcache -i /`.
Every external kext mentioned is assumed to be the latest.
## First Steps
1. Lilu and FakeSMC kexts installed to `/Library/Extensions`
2. DSDT files generated by Clover to the EFI partition
3. DSDT tables to drop: `MATS` and `DMAR`
## CPU Power Management
1. Clover ACPI `PluginType` enabled
2. Clover Kernel Patches `Kernel LAPIC`, `KernelPM` and `AppleRTC` enabled
3. ACPIBatteryManager kext installed to `/Library/Extensions`
## ALC255 Realtek Audio
Internal speaker and microphone work. For Headphone output, volume balance has to be either left or right to make the sound normal.
1. `/System/Library/Extensions/AppleGFXHDA.kext` must be removed (ID matched but not actually compatible)
2. AppleALC kext installed to `/Library/Extensions`
3. Clover Audio injection `Inject=3` (`ResetHDA` may be enabled)
## PS/2 Keyboard
1. VoodooPS2Controller kext installed to `/Library/Extensions` and `/EFI/CLOVER/kexts/Other` (using keyboard in Recovery mode)
2. Karabiner (to remap your keyboard)
## Intel UHD 630 Graphics
1. Enabled by `device-properties` injection (have Clover's Inject Intel **unchecked**, go with `0x3E9B0000`)
2. WhateverGreen kext (with CFL backlight fix) installed to `/Library/Extensions`
### Backlight Control
Somewhat works. There may be a 3-minutes lowest backlight level bug at boot.
1. AppleBacklightFixup kext installed to `/Library/Extensions`
2. `SSDT-PNLF.aml` installed to `/EFI/Clover/ACPI/patched`
3. Clover flag added for WhateverGreen: `igfxcflbklt=...` (three possible values, niether makes the fix consistent)
4. Brightness adjustment keys working by modifying `/EFI/Clover/ACPI/patched/DSDT.aml`
   ```
   Scope (_SB.PCI0.LPCB.EC0) {
        ...
        Method (_Q11, 0, NotSerialized)  // _Qxx: EC Query
        {
            Notify (PS2K, 0x0405) // Brightness down
        }
        Method (_Q12, 0, NotSerialized)  // _Qxx: EC Query
        {
            Notify (PS2K, 0x0406) // Brightness up
        }
        ...
   }
   ```
### HDMI Port
**Major drawbacks**: You have to put the laptop to sleep then wake it again to fix blank screen issue at boot. Also, while you are using your laptop, the screen may at some point go blank and the only way to fix this is to hard reboot.
1. Disable WhateverGreen's HDMI injection by adding a boot flag `-igfxnohdmi`
2. `device-properties` combination of `framebuffer-con1-type`, `framebuffer-con1-pipe` and `AAPL01,override-no-connect` based on [this post](https://www.tonymacx86.com/threads/uhd-630-no-hdmi-audio.265490/page-2#post-1858289)
## USB 2.0/3.1 Ports
1. Clover USB injection `Inject=false`
2. USBInjectAll and XHCI-300-series-injector kexts installed to `/Library/Extensions`
3. `SSDT-XHC.aml` installed to `/EFI/Clover/ACPI/patched` for better support
4. TODO: Edit `SSDT-UIAC.aml` to match available ports
## Realtek LAN
1. RTL8111 kext installed to `/System/Library/Extensions` and `/EFI/CLOVER/kexts/Other` (using internet in Recovery mode)
## SATA controller
1. SATA-300-series-unsupported kext installed to `/Library/Extensions`
## Miscellaneous
1. NoTouchID kext installed to `/Library/Extensions` (MacBookPro15,2 has Touch ID)

# Things that do not work
## NVIDIA Geforce 1050 Ti (Optimus)
Discrete graphic, we probably never see the day. For now, use `SSDT-DDGPU.aml` (in `/EFI/Clover/ACPI/patched`) to power it off.
## Intel HDMI Audio
Does not work, yet.
## Intel Wi-Fi AC 9560
Intel built-in Wi-Fi chipset, we again probably never see the day.
## I2C ELAN1200 Precision TouchPad (pci8086,a368)
VoodooI2C does not support GPIO controller (interrupt mode) on Coffee Lake machines, yet.
## Sleep and Wake
No deep sleep support.
## Intel Bluetooth
Not functional, but shown in preferences.

![Screenshot](FX504GE-SS.png?raw=true)
