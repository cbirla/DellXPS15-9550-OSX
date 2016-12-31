Before we start: this installation is based on the chinese tutorial of darkhandz. It includes real time DSDT/SSDT patching from within clover. This is pretty easy to install. But it is NOT suited for people with no or only few knowledge in Hackintosh Systems. If you only know how to copy commands in your shell and you dont know what they're doing, then stop the tutorial and revert to windows or buy a real mac. Even if you get it running: this system is not failsafe and will be broken multiple times in its usage time, where you have to fix it without a tutorial. English is not my mother-tongue and i'm writing this without proof reading, so please forgive my bad spelling   If you've questions: please read the whole thread (doesn't matter how long it is) before asking to prevent multiple questions. Additionally do a search in google and this forum.   

## Credits:
Based on the Tutorial and files of darkhandz: https://github.com/darkhandz/XPS15-9550-Sierra  
Mixed with much knowledge of the tutorial by @Gymnae: https://www.tonymacx86.com/threads/guide-dell-xps-15-9550-skylake-gtx960m-ssd-via-clover-uefi.192598  
Using many kexts and solutions from @RehabMan  
## What's not working:
• Hibernation (works somehow, but high chance to destroy your whole data)  
• USB-C hotplug  
• SD-Card reader  
• Hynix SSD or Killer 1535 Wifi (rarely used, need replace)  
• nVidia Graphics card (Intel works)  
• FileVault 2 (full HDD encryption)  
## Requirements:
• one working MAC OS X Enviroment (aka a real mac or at least a VMWare Container running OSX)  
• 16GB USB Stick (larger is sometimes not bootable and/or requires advanced partitioning)  
• MacOS Sierra 10.12.2 installation file from the app store (redownload, just in case)  
• Knowledge in PLIST editing  
• USB Harddrive for backup - you'll loose all data on your computer!   
## Step 1: Prepare Installation
Use the existing Mac to download the Sierra installer from the App Store and create a bootable USB stick with CLOVER. You can do this with the App "Pandora's Box" of insanelymac (use google for download link), which is pretty easy to use.  After you've finished you need to download the Dell XPS 15 specific configurations for clover.  
Link: https://github.com/wmchris/DellXPS15-9550-OSX/archive/master.zip / this repo. Unzip this file. You only need the folder 10.12, you can delete the 10.11. I'll refer to this folder by "git/"  
Now mount the hidden EFI partition of the USB Stick by entering
`diskutil mount EFI` 
Inside the terminal. Mac OS will automaticly mount the EFI partition of the USB stick, but just in case: make sure it really is  
  
Overwrite everything in the CLOVER folder of the partition EFI with the content of git/10.12/CLOVER.  
If your PC has a Core i5 processor, you'll have to modify your config.plist in EFI/EFI/CLOVER/: search for the Key ig-platform-id: 0x191b0000 and replace it with 0x19160000.  
  
Go into the EFI Configuration (BIOS) of your Dell XPS 15:  
```
gymnae said: 
In order to boot the Clover from the USB, you should visit your BIOS settings:  
- "VT-d" (virtualization for directed i/o) should be disabled if possible (the config.plist includes dart=0 in case you can't do this)  
- "DEP" (data execution prevention) should be enabled for OS X  
- "secure boot " should be disabled  
- "legacy boot" optional  
- "CSM" (compatibility support module) enabled or disabled (varies)  
- "boot from USB" or "boot from external" enabled`  
  
