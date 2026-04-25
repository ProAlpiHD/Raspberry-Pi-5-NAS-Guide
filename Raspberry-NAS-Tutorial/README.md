# Raspberry Pi 5 NAS – Comprehensive Setup Guide
*A beginner-friendly guide to building your own high-speed Network Attached Storage.*

---

## 1. Introduction
### What is a NAS?
A **NAS (Network Attached Storage)** is basically your own private cloud. Instead of paying monthly fees to Google or Apple, you store your photos, videos, and documents on a hard drive connected to your Raspberry Pi. You can access these files from any device in your home network (and even from outside).

### Why Raspberry Pi 5?
The Raspberry Pi 5 is the first model with enough power and a dedicated PCIe interface to handle high-speed data transfers, making it the perfect heart for a home server.

---

## 2. Table of Contents
1. [Introduction](#1-introduction)
2. [Hardware Requirements](#3-hardware-requirements)
3. [Step 1: Preparing the OS](#4-step-1-preparing-the-os)
4. [Step 2: Access Pi and set up NAS](#5-step-2-access-pi-and-set-up-nas)
5. [Step 3: Installing the NAS Software](#6-step-3-installing-the-nas-software)
6. [Step 4: Creating Network Shares](#7-step-4-creating-network-shares)
7. [Troubleshooting & FAQ](#8-troubleshooting--faq)

---

## 3. Hardware Requirements
To follow this guide, you will need the following components:

| Component | Recommendation | Why? | Links |
| :--- | :--- | :--- | :--- |
| **Microcomputer** | Raspberry Pi 5 (4GB or 8GB) | The brain of your NAS. | https://www.berrybase.de/raspberry-pi-5-4gb-ram |
| **Power Supply** | Official 27W USB-C Power Supply | Critical for powering external hard drives. | https://www.berrybase.de/raspberry-pi-27w-usb-c-power-supply-netzteil-schwarz |
| **Cooling** | Active Cooler or Case with Fan | The Pi 5 gets hot during data transfers. | https://www.berrybase.de/raspberry-pi-active-cooler-luefter-fuer-raspberry-pi-5 |
| **MicroSD Card** | 16GB+ MicroSD (Class 10) | Holds the operating system. | - |
| **Storage** | External SSD, HDD, or M.2 NVMe | This is where your files live. | - |
| **Ethernet Cable** | Cat 5e or Cat 6 (LAN Cable) | Required for the initial installation to ensure a stable connection. | - |

> **Important Note on Connectivity:** You will need the Ethernet (LAN) cable for the initial setup. This ensures that the system installation and software updates are not interrupted. Once the configuration is finished and the Wi-Fi is reconnected, you can remove the cable and place the NAS wherever you like.

---

## 4. Step 1: Preparing the OS

1. Insert the MicroSD card into your computer.
2. Go to the **Raspberry Pi Imager** [download page](https://www.raspberrypi.com/software/) and install it for your operating system.
3. Once the installation is complete, open **Raspberry Pi Imager**.
4. Choose **Raspberry Pi 5** as the device and press **NEXT**.
5. Then select **Raspberry Pi OS (other)** and then **Raspberry Pi OS Lite (64-bit)**. The description must say **‘A port of Debian Trixie’**.

> **Note:** Why the 'Lite' version? A NAS (Network Attached Storage) runs in the background and doesn't need a visual desktop interface like Windows or macOS. The Lite version only uses a text-based command line. This saves a massive amount of system resources (RAM and CPU), leaving all the power for transferring your files quickly!

| 1. Raspberry Pi OS (other) | 2. Raspberry Pi OS Lite (64-bit) |
| :---: | :---: |
| <img src="Images/Screenshot 2026-04-25 132121.png" width="350"> | <img src="Images/Screenshot 2026-04-25 132130.png" width="350"> |

6. In the next window, select your microSD card and click **NEXT**.
7. After that, please enter a **hostname** – it can be anything, but you must remember it or write it down. 

> **Important:** The hostname is essentially the "name tag" of your Raspberry Pi on your network. Later on, instead of typing a complicated IP address, you can simply access your NAS by typing this hostname into your computer.

8. Then, in the next window, you’ll need to enter your location – first the country, then the time zone you live in, and at the very bottom, the keyboard layout you want to use.
9. Once you’ve clicked **NEXT**, you’ll need to choose a **username** and **password**. 

> **Note:** Please make sure to remember or write down these credentials. While it is technically possible to reset a lost password later, it is a very complicated and time-consuming process for beginners. Having these details ready will save you a lot of trouble when you allways try to log in to your NAS.

10. You will then need to enter the **SSID** of your Wi-Fi router (the network name you see on your phone) and the corresponding Wi-Fi password.

<img src="Images/Screenshot 2026-04-25 132830.png" width="400" alt="My Raspberry Pi 5 Setup">

11. In the next step, you need to choose the SSH authentication method. It’s very important that you enable it and select the top option: **‘Use password authentication’**. Once you’ve done all that, click **NEXT**.

> **Note:** SSH stands for "Secure Shell". Since we installed the 'Lite' version without a desktop, SSH allows us to connect to the Raspberry Pi from our main computer. We can type commands on our Windows/Mac keyboard, and they are executed securely on the Pi over the network. 

12. You shouldn't enable Raspberry Pi Connect, just press **NEXT** to skip it.
13. You can then check all your settings again and, if everything looks correct, simply click **FINISH** to format the microSD card and install the software.

---

## 5 Step 2 Access Pi and set up NAS

1. Open your command line tool: On Windows, search for and open **Windows PowerShell** or **CMD**. On macOS or Linux, open the **Terminal**.
2. To access your Raspberry Pi over your network, you need to use the SSH protocol. Enter the following command, replacing `<username>` and `<hostname>` with the exact details you chose in Step 1:
   
   `ssh <username>@<hostname>` 

> **Note:** Depending on your network, you might need to add `.local` to your hostname if the connection fails. For example, if your username is "admin" and your hostname is "mypi", you would type: `ssh admin@mypi.local`

3. Press **Enter**. You will then be asked to enter the password you created earlier. 

> **Important:** When you type your password, the screen will remain completely blank. There are no asterisks (*) or dots to show you what you are typing. This is a normal security feature in Linux! Just type your password blindly and press **Enter**. If you get a security prompt asking "Are you sure you want to continue connecting?", simply type `yes` and press **Enter**.

4. If your password was correct, you are now logged into your Raspberry Pi! Before installing new software, it is crucial to update the system. Enter this command and press **Enter**:
   
   `sudo apt update && sudo apt full-upgrade -y`

5. After the updates are finished, you should restart the Raspberry Pi to apply all changes. Type this command and press **Enter**:
   
   `sudo reboot`
> **Note:** As soon as you press Enter, your connection to the Pi will close immediately because the Raspberry Pi is shutting down to restart. This is perfectly normal! Please wait about 2 to 3 minutes for the Pi to boot up again. Once the wait is over, you will need to reconnect to the Pi by repeating steps 2 and 3 of this chapter.

6. Once you are connected again, let's check if your external hard drive is recognized by the Raspberry Pi. Plug your hard drive into one of the blue USB 3.0 ports on the Pi, wait a moment, and run this command:
   
   `lsblk`

> **Note:** This command lists all connected storage devices. You will see your MicroSD card (often listed as `mmcblk0`) and, if everything works, your external hard drive should appear as something like `sda` or `sdb` with its corresponding storage size.

7. Next, we need to verify your operating system version. Enter the following command:
   
   `cat /etc/os-release`

   Look for the line that says `VERSION_CODENAME`. It must say `trixie`. If it does not, you installed the wrong operating system version. In that case, you must go back to Step 1 and start again. Continuing with the wrong version will cause the NAS installation to fail.
8. If the codename is indeed `trixie`, you can start the OpenMediaVault (OMV) installation. Copy and paste this exact command into your terminal:
   
   `wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash`

> **Note:** This script automatically converts your Raspberry Pi into a NAS by installing OpenMediaVault. If the installation pauses and asks `[sudo] password for <username>:`, simply type your Raspberry Pi password again and press Enter. 
> 
> **Important:** This installation process downloads and configures a lot of packages. It can easily take 10 to 30 minutes! Do not close the window or unplug the Pi while this is running. 

9. If the installation finishes successfully, the Raspberry Pi will automatically restart. You might hear the fan spin up briefly. Wait about 3 to 5 minutes. Open a web browser on your main computer and type `http://<hostname>.local` (replace `<hostname>` with your chosen name). If you see the blue OpenMediaVault login screen, congratulations – the installation was a success!
10. To make accessing your NAS even easier in the future, it is helpful to know its exact IP address. Go back to your PowerShell or Terminal and type:
    
    `ping -4 <hostname>.local`

    This command will ping your Raspberry Pi. Look at the output – it will show a sequence of numbers separated by dots (for example, `192.168.178.69`). Please make a note of this IPv4 address, as you can use it in your browser instead of the hostname to access your NAS perfectly every time.

---

## 6. Troubleshooting & FAQ
**Q: I can't find my Pi on the network.**
*A: Make sure it is connected to the same router as your computer. Check the lights on the Ethernet port.*