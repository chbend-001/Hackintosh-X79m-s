# Hackintosh-X79m-s
EFI for motherboard X79m-s ( v1,0)
OpenCore-South China x79_e5 2670 v1 c2 v2 rx588
image
PROJECT FORK https://gitee.com/yaming-network/clover-x79-e5-2670-rx588

Introduction
The project is maintained by Yaming Network Studio, Emeishan City, and is mainly adapted to the South China x79 motherboard to install and adapt to Apple's Mac OS
The current warehouse code supports the OS version: 10.14.x-11.5beta1
Old version supports 10.9--10.12.6
10.13.x has been arranged for adaptation due to large differences, but the progress is slow
Software and hardware
The specific motherboard model: South China X79 motherboard printed with South China Gold ver2.4.x blue four memory slot version motherboard BIOS V2.47
At present, the relevant South China x79 motherboards that have been tested can basically directly install individual dual-channel versions. Because there is no such board type, I don’t know the specific situation.
There are many x79 versions, please choose carefully
Precautions
Thank you for your attention to the new penguin exchange group to deal with the problem of cpu frequency conversion for you
Thousand people number 591391580
Add a description for the support of the two available smbios models
Mac pro 6,1 This model supports a minimum system of 10.9.0
i Mac pro1,1 This model supports the minimum system version: 10.13.2, but the current boot does not support the 10.13.x series and can only be installed with 10.14.x
If the system to be installed is lower than the minimum system version of the supported model, a prohibition symbol will appear
Installation tutorial
Before starting the installation
Pay attention to bios settings
Disable CSM
When you choose to erase the disk at installation time, please erase it directly to apfs format
The original BIOS is already supported in version 0.6.7 and can be installed directly without any settings image
Folder structure description
category	description
clover-x79	Last maintained archive
OpenCore	New major maintenance
tools	Tool maintenance
docs	Storage path of future documentation
patch	Patch configuration separate directory
Make and install U disk under mac
system version	Command used	Remarks
macOS BigSur	sudo /Applications/Install\ macOS\ Big\ Sur.app/Contents/Resources/createinstallmedia --volume /Volumes/usbmac	Where usbmac is your own U disk name
macOS Catalina	sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/usbmac	
macOS Mojave	sudo /Applications/Install\ macOS\ Mojave.app/Contents/Resources/createinstallmedia --volume /Volumes/usbmac	
Create and install u disk under win
Historical version complete dmg mirror download
mkdir ~/macOS-installer && cd ~/macOS-installer && curl -O https://gitee.com/yaming-network/macadmin-scripts/raw/main/installinstallmacos.py && sudo python installinstallmacos.py
Maintenance plan
Four Leaf Clover is no longer maintained because the driver is no longer undergoing compatibility testing.
Open core will push a new version within one week after each stable version is released
Open core configures the release version download address by itself
Configuration editor address
GenSMBIOS is an essential tool for generating three codes git clone https://gitee.com/yaming-network/GenSMBIOS cd GenSMBIOS chmod +x GenSMBIOS.command
ProperTreegit clone https://gitee.com/yaming-network/ProperTree python ./ProperTree/ProperTree.command
The use of the configurator requires the installation of the python environment
Fix the problem that the configurator crashes on MacOS Big Sur macOS 11 Download the latest version and install it and use the buildapp-python3.commandgenerated app in the script directory to use it normally
Win requires at least version win10 1703 and above to install py and execute it ProperTree.batto run the editor
Do not use the occ configurator to edit the configuration file, because if the version is wrong, the configuration file will be damaged and cause various abnormalities
Click to view the release notes log
Corresponding ssdt description in ACPI
name	effect	Do you have to
SSDT-UNC.aml	All X99 and many X79 boards need this SSDT, which specifically disables unused devices in ACPI, and then IOPCIFamily will not panic the kernel. This requires very little configuration for the end user.	Yes
SSDT-SBUS-MCHC	This part involves repairing the support for AppleSMBus in macOS. What is AppleSMBus? Then this mainly deals with the system management bus, which has many functions to verify whether it is working properly.	no
SSDT-PMC.aml	All "real" 300 series motherboards (excluding Z370), it especially brings back NVRAM support, and requires very little configuration for the end user	no
SSDT-HPET.aml	Fancy hot patches from clover, such as FixIPIC, FixTMR, FixRTC, FixHPET, etc., when we complete the conversion, the ssdt is no longer needed	no
SSDT-PLUG.aml	The purpose of SSDT-PLUG is to allow the kernel's XCPM (XNU's CPU power management) to manage our CPU power management. Although it is not necessary, it may need to exist.	no
SSDT-EC.aml	The purpose of SSDT-EC/USBX is several things: On desktops, EC (or more widely known as embedded controllers) is not compatible with the AppleACPIEC driver. To bypass this, we disable this device AppleBusPowerController when running macOS We will be looking for a device called EC, so we want to create a fake device for this kext to load into AppleBusPowerController. It also needs a USBX device to provide USB power properties for Skylake and newer models, so we will bundle this device with the EC repair program. Together on laptops, EC is used for hotkeys and battery, so disabling this is not ideal. The problem is that our EC names are not compatible, so we will create a simple "fake" EC device to satisfy Apple	Yes
SSDT-USB-Reset.aml	USB port fixed	Yes
SSDT-USBX.aml	USB power device power supply repair, we need the ssdt to repair the USB device power supply problem in systems above 10.15, because macOS no longer provides power management in the driver	no
SSDT-CPUM	When the cpu frequency conversion correction is installed for the purpose, we can not have	no
SSDT-SSDT-IMEI.aml	Shield a non-standard PCI device, but it has not been shielded for the time being. After waiting for the correction, the parameter npci=0x2000 will no longer be needed.｜ No	
SSDT-NVMe.aml	Fix the default nvme disk display external problem, we don’t need it during installation｜ No	
Instruction when verifying whether SSDT-SBUS-MCHC is working normallykextstat | grep -E "AppleSMBusController|AppleSMBusPCI"
The ssdt in the ACPI folder can only be used directly unless the board type is exactly the same to avoid unnecessary abnormal problems.
Try to generate the same ssdt by yourself
The generation tool uses SSDTTime
Use py installation environment generator operating at its own motherboard specific win git clone https://gitee.com/yaming-network/SSDTTime.gitalternative to the inside efi
Wi-Fi network card original disassembly support system description list
system version	Support chip	Highest support
Big Sur(11)+｜ BCM943602, BCM94360, BCM94352, DW1560, BCM94350, DW1820A ｜ Compatible with all systems		
CPU frequency conversion repair
Start repair:
Use ssdtPRGen.sh to generate a dedicated cpu frequency conversion file under Mac
Before using, please open the terminal and install the xcode-select --installcommand line developer tools
Execute the following command
curl -o ~/ssdtPRGen.sh https://gitee.com/yaming-network/ssdtPRGen.sh/raw/Beta/ssdtPRGen.sh
wc -c ssdtPRGen.sh
chmod +x ~/ssdtPRGen.sh
Run multiple times