Note: If you get a "garbled" screen when booting the installer in UEFI mode, enable legacy boot and/or CSM in BIOS (but still boot UEFI). Enabling legacy boot/CSM generally tends to clear that problem.  
In my case I left VT-d and Fastboot as they were. Also, update your 9550 to the latest BIOS.  
Don't forget to set mode to "AHCI" in the sub-menu "SATA Operation" of "System Configuration". It's mandatory.
```

Insert the stick on the Dell XPS 15 and boot it up holding the F12 key to get in the boot-menu and start by selecting your USB-Stick (if you've done it correctly it's named "Clover: Install macOS Sierra", otherwise it's just the brandname of your USB-Drive). You should get to the MacOS Installation like on a real mac. If you're asked to log-in with your apple-id: select not now! Reason: see Step 5.
## Step 2: Partition and Installation
INFORMATION: after this step your computer will loose ALL data! So if you haven't created a backup, yet: QUIT NOW!  
  
Dont install macOS yet. Select the Diskutil and delete the old partitions. Create a new HFS partition and name it "OSX". If you want to multiboot with Windows 10, then you'll have to create a second partition, too (also HFS! Dont use FAT or it will not boot! You have to reformat it when installing Windows). Make sure to select GUID as partition sheme. Close the Diskutil and install OSX normally. You'll have to reboot multiple times, make sure to always boot using the attached USB stick. So dont forget to press F12. After the first reboot you should see a new boot option inside clover, which is highlighted by default. Just press enter. If you only see one, then something went wrong.  

## Step 3: Make it bootable
After a few reboots you should be inside your new macOS enviroment. You can always boot into it using the USB stick. Remove the USB drive after successful bootup. Enter 
`diskutil mount EFI`
in your terminal, which should mount the EFI partition of your local installation.  
install git/10.12/Post-Install/Tools/Clover_v2.3k_r3961.pkg. Make sure to select "Install Clover in ESP". Also select to install the RC-Scripts. This should install the Clover Boot System. Now copy everything from git/10.12/CLOVER to EFI/CLOVER like you did before by creating the usb stick. (if you had to modify the config.plist in step 1, do it here, too). Your system should be bootable by itself. Reboot and check if your system can boot by itself.  

## Step 4: Post Installation
Because all DSDT/SSDT changes are already in the config.plist, you dont need to recompile your DSDT (albeit i suggest doing it anyway to make your system a lil bit more failsafe, see gymnaes El-Capitan tutorial for more informations). So we can skip this part and go directly to the installation of the required kexts. Open a terminal and goto the GIT folder.
```
sudo cp -r ./Post-Install/LE-Kexts/* /Library/Extensions/  
sudo mv /System/Library/Extensions/AppleACPIPS2Nub.kext /System/Library/Extensions/AppleACPIPS2Nub.bak  
sudo mv /System/Library/Extensions/ApplePS2Controller.kext /System/Library/Extensions/ApplePS2Controller.bak  
sudo ./AD-Kexts/VoodooPS2Daemon/_install.command
``` 
Now you'll have to replace the config.plist. Because you'll install modified kexts you'll HAVE TO replace the config.plist in your installation. Otherwise your PC will not boot anymore.
`diskutil mount EFI`
replace EFI/CLOVER/config.plist with git/Post-Install/CLOVER/config.plist. Again: if your PC has a Core i5 processor, search for the Key ig-platform-id: 0x191b0000 and replace it with 0x19160000.  
If you've a NVM SSD Drive, you need to install NVMe-Hackr with SSDT Spoofing (enables easier system upgrading from appstore). Dont do this if you use the HDD version of the Dell or you use your M.2 port for something different than a SSD (for ex. a UMTS modem)
```
sudo cp ./Post-Install/AD-Kexts/HackrNVMe/SSDT-Hackr.aml /EFI/EFI/CLOVER/ACPI/patched/  
sudo cp -r ./Post-Install/AD-Kexts/HackrNVMe/HackrNVMeFamilySpoof-10_12_2.kext /Library/Extensions/
```
OPTIONAL (in case you've audio problems):  
AppleHDA has some problems after Wake-Up. You'll have to plug in a headphone to get your speakers working again. You can use VoodooHDA instead, which breaks the headphone jack most of the time, but makes the rest much more stable.
```
sudo rm -r /Library/Extensions/CodecCommander.kext  
sudo rm /EFI/EFI/CLOVER/ACPI/patched/SSDT-ALC298.aml
```
then remove from your config.plist from the key "KextsToPatch" the elements "AppleHDA#1" to "AppleHDA#7". Install the package: git/Post-Install/AD-Kexts/VoodooHDA-2.8.8.pkg  
i also suggest moving some of the kext from EFI/CLOVER/kexts/10.12 to /Library/Extensions. It's just more stable.  
Finalize the kext-copy by recreating the kernel cache:
```
sudo rm -rf /System/Library/Caches/com.apple.kext.caches/Startup/kernelcache  
sudo rm -rf /System/Library/PrelinkedKernels/prelinkedkernel  
sudo touch /System/Library/Extensions && sudo kextcache -u /
```
sometimes you'll have to redo the last command if your system shows "Lock acquired".  
OSX 10.12.2 removed the posibility to load unsigned code. You can enable this by entering 
`sudo spctl --master-disable `
If you're using the 4K monitor, you'll have to apply a small patch:
```
sudo perl -i.bak -pe 's|\xB8\x01\x00\x00\x00\xF6\xC1\x01\x0F\x85|\x33\xC0\x90\x90\x90\x90\x90\x90\x90\xE9|sg' /System/Library/Frameworks/CoreDisplay.framework/Versions/Current/CoreDisplay  
sudo codesign -f -s - /System/Library/Frameworks/CoreDisplay.framework/Versions/Current/CoreDisplay
```
To prevent getting in hibernation (which can and will corrupt your data).
`sudo pmset -a hibernatemode 0`
## Step 5: iServices (AppStore, iMessages etc.)
WARNING! DONT USE YOUR MAIN APPLE ACCOUNT FOR TESTING! It's pretty common that apple BANS your apple-id from iMessage and other services if you've logged in on not well configured hackintoshs!  
If you want to use the iServices, you'll have to do some advanced steps, which are not completly explained in this tutorial. First you need to switch the faked network device already created by step 4 to be on en0. Goto your network settings and remove every network interface.
`sudo rm /Library/Preferences/SystemConfiguration/NetworkInterfaces.plist`
Reboot, go back in the network configuration and add the network interfaces (LAN) before Wifi.  
You also need to modify your SMBIOS in the config.plist of Clover in your EFI partition with valid informations about your "fake" mac. There are multiple tutorials which explain how to do it like "http://www.fitzweekly.com/2016/02/hackintosh-imessage-tutorial.html". Dont forget we have the "Dell Truncation Problem". There is a workaround available inside the FakeSMBIOS.kext. If you edit your Model to something longer than 11 chars (for ex. MacBookPro13,3), you'll have to edit the info.plist in this kext, too. If you use something lower (like iMac17,1), you can delete the FakeSMBIOS.kext.  
It's possible you have to call the apple hotline to get your fake serial whitelisted by telling a good story why apple forgot to add your serial number in their system. (aka: dont do it if you dont own a real mac). I personally suggest using real data from an old (broken) macbook.
## Step 6: Upgrading to macOS 10.12.3 or higher / installing security updates
Each upgrade will possibly break your system!  
(Update: most has been removed, because it's now more update proof)  
If you're using the 4K display,After update boot your machine with the installation stick and boot into your installation (do not select install). You can then redo the patch and reboot normally:
```
sudo perl -i.bak -pe 's|\xB8\x01\x00\x00\x00\xF6\xC1\x01\x0F\x85|\x33\xC0\x90\x90\x90\x90\x90\x90\x90\xE9|sg' /System/Library/Frameworks/CoreDisplay.framework/Versions/Current/CoreDisplay  
sudo codesign -f -s - /System/Library/Frameworks/CoreDisplay.framework/Versions/Current/CoreDisplay
```
## Afterword and fixes
as i said before: this is not a tutorial for absolute beginners, albeit it's much easier then most other tutorials, because most is preconfigured in the supplied config.plist. Some Dells have components included, which are not supported by these preconfigured files. Then i can only suggest using Gymnaes tutorial which explains most of the DSDT patching, config.plist editing and kexts used in detail and use the supplied files here as templates.
•	Warning: Some people have reported multiple data losses on this machine. I suggest using time-machine whenever possible!  
•	If you want to save the settings of the display brightness: downgrade to clover 3899, in the never versions the nvram restore is broken.  
•	USBInjectAll is not intended for permanent use. You should check which USB are really required to be injected and modify your DSDT/SSDT.  
•	4K Touchscreen only: Multitouch can be achieved with the driver from touch-base.com, but it's not open source - costs > 100 $  
## Tutorial Updates
•	28. December 2016: NVMe SSDT Spoof precreated, FakeID already preset in installation config.plist. VoodooHDA added as alternative to SSDT-ALC298 patch as well as color coding in tutorial  
•	22. December 2016: FakeSMBios added  
