# RetroPie-Setup-Ubuntu

Script to automate the installation on RetroPie on Ubuntu 20.04, with the end-state user experience nearly identical to a Raspberry Pi installation, but with the power and flexibility of x86.

This script was inspired by feedback provided on the [RetroPie forums](https://retropie.org.uk/forum/topic/18810/retropie-installation-on-ubuntu-server-x64-18-04-1), and does the following:

- Disables sudo password prompts
- Installs the minimal OS dependencies needed to install OpenBox and run RetroPie
- Installs the RetroPie 'basic' packages
- Install the latest video drivers for Intel, Nvidia, and Vulkan
- Applies the RetroPie-PacMan Plymouth theme to run during startup/shutdown
- Hides all GRUB and kernel text output during startup/shutdown
- Enables autologin and boots directly into OpenBox / EmulationStation
- Hides all terminal window 'chrome' in OpenBox (menu, scrollbar, cursor) when launching emulators
- Uses 'unclutter' to show the mouse cursor only when it is being moved
- Enables 1080p resolution for GRUB and X Windows to improve the user experience & performance on 4K displays (user configurable)

The script also provides the ability for additional customizations, optionally executing external scripts at the beginning and end of the installation process. A library of user-submitted scripts is available to get you started. See the [Optional Scripts](#optional-scripts) section for more information.

Of course, your mileage may vary when using the master `retropie_setup_ubuntu.sh` script. It has been organized into functions to improve readability. If you don't want to run the full script, you can copy/paste the sections that apply to the specific changes you wish to make. Just note that many of these commands would need to be run in `sudo` if executed independently and _variable substitution may be required_.

## Installing the Base OS

### Download a copy of Ubuntu 22.04 or 20.04

These scripts are intended for one of the following Ubuntu 22.04 or 20.04 installations:

- [22.0.4 Live Server (**Recommended**)](http://releases.ubuntu.com/jammy/ubuntu-22.04.3-live-server-amd64.iso)
- [20.0.4 Live Server](http://releases.ubuntu.com/focal/ubuntu-20.04.1-live-server-amd64.iso)
- [20.0.4 Legacy Server](http://cdimage.ubuntu.com/ubuntu-legacy-server/releases/20.04/release/ubuntu-20.04.1-legacy-server-amd64.iso)
- [20.0.4 Legacy Minimal Install (mini.iso)](http://archive.ubuntu.com/ubuntu/dists/focal/main/installer-amd64/current/legacy-images/netboot/mini.iso)

### Manual Ubuntu Install

Perform a basic install of Ubuntu with these basic options

- Language: `English`
- If prompted to do so, choose to `Update to the new installer`
- Keyboard configuration:
  - Keyboard: `English (US)`
  - Keyboard variant: `English (US)`
- Type of install:
  - The base for the installation: `Ubuntu Server`sudo 
  - Additional options: `Search for third-party drivers` checked/selected
- Network connections: configure as needed to obtain a valid IP address
- Configure proxy: leave blank unless your network uses a web proxy (which would not be typical)
- Configure the Ubuntu archive mirror
  - The default mirror should be OK
  - Wait for the test to complete (`This mirror location passed tests.`), then press `Enter/Return`.
- Guided storage configuration: use defaults (preferred, not required), i.e.:
  - Leave selected: `Use an entire disk`
  - Leave selected: `Set up this disk as an LVM group`
  - Leave **unselected**: `Encrypt the LVM group with LUKS`
- Profile setup:
  - Your name: `pi`
  - Your server's name: `retropie`
  - Pick a username: `pi`
  - Password: `raspberry`
- Upgrade to Ubuntu Pro
  - Leave `Skip for now` selected
- SSH Setup
  - Optional: Choose to `Install OpenSSH server` (which allows for remote access, copy/paste)
  - Import SSH identity: leave `no` selected
- Complete/acknowledge the `Third-party drivers` screen
- Do not select any `Featured Server Snaps`

### Automated Ubuntu Install - Live Server

If you are familiar with the use of [autoinstall](https://ubuntu.com/server/docs/install/autoinstall) files to automate Ubuntu installs (no support here), you can use the example [autoinstall-user-data](autoinstall/autoinstall-user-data) file to perform a basic install against the Live Server installer. _Use at your own risk!!!_

### Automated Ubuntu Install - Legacy Mini & Legacy Server

If you are familiar with the use of [preseed](https://help.ubuntu.com/lts/installation-guide/s390x/apbs02.html) files to automate Ubuntu installs (no support here), you can use the example [retropie.preseed](autoinstall/retropie.preseed) file to perform a basic install against the Legacy Server or Mini installer. _Use at your own risk!!!_

## Start the RetroPie Setup Script

Note: Do not log in via SSH yet.
If you do, the RetroPie setup process will disconnect your SSH session and make the setup process appear hung.

- If you have not already done so, reboot the Ubuntu Server following the operating system installation, then log in as the `pi` user.
- Download the `bootstrap.sh` script

  `wget -q https://raw.githubusercontent.com/MizterB/RetroPie-Setup-Ubuntu/master/bootstrap.sh`

- Run the `bootstrap.sh` script

  `sudo bash ./bootstrap.sh`

  Optionally, you can override the Git repository (-r) and branch (-b) used by the bootstrap script. This is helpful if you want to bootstrap from your fork or a different branch. The available options can be passed as follows:

  `sudo bash ./bootstrap.sh -r https://github.com/<USERNAME>/RetroPie-Setup-Ubuntu -b <BRANCHNAME>`

- Configure [Optional Scripts](#optional-scripts), if desired

- Run the installer script as sudo

  `sudo ./RetroPie-Setup-Ubuntu/retropie_setup_ubuntu.sh`

- The script will run for some time.
Depending on your system's architecture, you may see RetroPie Setup start, resulting in the process pausing at a disclaimer screen.
  - If this happens, press `Enter` to accept the disclaimer.
  - Then, use the arrow keys to select `Exit`, then press `Enter`.
  - The installation of RetroPie continues.

- When prompted, reboot the system

## A Note on Exiting and Restarting EmulationStation

This setup uses a desktop environment called `Openbox` to bootstrap the start of `EmulationStation`.
This is done to ensure that EmulationStation is consistently launched in full screen and the foreground.

As a consequence of this setup, when you exit EmulationStation, you will be faced with a black screen instead of a terminal prompt.
To return to a terminal prompt, right-click the mouse and a pop-up menu will appear.
Click `Exit`, then click `Exit` again to confirm.
You are now in a terminal.

Because EmulationStation requires a desktop environment, to restart it, we'll need to first start Openbox.
Therefore, the easiest way to restart EmulationStation is to type:

`startx`

## Optional Scripts

The main `retropie_setup_ubuntu.sh` script is intended to create a Ubuntu environment that closely mimics the basic RetroPie image for Raspberry Pi. However, some users have suggested additional tweaks and features that further customize the Ubuntu environment beyond this scope, as well as experimental changes that may be incorporated into the master script in the future. The `optional_scripts` directory provides the ability to include these additional changes in the installation process or to write scripts based on your needs.

### The Library

Inside `optional_scripts/library`, you will find a collection of additional scripts that further extend the installation process. While the script names are intended to be descriptive, additional details for each script are included at the beginning of each file.

### Pre-Install and Post-Install Scripts

The `optional_scripts/pre_install` and `optional_scripts/post_install` directories can be populated with BASH scripts that are run at the very beginning and end of the master installation process. Scripts in these directories will be executed in alphabetical order, so if the order is important to you, you can prepend the file names with a number (e.g. `01-myscript.sh`) for greater control.

If you want to include a Library script in your pre or post-install process, the easiest way to do this is to simply copy it into the appropriate folder. Alternatively, you can also move the Library scripts or create symbolic links to them.

You can also write your custom scripts and place them in these directories. Use the scripts in the Library as an example. Note that variables defined in the master script (`USER_HOME`, `SCRIPT_DIR`, etc.) are also available to the custom scripts and that all output will be automatically written to both the console and log file.

Additionally, you can create a single pre/post-install script that calls multiple Library scripts by using the `source` command. Note that paths should be relative to the `retropie_setup_ubuntu.sh` script. For example:

```bash
#!/bin/bash
###############################################################################
# This is an example script that wraps multiple Library scripts
# that disable system services
###############################################################################
echo "--------------------------------------------------------------------------------"
echo "| Disabling multiple services via Library scripts"
echo "--------------------------------------------------------------------------------"
echo -e "\n\n"
source ./optional_scripts/library/disable_apparmor.sh
source ./optional_scripts/library/disable_avahi_daemon.sh
source ./optional_scripts/library/disable_bluetooth.sh
source ./optional_scripts/library/disable_modemmanager.sh
source ./optional_scripts/library/disable_samba.sh
echo -e "FINISHED $BASH_SOURCE \n\n"
```

### Submissions Welcome

If you have master script changes or additional features that you would like to include in the Library, please share!
Pull Requests are preferred.

## Calling Single Functions or Optional Scripts

Running the installer script `retropie_setup_ubuntu.sh` without any arguments results in the full installer logic being executed. But you can also pass individual function names as arguments, and the script will just execute those functions. For example:

`sudo retropie_setup_ubuntu.sh "set_resolution_xwindows 720x480"`

or

`sudo retropie_setup_ubuntu.sh "run_optional_scripts optional_scripts/pre_install/pre_install.sh"`

or

`sudo retropie_setup_ubuntu.sh "run_optional_scripts optional_scripts/library/enable_wifi.sh"`

A couple of things to note...

- Take care when running a function or script more than once - depending on the steps it takes, it could create duplicate configuration entries and break your system!
- When calling an optional script, the script path should be absolute, or _relative to the current directory_. This means that you can also run arbitrary scripts from outside the Retropie-Setup-Ubuntu directory structure if desired.

## CHANGELOG

### 20221226

- Explicitly support Ubuntu 22.04
- Clarifications to make the setup process more user-friendly
- Markdown cleanup

### 20200923

- 20.04 promoted to master branch
- Instructions for multiple 20.04 install methods, including autoinstall example
- New bootstrap installation process
- Customizations are possible through pre_install and post_install script directories
- Library of optional scripts
- The log file is timestamped
- Script runtime is now calculated
- Prevent running as the root user, only allow sudo as a normal user
- _MANY THANKS TO [@etheling](https://github.com/etheling) FOR [MULTIPLE RECOMMENDATIONS](https://retropie.org.uk/forum/post/234008) FOR THIS RELEASE!_

### 20200521

- Validated against Ubuntu 20.04 LTS
- Verbose logging to both console and file
- Improved modularity, formatting, and commenting of functions
- Installs the same base packages as the official RetroPie image
- Installs the latest Intel video driver
- Option to reboot after install
- Disabled screen blanking
- Safely enable 1080p resolution in GRUB, if available _- the user can override the screen resolution in script_
- Safely enable 1080p resolution in X Windows, if available _- the user can override the screen resolution in script_
- _MANY THANKS TO [@movisman](https://github.com/movisman) FOR HIS [CODE OPTIMIZATION AND NEW FUNCTIONS](https://github.com/MizterB/RetroPie-Setup-Ubuntu/pull/4)!_

### 20190728

- Changed name to reflect support for both Server and Mini versions of Ubuntu
- Added `retropie.preseed` file to standardize basic OS install & config
- Installs the basic_install RetroPie meta-package, rather than its individual parts
- Improved hiding of boot messages
- Updated autostart logic, moved EmaulationStation launch into RetroPie's autostart.sh file
- Enabled installation of updated video drivers and Vulkan by default
- Removed install of Ultimarc-linux and RetroPie launchingimages package.

### 20190530

- Initial release
