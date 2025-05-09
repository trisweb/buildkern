# buildkern
Lightweight Debian-based custom kernel downloader, builder, and manager

## Overview

**buildkern** makes downloading, compiling, and managing custom kernels for your Debian-based Linux computer as easy as other common every-day maintenance tasks.

Buildkern works with the apt package manager and the build-in kernel compiler debian packaging pipeline to enable you to treat your custom kernels as a real system package, just like your distro would.

Buildkern does not come with kernel configuraton! This is the most critical part of any custom kernel. See the section "Making your kernel config" below for more information.

For normal desktop kernel compilation once you have everything set up (see below), you can just run 'buildkern -o'. This will download, configure, and build a kernel optimized for exactly your current system (and only your current system). Note: it uses the default optimization level and compile flags, as the kernel is sensitive to compiler flags and there's near zero benefit. If you would like to use optimization flags, such as -O3, these can be configured in your kernel config.

Run buildkern with no arguments for full usage.

*Note: This script is not designed for building generic or portable kernels. It builds kernels for your current machine. Generally speaking, this will mean a faster, more appropriate kernel for your hardware that's streamlined and compiled for exactly what your CPU likes. It can result in a 3-5% speedup from a stock generic kernel, which is generally speaking not worth the trouble, but if it's easy enough, why not?*

## Installing

1. Install dependencies:
   `sudo apt install build-essential bc kmod cpio flex libncurses5-dev libelf-dev libssl-dev dwarves bison packaging-dev`
    
2. Download the buildkern script: `wget https://github.com/trisweb/buildkern/raw/refs/heads/main/buildkern`
3. Edit the script file to configure
   * You should enter your kernel LOCALVERSION (the name of your custom kernel, usually a suffix) within the script file, so that `--clean` can restrict itself to deleting only your custom kernels.
   * You may also update paths for the kernel source and config backups.
4. Make the file executable: `chmod +x buildkern`
5. Copy the `buildkern` executable script to a directory in your $PATH. `$HOME/bin` if you use that, or `/usr/local/bin` works well.

## First Run

1. Run `buildkern` to test, and `buildkern -v` to test finding the latest kernel version.
2. Run `buildkern -d` to download the latest kernel sources.
3. Go to the source folder, usually located in $HOME/src/linux unless you changed that in the prior config steps— `cd $HOME/src/linux`

### Making your Kernel Config

At this point, you have a few options on how to generate your kernel configuration. This is the most important step of creating your kernel, that will ensure first and foremost that it works for your system and devices, but additionally that it is minimal and efficient in both compilation time and size. The kernel is large and contains drivers for thousands of devices, not all of which are needed for your system—however, if you miss a device that is required, then it could cause failure to boot, or other random dysfunction.

**There are two basic options:**
   
#### Copy an existing configuration (Recommended)
   
Copying an existing configuration from a working, generic kernel is a failsafe way to achieve a running and stable kernel. To find your existing config, look for config files in your `/boot` directory or equivalent, or look in /usr/src/linux.

Once you have a config, you can  "whittle it down," removing modules and devices you don't need, and adding any that you prefer or will need. Usually, you will be painstakingly removing many options using this method, however it is a good exercise to understand the kernel you are building.

#### Create a new configuration for your system from scratch (Advanced)
   
With this method, you will be starting from a minimal set of drivers and options and "additively" making a kernel by adding what you have. You need to take care to include every device your system has, for which there are a few strategies to discover and catalog.
   
Here is a guide to the various options for generating a brand new config. A good start is `make localmodconfig`, which will build a kernel with modules that are currently loaded on the running system, however it will be incomplete.

## General advice for building your own kernel

Undoubtedly, you will remove a module or driver that you thought you did not need, only for some obscure corner of the system to fail to function when asked, debug for 3 days, and finally try a generic kernel only for everything to work, realizing that your custom kernel is to blame.

This is normal. It is part of the process of understanding and learning, and makes life more fun. Enjoy.

If you want a kernel that always works and you never need to worry about, I don't recommend building a custom one.

However, with experience and a nicely tuned config, your custom kernel can be generally stable and low maintenance, and provide a sense of satisfaction with each build and boot of your system.

## Usage

    **buildkern:** get a Linux kernel and build it for Debian-based Linux systems.

    This script is designed to make keeping kernels up to date on Debian as
    simple as other activities on a modern Linux system.

    Given the parameter -b or --build, buildkern simply looks for a properly configured kernel 
    config in the current directory and initiates a 'bindeb-pkg' build for Debian.
    This will produce .deb packages you can easily install with 'dpkg -i'.

    With most parameters, buildkern will instead download a kernel to the SRCDIR 
    folder, extract it, configure it with various prompts, and then build it.

    The default setup is to have sources live at ~/src/linux, with your 
    configuration files for various builds in ~/src/linux/config. This may be changed 
    in the script.

    Parameters:
    -h|--help: show this message.
    -v|--get-version: print the latest release only (works with other params)
    -d|--download: download the latest kernel (stable by default) before building
    -b|--build: build the kernel in the current dir (or the one just grabbed)
    -c|--configure: run 'make menuconfig' to enter kernel configuration before compiling
    -r|--rc: download an RC kernel. Implies -d.
    -s|--stable: download a stable kernel. Implies -d. Default
    --clean: automatically clean all sources and installed kernels of yours, keeping the latest two
    --torvalds: get directly from Linus Torvalds' repo (sometimes needed for newest releases)
    --version: download and build a specific version (6.x branch only right now)
    --march=[native|arch]: build an optimized kernel specifically for the specified 
                            CPU. Defaults to 'native' (current CPU) if none specified.
                            Note: this script is not recommended for cross-compiling, as
                            significant additional setup is necessary.
    -o|--optimized: shortcut for "-dbs --march=native", an optimized kernel for the current machine (ONLY).
    -y|--yes: say yes to all prompts immediately. Good for non-interactive/scripting and/or laziness.
    --dry-run: don't actually do anything, just show what would be done.
