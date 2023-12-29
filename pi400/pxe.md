# PXE Source Files

Served from the raspberry pi (arm64) but we need x86_64 files for the clients.  Obtained
from various packages relating to syslinux and syslinux-efi.

# PXE Notes

Simplest working configuration for Lorien (x86_64 standard BIOS) is to have a pxelinux.0 
file in the tftpboot directory and run the tftpboot service and DHCP server via dnsmasq.


The handoff of where to find the tftpboot location is done via this stanza

'''
#dhcp-boot=pxelinux.0
# Ensure we understand whether PXE or "DHCP-boot" is running.
dhcp-boot=x86/pxelinux.nosuch.0

#Turn tftp ON
enable-tftp

#Set tftp root
tftp-root=/share/tftpboot

#Provide network boot option called "Network Boot"

# The pxelinux.cfg/ directory is expected in the same directory as pxelinux.0 apparently.
# BIOS oriented systems.
pxe-service=x86PC,"Network Boot", pxelinux.x86.0

pxe-service=x86_64_EFI,"PXELINUX (EFI)",efi64/syslinux.efi

'''

To test the tftpboot environment, try installing the tftp client.

The default pxelinux environment then expects a pxelinux.cfg/ directory containing
a 'default' script as follows

'''
MENU TITLE Network Boot Menu
UI vesamenu.c32
MENU INCLUDE pxelinux.cfg/pxe.conf

LABEL next
    MENU LABEL Load local bootloader
    MENU DEFAULT
    localboot

# Separator
MENU SEPARATOR

LABEL reboot
    MENU LABEL Reboot
    COM32 reboot.c32

LABEL poweroff
    MENU LABEL Power Off
    COM32 poweroff.c32
'''

and the pxe.conf file references the first remote boot 'kernel' and 'initrd' [1].


'''
MENU BACKGROUND pxelinux.cfg/logo.png
MENU RESOLUTION 1024 768
NOESCAPE 1
ALLOWOPTIONS 0
PROMPT 0
menu width 32
menu rows 5
MENU MARGIN 0
MENU VSHIFT 10
MENU HSHIFT 46
menu color title                1;37;40    #ffffffff #00000000 std
menu color border               36;40      #c00090f0 #00000000 std
menu color sel                  30;47      #00000000 #ffffffff all
menu color unsel                37;40      #ffffffff #00000000 std
LABEL minimal
	MENU LABEL Minimal Linux
	LINUX ../bzImage
	INITRD ../initrd.img
LABEL alternative
	MENU LABEL CWD Linux
	LINUX bzImage
	INITRD initrd.img
'''


# UEFI Boot x86_64
