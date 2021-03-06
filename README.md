# Voron-V0.564
This is the documentation of the V0564 refurbishment

After the initial setup has run half a year of service, it was time for a soft-refurbishment. I appreciate minimalism, however half a year of printing tought me I need some utilities and configuration changes for comfort and effectiveness. 
First, with the release of the release of the Raspberry Zero W 2, there is enough computing power with a reduced footprint to feed direct off of the Big Tree Tech E3 mini. This reduces also the wire clutter and clears the V0.1 from an additional voltage converter.
<img width="1025" alt="Schermafbeelding 2021-12-29 om 11 49 50" src="https://user-images.githubusercontent.com/61151314/147654966-99fabae2-09ca-45a7-9f58-60112167e52c.png">
* in this configuration, both SD-card slots can be accessed without taking off the back cover. The altered SKR_E3_Mounting_Bracket mounts to the vertical 1515 profile using two M3*16 BHCS fastners next to the VHB tape, and it holds a tie-wrap mount for the umbilical.
* The RPI has its own mounting bracket and bonds to the Mid-panel using 20mm VHB tape.
<img width="300" alt="image" src="https://user-images.githubusercontent.com/61151314/147820874-e55cf62e-23d9-4c15-b7b5-de01e9ec226c.png">
Find the file here: https://github.com/SanderRave/Voron-V0.564/blob/407bb8968269d2263191a25c71a370739c0183fd/Rave_RPI0W2_V0_Mount.stl

My initial idea was to solder on a reduced header (only RX/TX to E3 and SPI header for the ADXL345 accelerometer for input shaper. After some thought, you never know what the future will bring, so a full header it is.
In this repository I will document the basic workflow, as well as the changes I found usefull for my needs, and I hope it will improve the experience of other Voron builders and 3D printing enthousiasts.

<img width="300" alt="image" src="https://user-images.githubusercontent.com/61151314/147820937-3d84b407-bff1-497a-882f-24bfa48300ba.png">
Find the file here: https://github.com/SanderRave/Voron-V0.564/blob/263e1282da5027531e298a2915c9b21c5a905afc/Skr_E3_umbilical_Mounting_Bracket_x1.stl

My workflow and learnings to install Klipper and MainsailOS:
Before starting, safe all your config files, calibration files etc. before flashing your SD card. Once flashing, there is no way back.
1. Download the latest image of Mainsail from https://docs.mainsail.xyz/setup/mainsail-os
2. Because of the level of control over the individual configuration steps, I prefer to burn the image with Balena etcher https://www.balena.io/etcher/
3. Straight after writing the image, re-insert your SD-card to configure your network in the file mainsailos-wpa-supplicant.txt. Uncomment your applicable network options and fill out the credentials. Safe the file, insert the card into the RPI Zero, and fire it up to install the image.
4. If you want to give your machines fixed IP addresses, follow the next steps: find your RPI in Terminal. Type 'ping mainsailos.local' for your IP address, if there is no other instance of MainsailOS running. Type 'arp -a' and get an overview of all the machines in your network and identify your new installation.
5. ssh into your new installation (sudo nano /etc/dhcpcd.conf) and alter the information. Credentials: pi@{RPi-IP-Address} passwd raspberry. For a detailed explanation, search for raspberry-pi-with-a-static-ip-address in your search engine of choice. I keep a copy of the file at hand and copy-and-paste the content in via Cyberduck.
6. If you are replacing an old installation at the same IP address, chances are, you get a warning "WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!" Type ssh-keygen -R {RPi-IP-Address}
7. Change your password: ssh into your RPI with the default credentials, type 'yes' when asked for confirmation. type passwd to set a new password (recommended).
8. In your browser type 'mainsailOS.local' and click on Machine in the left collumn.
9. Upload your safed config files.
10. Read the version changes and run essential/relevant updates and upgrades
11. Time to flash the MCU; my favourite resource (surprisingly) is https://docs.vorondesign.com/build/software/#firmware-flashing, or jump straight to https://docs.vorondesign.com/build/software/miniE3_v20_klipper.html for the E3 mini and follow the steps described in the document.In the last step, instead of USB, select 'Serial (on USART2 PA3/PA2)' and make the image for the MCU.
12. Check the following configurations 1. Swapping ports used by GPIO and Bluetooth: sudo nano /boot/config.txt Move the cursor to the very end and check if the following line is present: dtoverlay=pi3-miniuart-bt. 2. Make sure the serial console is disabled: sudo nano /boot/cmdline.txt. Look for following string (text) and delete it, if present: console=serial0,115200
13. I prepare my installation for Resonance testing at this point. Because, hey, if not now, when then? https://www.klipper3d.org/Measuring_Resonances.html
14. If you want a simple and straightforward VPN: I use Tailscale: https://tailscale.com/download/linux/rpi
15. Copy the image to your computer, rename the image to firmware.bin and flash your MCU. 
16. Update your printer.cfg: serial: /dev/ttyAMA0
Basic steps, wiring illustrations, background information etc. can be found in the YouTube video of @3DPNero https://youtu.be/AtW3GqkKUz8
