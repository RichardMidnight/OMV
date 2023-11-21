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
    
  5) 
