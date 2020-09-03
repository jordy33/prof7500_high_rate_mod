### PROF7500 patch file

This files patch the stv0900_core.c

```
Substitute the file. The file is already patched

My patch file is also in the patch directory

If you want to patch a file just go to the patch folder and copy the patch file to:
/linux/drivers/media/dvb-frontends
patch < stv0900_core.c.patch
It will replace the original file with the patches
```

Cross compiling RASPBIAN kernel with default configuration on Ubuntu 16.04 and flashing on Raspberry Pi 3.

```
dpkg — print-architecture
```

Library & Essentials required.
```
sudo apt-get install git
sudo apt-get install libncurses-dev
```

If its 64-bit machine :
```
sudo apt-get install lib32z1 lib32ncurses5 lib32stdc++6 lib32z1 lib32z1-dev
```

Fetch latest Kernel of Raspbian from git.
```
cd ~
git clone https://github.com/raspberrypi/linux
```
Apply patch
```
cd ~/linux/drivers/media/dvb-frontends
patch < stv0900_core.c.patch
```

Look for system architecture and set the tool chain.
```
export PATH=tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/:$PATH
```

Tool chain check.
```
cd linux
ls arm*
```
you should see arm-linux libraries

Start Compiling.
set default config for raspberry pi 3.
```
make ARCH=arm bcm2709_defconfig
```

To set your own configuration use menuconfig, i keep default config.
```
make ARCH=arm menuconfig
```

Check processors in build system.
```
nproc
```

which gives you number of processors it support and we will be using it coming stages.
Replace -j4 matching your processors in your cloud computer.

Compile.
zImage:
```
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j4 zImage
```
dtbs:
```
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j4 dtbs
```
modules:
```
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j4 modules
```
Note: The above steps will take hours to get complete, and -j4 is from nproc.
10. Install modules:
Create a modules folder in same folder where linux and tools present.
```
mkdir ../modules
sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=../modules modules_install
```
Rename zImage and copy to rasberry pi /tmp folder.
```
cd ~/linux
mv arch/arm/boot/zImage arch/arm/boot/kernel7.img
```
Compresing the Modules that need to be tar and copied to raspberry pi 
```
cd ~/modules/lib/modules
tar -czvf modules.tar.gz ./5.4.58-v7+
```

Create a directory kernel in the /home/pi folder
```
mkdir ~/kernel
```
Move kernel7.img and modules.tar.gz from the cloud machine to the raspberry in the kernel directory

Copy the kernel to the raspberry pi
```
cd /kernel
sudo cp ./kernel.img /boot/kernel7.img
```
Decompress the kernel modules in the raspberry pi
```
sudo cp ./modules.tgz /lib/modules/modules.tgz
cd /lib/modules
sudo tar -zxvf ./modules.tgz 
```
Reboot Raspberry pi 3
```
sudo shutdown -r now
```
once reboot check
```
uname -r
```


