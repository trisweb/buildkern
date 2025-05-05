# buildkern
Lightweight Debian-based custom kernel downloader, builder, and manager

## Overview

**buildkern** makes downloading, compiling, and managing custom kernels for your Debian-based Linux computer as easy as other common every-day maintenance tasks.

This Debian Kernel build script makes compiling your own kernels and keeping up to date with latest kernels very easy. It works with the apt package manager and the build-in kernel compiler debian packaging pipeline to enable you to treat your custom kernels as a real system package, just like your distro would.

I recommend you start by creating your own kernel config. This is the only part I haven't create a guided system for, but it's good to learn in any case. To do this, run 'buildkern -d' to download the latest kernel version, then cd into the source folder, and run "make menuconfig". You can also start with the config file from your distro's kernel, which will be large and have a lot that you will not need, but should be safe and more guaranteed to work. I recommend you start your own config.

Here's a good guide: https://how-to.fandom.com/wiki/How_to_configure_the_Linux_kernel

For normal every day kernel compilation once you have a config, you can just run 'buildkern -o'. This will download, configure, and build a kernel optimized for exactly your current system (and only your current system). Note: it uses the default optimization level and compile flags, as the kernel is sensitive to compiler flags and there's near zero benefit. If you would like to use optimization flags, such as -O3, these can be configured in your kernel config.

If you just want to check if a new kernel version is available, you can run 'buildkern -v'

Run buildkern with no arguments for full usage.

*Note: This script is not designed for building generic or portable kernels. It builds kernels for your current machine. Generally speaking, this will mean a faster, more appropriate kernel for your hardware that's streamlined and compiled for exactly what your CPU likes. It can result in a 3-5% speedup from a stock generic kernel, which is generally speaking not worth the trouble, but if it's easy enough, why not?*

## Before you Begin

buildkern handles most of the process of downloading, compiling, installing, and cleaning your custom kernels, however it still leaves some of the process to you, primarily your kernel config. See above for how to generate one.

Before you start, you should also edit the `buildkern` script file, with two key configuration params near the top: the folder you want your sources in (will be created, defaults to `$HOME/src/linux`), and where you want to store your config backups (defaults to `/config` in your source folder). You should also enter your kernel LOCALVERSION (the name of your custom kernel, usually a suffix) here, so that `--clean` can restrict itself to deleting only your custom kernels.

## Installing

Copy the `buildkern` executable script to a directory in your $PATH. `$HOME/bin` if you use that, or `/usr/local/bin` works well.

## Usage

    **buildkern:** get a Linux kernel and build it for Debian.

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
