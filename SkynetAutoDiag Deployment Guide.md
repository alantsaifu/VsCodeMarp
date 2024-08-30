# SkynetAutoDiag Main Platform Easy Deployment Guide

This document will guide you through the easy deployment of the SkynetAutoDiag diagnostic tool on the main platform of a master-slave system and how to start the service at `127.0.0.1:5000`.

## 1. Preparing the Installation Media

First, you need to prepare a **USB drive with at least 32GB of storage capacity** and follow these steps:

1. **Install Ventoy on the USB Drive**
   - Download the [Ventoy](https://www.ventoy.net/) tool and run `Ventoy2Disk.exe`.
   - In the main screen, click on the menu `Option -> Partition Configuration` and set the following:
     1. Select **exFAT** as the `File System`.
     2. Check the box for `Preserve some space at the end of the disk` and select **20GB**.
   - Return to the Ventoy installation wizard, find, and click the "Install" button to start the Ventoy installation process. Note: The installation process may involve formatting the USB drive.
   - Once Ventoy is successfully installed, the USB drive's partition will be named **Ventoy**.
   - Ensure that you see the correct `Ventoy In Device` information on the main Ventoy screen.

2. **Create a New Partition on the USB Drive**
   - Press **Win + R** to open the "Run" dialog box, then enter the following command:
     ```
     compmgmt.msc
     ```
   - In the Computer Management console, click on **Disk Management**, find the Ventoy USB drive, and format the 20GB reserved space as **FAT32**. Name this partition `SAD` (SkynetAutoDiag Execution Area).

## 2. Adding the Clonezilla Image

Add the Clonezilla image to the Ventoy partition to use for future system backup and restoration:

1. **Download Clonezilla**
   - Visit the [Clonezilla official website](https://clonezilla.org/) to download the latest ISO file.

2. **Copy the Clonezilla ISO File to the Ventoy Partition**
   - Copy the downloaded Clonezilla ISO file directly to the Ventoy partition on the USB drive.

## 3. Adding the SkynetAutoDiag Image File

Add the SkynetAutoDiag image file to the `SAD` partition on the USB drive:

1. **Prepare the SkynetAutoDiag Image File**
   - Ensure you have the compressed file named `SkynetAutoDiag_2024-08-28-03-img.7z`.

2. **Extract and Copy the Image File to the `SAD` Partition**
   - Extract the `SkynetAutoDiag_2024-08-28-03-img.7z` file and copy the image file to the `SAD` partition on the USB drive.

## 4. Starting Clonezilla and Restoring the System Image

1. **Boot from the Ventoy USB Drive**
   - Restart the computer and boot from the Ventoy USB drive. When you see the option `clonezilla-live-3.1.3-16-amd64.iso`, press **Enter** to proceed.

2. **Enter Clonezilla and Configure Settings**
   - Select `Clonezilla live (VGA 800*600)` and press **Enter** to continue booting.
   - During the boot process, you may be prompted to choose a language. Use the arrow keys to select `English` and press **Enter** to confirm.
   - Choose the appropriate keyboard layout, then press **Enter** to apply.

3. **Mount the Image Partition**
   - In the Clonezilla startup menu, select `Enter_shell` to enter the console.
   - Enter the following commands to mount the partition:
     ```bash
     sudo mount /dev/sdb3 /mnt
     sudo mount --bind /mnt /home/partimag
     ```
   - After mounting, return to the Clonezilla startup menu and select `Start Clonezilla` to launch the main Clonezilla program.

4. **Restore the System Image**
   - In the Clonezilla interface, select the `device-image` option.
   - In the following interface, choose the mounted `/home/partimag` directory.
   - Press **Enter** to continue, then choose `Beginner mode` to proceed.
   - Select the `restoredisk` option to continue and locate the system image file you intend to restore.
   - Choose the target hard drive partition (HDD) as the destination for the restore operation.
   - Confirm the settings and choose `Default Settings` to proceed with the Clonezilla process.
   - Press **Enter** to continue and confirm the restore operation.

5. **Complete the Restoration and Boot the System**
   - Wait for the progress bar to complete, and the SkynetAutoDiag diagnostic tool will be successfully deployed to the hard drive.
   - Once completed, remove the USB drive, restart the computer, and the system will automatically boot from the HDD and enter the SkynetAutoDiag diagnostic tool system.

## 5. Starting the SkynetAutoDiag Service

After the system boots, open Chrome and enter `127.0.0.1:5000` in the address bar to start the SkynetAutoDiag diagnostic tool.

## 6. Installing the SUT System on the SUT Machine

Follow steps (1)~(4) to install the SUT system on the SUT machine:

### Adding the SUT Image File
Add the SUT image file to the `SAD` partition on the USB drive:

1. **Prepare the SUT Image File**
   - Download the compressed file named `SUT_OS_2024-08-28-10-img.7z` from the internal Tinyfile FTP.

2. **Extract and Copy the Image File to the `SAD` Partition**
   - Extract the `SUT_OS_2024-08-28-10-img.7z` file and copy the image file to the `SAD` partition on the USB drive.

### Configure Network Settings in SUT CentOS System
After entering the SUT CentOS system, you can execute the `auto_add_net_profile` script to automatically create network card configurations and enable DHCP for IP assignment.

## 7. Setting Up the Local Area Network

To establish the local area network (LAN) for SkynetAutoDiag, you need to set up a router to allocate DHCP and a switch to connect the SkynetAutoDiag master server and multiple slave SUT machines:

1. **Setting Up the LAN Components**
   - Connect a router to distribute DHCP IP addresses within the network.
   - Use a switch to connect the SkynetAutoDiag master server and the slave SUT machines to the network.

2. **Obtaining the Slave SUT Local IP**
   - After the setup is complete, each slave SUT will receive a local IP, such as `192.168.23.167`.

3. **Configuring the SkynetAutoDiag Software**
   - Enter the obtained local IP into the "Remote IP" field in the SkynetAutoDiag software interface.
   - Enter a "Project Name," for example, `SG220`.
   - In the "SUT Auto Diag Type" dropdown menu, select from the following options:
     - `reboot`: Performs an OS ACPI soft reboot.
     - `reset`: Performs a BMC soft reboot of the SUT.
     - `dc`: Performs a BMC hard reboot of the SUT.

4. **Setting Test Parameters**
   - Enter the desired number of cycles in the "Total Cycle" field.
   - Enter the required stress test duration in the "Stress Time" field (in seconds). This stress test is executed once during Cycle 2.

5. **Starting the System Diagnostic**
   - After confirming all parameters are correct, click the "Trigger Build" button to start the system diagnostic process.

6. **Viewing the Test Report**
   - After the diagnostic process is complete, a **Test Report** will be generated.
   - Click on the **Log Path** link within the report to access the log files. These logs can be used to analyze the system's status.

7. **Running Diagnostics on Multiple SUTs**
   - If you need to run diagnostics on multiple SUTs simultaneously, click the **"Open New SUT"** button.
   - This will allow you to open additional threads, enabling multiple SUTs to run test cases concurrently.

# Troubleshooting

This section addresses common issues and simple troubleshooting steps to resolve them.

## 1. System Stuck at Bootloader During Startup

If the system gets stuck at the Ubuntu boot logo or displays a message like "dev/sdaX: clean, ... files ... blocks," follow these steps to resolve the issue:

1. **Restart the System**: Power off the system and turn it back on.
2. **Access Grub Menu**: During the POST process, press the **ESC** key to enter the Grub boot menu.
3. **Select Recovery Mode**:
   - Choose the second option: `Advanced options for Ubuntu`.
   - Then select: `Ubuntu, with Linux 6.8.0-40-generic (recovery mode)`.
4. **Run dpkg to Repair Broken Packages**:
   - Once the Recovery Menu appears, select the third option: `dpkg - Repair broken packages`.
   - Wait for the process to complete.
5. **Resume Normal Boot**:
   - Return to the main menu and select `resume - Resume normal boot`.
   - The system should now successfully boot into the operating system.

## 2. Adding and Enabling a New Network Card's MAC Address

If the system does not automatically add and enable a new network card's MAC address, you can manually add and enable it by following these steps:

1. **Access the Network Settings**:
   - Open the **Settings** tool on your system.
   - Navigate to the **Network** menu and click on it.
   - Click on the **+** icon to add a new network profile.

2. **Create a New Network Profile**:
   - When the **New Profile** menu appears, click on the **Identity** tab to create a new profile.
   - In the **Name** field, you can define a name for the profile, for example, `"Profile 1"`.
   - In the **MAC Address** dropdown menu, select the MAC address of the network interface connected to the network cable.
   - Finally, click the **Add** button to add the profile.

These steps will allow you to manually add and enable the network card with the correct MAC address.

