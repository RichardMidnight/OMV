# Open Media Vault (OMV 6) on a Raspberry Pi

A NAS on a Raspberry Pi. OMV 6, one 2 TB data disk exposed as the "shared"
folder (photos, videos, documents), backed up to a USB drive with the
"USB Backup" plugin. The OS runs off the SD card, so the SD card gets its
own backup too (below).

What's in this folder:

- README.md    — this document (the one thing committed to git)
- private.txt  — address, web/SSH logins (kept OUT of git, always)
- archive.txt  — the gory technical reference (agent working docs)
- state.txt    — dated state log ("before" baselines, update log)

## Disks

| Role      | Drive                                                    |
|-----------|----------------------------------------------------------|
| Source    | 2 TB data disk (internal)  →  the "shared" folder        |
| Destination | Seagate Backup Plus 2 TB (USB)  ←  where the backup lands |
| System    | SD card  (the OS boots from it)                           |

## Setup and installation  (Nov 2023)

The historical record of how this box was built. It is already installed
and in production — do NOT re-run these steps on the current machine
(step 6 wipes the data disk).

Simplified instructions to install OMV on a Raspberry Pi, based on the
official instructions but using a monitor and keyboard to keep it simple.

- Official instructions:
  https://wiki.omv-extras.org/doku.php?id=omv6:raspberry_pi_install
- Explaining Computers video:
  https://www.youtube.com/watch?v=IWDBYDYJVrA
  But note you MUST turn on SSH.

### Need

- Raspberry Pi 4 (or other)
- I used the Geekworm NASPi-Lite case
- A good 8 GB SD card
- HDD — a 2 TB spinning drive
- A network wire
- Monitor and keyboard for the simplified setup

### Steps

1. Flash "Raspberry Pi OS Lite Bullseye" to the 8 GB card using the imager
   (the 64-bit version if you have an 8 GB Pi).
   - Set name to `omv` and local
   - You MUST turn on SSH, or the script will not work (as of Nov 2023)
   - Set the username and password
   - Set the locale settings
2. Boot the Pi with the 8 GB card, let it install, then log in.
3. Update the OS and reboot:
   ```
   sudo apt update && sudo apt upgrade -y && sudo reboot
   ```
4. Install OMV 6:
   ```
   wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash
   ```
5. When it finishes installing, write down the IP address. Now you can
   relocate the Pi and run it headless (keep the network wire connected).
6. Configure OMV:
   - Log in to OMV: web browser to `omv.local` or the IP; user `admin`,
     password `openmediavault`
   - Disks → select the HDD → wipe it
   - Add an ext4 filesystem to the HDD
   - Mount the filesystem
   - Create a shared folder called "shared"; give everyone full access
   - Add SMB service
   - Set up an SMB share on the shared folder ("shared") — GUEST ONLY
7. Test from a Mac / Windows / Linux computer.

### Optional

8. Wireless — slower and less reliable.
9. Set up a backup drive (USB HDD) — see "Data backup" below.
10. Set up private shares for users.

## Management

Day-to-day: keeping the data backed up, the OS protected, and the software
current. Do the things in this part — that is what "owning" this box is.

### Data backup  ("shared" → Seagate)

There are two backup jobs in USB Backup; use the ENABLED one (shared →
Seagate). It is NOT on a schedule and NOT continuous: it copies when
triggered, then stops and lets the drive spin down. (It also starts on its
own whenever the backup drive is plugged in, so "leave it connected and it
stays backed up" works too.)

#### How to back up

1. Make sure the Seagate backup drive is plugged in (it has to be connected).
2. Web GUI → Services → USB Backup.
3. Select the ENABLED job (the row with the check mark: shared → Seagate).
4. Click RUN (the play/triangle icon) to start it now.

#### Checking it worked

- Web GUI: the notification bell (top right) reports when the job starts and
  finishes.
- The job's e-mail alert is known to fail with "message too big" — ignore
  it; the backup itself still completes.

#### With opencode (easiest — let it do the checking for you)

1. Start opencode and open this OMV folder.
2. Say: "Check that the backup is good."
   opencode reads the files here (README + archive) for the details, then
   SSHes into the box and confirms the run finished successfully AND that
   the data is actually present on the backup drive.

### Backing up / restoring the OMV system  (the SD card)

The USB/Seagate backup covers your FILES ("shared") only — not the operating
system. The OS runs off the SD card, so if that card dies the Pi will not
start — even though your data and data-backup are fine. So: whenever you
update the OS/software, and any time the SD card feels old, make a copy of
the SD card first.

#### The order (always)

1. Back up the OMV system (the SD card) — below.
2. Confirm the box is back up and healthy.
3. Then update the OS / OMV software — below.

#### Backing up the SD card (a full copy of the whole OS)

What you need: the other Raspberry Pi, with a drive that has a few GB free
(it holds the image while the main box is off).

1. Power the OMV Pi off (Web GUI → the power button, top right).
2. Remove the SD card from the OMV Pi.
3. Put the SD card into the other Pi and run PiSafe to make an image of it
   onto that Pi's drive. (PiSafe = the tool used to read/copy the SD card.)
4. Check the image file exists and is a real size (a couple of GB — not zero,
   not tiny).
5. Put the SD card back in the OMV Pi and start it again.
6. Confirm it came back: you can reach it (ssh / web / ping) and the time is
   right. (It has no battery-backed clock, so it may show a stale time for
   a moment while it re-syncs over the network — that is normal.)

#### Restoring (if the OS ever stops working)

Write the PiSafe image back onto a new SD card and boot the OMV from it —
that is your full "start over" copy of the whole OS + settings.
(Lighter option: OMV's built-in "System Backup" saves settings + installed
apps only; restores faster, but is not the whole OS and needs internet to
re-pull the software.)

### Upgrading / updating OMV  (the OS + OMV software)

Do this right after the SD backup, only when the box is confirmed back up
and healthy.

- **Web page (easiest):** log in to the web GUI → Updates page (under
  "System"; some builds call it "General") → read what's offered → install
  the available updates (OMV core + plugins + OS security). When it finishes,
  confirm what matters still works: ssh in, open the web GUI, and run ONE
  data backup to be sure the backup plugin still runs.
- **Over ssh:** `sudo apt update && sudo apt -y upgrade` (then reboot the
  box if it asks you to).

#### The "big one" — keep it separate

A "Debian 12 / bookworm" upgrade is a MAJOR move, not a normal update. If
one is offered, do NOT do it in the same go as the routine updates: routine
updates first, confirm the box is healthy, BACK THE SD UP AGAIN, and only
then do the bookworm move as its own careful step.

## Status

- Last good backup: 14 Oct 2026, 09:00–09:21 (incremental, ~14 GB, finished
  OK).
- Authoritative dated record (versions, pending updates, baselines): see
  state.txt.
