# Sato image for rbpi 3 
```
//git clone -b kirkstone git://git.yoctoproject.org/poky
git clone -b kirkstone https://github.com/agherzan/meta-raspberrypi.git
git clone -b kirkstone https://github.com/openembedded/meta-openembedded.git
```


## 1 - add new build env
```
source oe-init-build-env rbpi/
```

## 2 - local.conf
- specify same caches and downloads 
```
SOURCES = "/home/nahass/source/poky/build"
SSTATE_DIR ?= "${SOURCES}/sstate-cache"
DL_DIR ?= "${SOURCES}/downloads"
TMPDIR = "${SOURCES}/tmp"
MACHINE ??= "raspberrypi3-64" 
```

- add sysd and dropbear
```

DISTRO_FEATURES:append = " systemd"
VIRTUAL-RUNTIME_init_manager = "systemd"
VIRTUAL-RUNTIME_initscripts = ""

IMAGE_INSTALL:append = " dropbear systemd systemd-serialgetty"
```
- produce sd.img + enable uart
```

IMAGE_FSTYPES = "tar.xz ext3 rpi-sdimg" 

ENABLE_UART = "1"

```

## 3- build
```
bitbake --runall=fetch core-image-sato
sudo init 3
source oe-init-build-env rbpi/
bitbake -k image-core-sato
```


# 4- flash the image
- format th sdcard
```
sudo dd if=/dev/zero of=/dev/mmcblk0 bs=4M
```

- Write the Image to the SD Card:
```
sudo dd if=/home/nahass/source/poky/build/tmp/deploy/images/raspberrypi3-64/core-image-sato-raspberrypi3-64.rpi-sdimg of=/dev/mmcblk0 bs=1M
```

## 5- connect through uart
```
sudo picocom -b 115200 /dev/ttyUSB0
```

## 6- add static ip and connect to rbpi
```
passwd
ip addr add 192.168.1.51/24 dev eth0
```
- from your machine
```
sudo ssh root@192.168.1.51
```
