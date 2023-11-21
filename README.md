# OMV
Open Media Vault on a Raspberry pi 4

As of Nov 2023

Official instructions: https://wiki.omv-extras.org/doku.php?id=omv6:raspberry_pi_install

Need:
  -  Raspberry pi 4
  -  I used the Geekworm NASPi-Lite case
  -  Good 8GB mmc card
  -  HDD

Steps:
  1) Flash RaspiLite Bullseye to the 8GB card.  Use the 64GB version if you have an 8GB pi.
     - you MUST turn on ssh
     - easiest if you set the username / password and local settings
  2) Boot your Pi with the 8GB card
  3) Update os and reboot:

         sudo apt update && sudo apt upgrade -y && sudo reboot
     
  4) Install OMB 6:

    wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash
    
  5) When it shows the IP address write it down and Now you can relocate your pi and power it up headless
  6) Configure OMV
     - Login to OMV: Go to a web browser and goto ip or name.  username is admin.  pw is openmediavault
     - Wipe your HHD:  Goto Disks. Select your HDD, wipe it
     - Add an Ext4 Filesystem to the HDD
     - Mount FS
     - Create a shared folder.  called shared.  Give everyone full access
     - Add SMB service
     - Setup SMB share to the shared folder.  Select GUEST ONLY