sudo ./ssdtPRGen.sh
The generated SSDT-CPUM.aml is in the ~/Desktop/CPUssdt directory

Put it in the corresponding directory of oc to replace the default
Add the patch in /OpenCore/patch/oc_cpu_patch.plist to the corresponding oc configuration file acpi and the repair will be completed after restart. Some unknown reasons do not take effect, please refer to the oc related documents
Note that it is not possible to patch first, unless it is an existing CPU ssdt that can be patched first
Define the name of the CPU frequency conversion ssdt in the 0.7.0 release version as SSDT-CPUM.aml
For the v2 version of the cpu, the graphical boot interface will be turned on by default
For the alc sound card id, our Mac terminal comes with a hexadecimal conversion command. printf '%x\n' 11This means that it will convert 11 to hexadecimal and return to display b, so fill in0b000000
Fusion Drive (fusion hard drive technology)
Create method
List all disks:
diskutil list

Create a fusion drive:
diskutil cs create "Cheney Fusion Drive" disk0 disk1

After the establishment is complete, it will tell you a uuid, copy it down.

Partition this fusion drive:
diskutil cs createVolume BDF819F4-06C0-4D49-943A-1A23E8B20928 jhfs+ "Macintosh FD" 100%

At this point, you can find that all the disks have become one disk, and then use the disk tool to wipe the disk and install it normally!

Note that to use this technology, you must perform this operation during installation. It cannot be split after the operation. If splitting will lose data, please choose whether to use it yourself!

Corresponding to the address of the self-maintenance model
GIGABYTE_b75m_d3v+e3_1230_v2
South China x79_e5_2670_c2_v1
Github synchronization address, South China x79_e5_2670c2
Thanks for your support:
name	date	Amount	channel
dusk	2021.3.23	9.9 yuan	WeChat Red Packet
Pteromyini	2021.3.29	9.9 yuan	QQ red envelope
zhenli	2021.3.31	9.9 yuan	QQ red envelope
Eawen	2021.3.31	9.9 yuan	WeChat Red Packet
Pteromyini	2021.4.02	5.99 yuan	QQ red envelope
Leoist	2021.4.05	10 yuan	QQ red envelope
Leoist	2021.4.14	20 yuan	QQ red envelope
click	2021.4.20	20 yuan	QQ red envelope
zhenli	2021.5.16	18.8 yuan	QQ red envelope
