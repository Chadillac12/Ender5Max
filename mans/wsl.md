## Flashing the Cartographer Using WSL on Windows


### Installing WSL


Open the Windows command prompt by typing `cmd` in search, right-click the icon, and select **Run as administrator**:

```
wsl --install
```
*Note:
The command above only works if WSL is not installed at all. If you run `wsl --install` and see WSL help text, try `wsl --list --online` to view available distributions and run `wsl --install -d <DistroName>` to install a distribution.*

You can read more about installation [here](https://learn.microsoft.com/en-us/windows/wsl/install) 

**After installation, you must restart your computer!**
Otherwise you will get an error.

### Installing Ubuntu
It is quite possible that Ubuntu was installed automatically when you installed WSL. However, to be sure, let's install the correct package:
```
wsl.exe --install Ubuntu-24.04
```

![](/images/wsl_install.jpg)

After installation it will most likely start automatically and ask you to enter a username and then a password twice. If it didn't start: 


#### Starting Ubuntu
```
wsl.exe -d Ubuntu-24.04
```

 Enter a username and password — the password will not be displayed while typing, this is normal. Repeat the password entry.

It is a good idea to immediately check for and install updates:

```
sudo apt update && sudo apt upgrade -y
```

Exit the distribution for now, as we still need to install the USB connection package.

```
exit
```
### Viewing USB Devices in Linux 

Install the package for detecting connected USB devices:

```
winget install --interactive --exact dorssel.usbipd-win
```

To list all USB devices connected to Windows, open PowerShell **as administrator** and run the following command. After listing the devices, find and copy the bus ID of the device you want to connect to WSL:
```
usbipd list
```

![](/images/pid_list.jpg)

**Note your BUSID — it may differ from the one shown in this manual.**

Before attaching a USB device, you must use the `usbipd bind` command to share the device, which allows it to be connected to WSL. This requires administrator privileges. Select the bus ID of the device you want to use in WSL and run the following command. After running it, verify that the device is shared by running `usbipd list` again.


```
usbipd bind --busid 1-2
```

![](/images/bind_usb.jpg)

1. To attach the USB device, run the following command. (You no longer need an elevated administrator command prompt.) Make sure the WSL command prompt is open to keep the WSL virtual machine running.
2. Note that while the USB device is attached to WSL, it cannot be used by Windows. Once attached to WSL, the USB device can be used by any distribution running as WSL.
3. Verify that the device is attached using `usbipd list`. In the WSL command prompt, run `lsusb` to confirm that the USB device appears and can interact using standard Linux tools.

*For example, if the output shows:*

```
BUSID  VID:PID    DEVICE
1-2    1d50:614e  Cartographer
```
then enter 1-2 in the command. **Warning: use your own number, not the one from the example!**

```
usbipd attach --wsl --busid 1-2 --auto-attach
```

*Don't forget to replace `<busid>` with your own value.*

![](/images/usb_attach.jpg)


If a red error message appears:

![](/images/error_usb.jpg)

Don't worry — in a separate window, simply start Linux:
```
wsl.exe -d Ubuntu-24.04
```
Then retry the command in the previous window:

```
usbipd attach --wsl --busid <your_busid> --auto-attach
```

*Don't forget to replace `<your_busid>` with your own value.*

Important! We intentionally added `--auto-attach` — it will be useful later when running the flashing script.


### Open Ubuntu 

```
wsl.exe -d Ubuntu-24.04
```

Install USB utilities:

```
sudo apt install usbutils
```
Then finally check what is on the USB ports:

```
lsusb
```

![](/images/lsusb_.jpg)


You will see the device you just connected and will be able to interact with it using standard Linux tools.

You can read more details [here](https://learn.microsoft.com/en-us/windows/wsl/connect-usb)


### Flashing the Cartographer

Run the following commands to install the required packages:

```
sudo apt-get install virtualenv python3-dev python3-pip python3-setuptools libffi-dev build-essential git dfu-util
```

Cartographer software:

```
git clone "https://github.com/Klipper3d/klipper" $HOME/klipper
git clone "https://github.com/Cartographer3D/cartographer-klipper.git" $HOME/cartographer-klipper
```
To flash firmware 5.1.0, you need to download the latest master branch and switch to it:

```
cd $HOME/cartographer-klipper
git fetch
git switch master
git reset --hard origin/master
```
Set up the Klipper virtual environment:

```
virtualenv --system-site-packages $HOME/klippy-env
$HOME/klippy-env/bin/pip3 install -r $HOME/klipper/scripts/klippy-requirements.txt
```

**Verify everything is done correctly:**

```
lsusb
```
The output should contain something like `1d50:614e` in the ID list.

### Flashing 

```
CARTO_DEV=$(ls /dev/serial/by-id/usb-* | grep -E "IDM|Cartographer" | head -1) && cd $HOME/klipper/scripts && sudo -E $HOME/klippy-env/bin/python -c "import flash_usb as u; u.enter_bootloader('$CARTO_DEV')" && echo "⏳ Waiting for device to reconnect..." && sleep 3 && for i in {1..20}; do KATAPULT_DEV=$(ls /dev/serial/by-id/usb-katapult* 2>/dev/null) && [ -n "$KATAPULT_DEV" ] && break; sleep 0.5; done && sudo -E $HOME/klippy-env/bin/python $HOME/klipper/lib/katapult/flashtool.py -f $HOME/cartographer-klipper/firmware/v2-v3/survey/5.1.0/Survey_Cartographer_K1_USB_8kib_offset.bin -d $KATAPULT_DEV
```

**What this command does:**

The device is running as usb-Cartographer

The script calls enter_bootloader

The device reconnects as usb-katapult

Windows automatically attaches it in WSL

The script detects the new /dev/serial/by-id

Flashing via Katapult



You should see output similar to this:

```
Connecting to Serial Device /dev/serial/by-id/usb-katapult_stm32f042x6_2F003D001653584833373720-if00, baud 250000
Detected USB device running Katapult
Attempting to connect to bootloader
Katapult Connected
Software Version: ?
Protocol Version: 1.0.0
Block Size: 64 bytes
Application Start: 0x8002000
MCU type: stm32f042x6
Flashing '/home/ujif8/cartographer-klipper/firmware/v2-v3/survey/5.1.0/Survey_Cartographer_K1_USB_8kib_offset.bin'...

[##################################################]

Write complete: 22 pages
Verifying (block count = 351)...

[##################################################]

Verification Complete: SHA = E0F7104BECCF44EDBB9346C237C28A42AF072BEC
Programming Complete

```

![](/images/complete.jpg)

You can read more [here](https://pellcorp.github.io/creality-wiki/cartographer_flashing/) 
