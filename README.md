# OMV
Open Media Vault on a Raspberry pi 4

As of Nov 2023

Simplified instructions based on official instruction but use keyboard and monitor to set it up.
Official instructions: https://wiki.omv-extras.org/doku.php?id=omv6:raspberry_pi_install

Need:
  -  Raspberry pi 4 (or other)
  -  I used the Geekworm NASPi-Lite case
  -  Good 8GB SD card
  -  HDD.  I am using a 2TB spinning drive.
  -  Netowrk wire.
  -  Monitor and keyboard for setup.

Steps:
  1) Flash "Raspberry Pi OS Lite Bullseye" to the 8GB card using imager.  Use the 64GB version if you have an 8GB pi.
     - Set name to omv and local
     - You MUST turn on SSH or the script will not work (valid Nov 2023)
     - Set the username and password
     - Set the locale settings

  2) Boot your Pi with the 8GB card, and let it install, then logon. 
     
  3) Update OS and reboot:

         sudo apt update && sudo apt upgrade -y && sudo reboot
     
  4) Install OMB 6:

        wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash
    
  5) When it shows the IP address write it down and Now you can relocate your pi and power it up headless
     
  6) Configure OMV
     - Login to OMV: Go to a web browser and goto ip or omv.local.  username is admin.  pw is openmediavault
     - Wipe your HHD:  Goto Disks. Select your HDD, wipe it
     - Add an Ext4 FileSystem to the HDD
     - Mount FileSystem
     - Create a shared folder.  I called it called shared.  Give everyone full access
     - Add SMB service
     - Setup an SMB share to the shared folder.  Select GUEST ONLY.
