# Non-interactive ssh password auth package for OpenWRT Legacy

Cross compile the sshpass for OpenWRT MIPS platform.

## Target OpenWRT Versions

* If you are running OpenWRT 14.07, please use the master branch.

## How to Compile Shared Library

OpenWRT compiler is required.

1. Download SDK, for example https://archive.openwrt.org/barrier_breaker/14.07/ramips/mt7620n/OpenWrt-SDK-ramips-for-linux-x86_64-gcc-4.8-linaro_uClibc-0.9.33.2.tar.bz2  
2. Extract the SDK  
3. Download sshpass 1.06 from https://sourceforge.net/projects/sshpass/files/sshpass/  
4. Extract sshpass and put it into SDK/package  
5. Rename it to `src`, now you have `SDK/package/sshpass/src`
6. Clone this repo
7. Copy the Makefile from this repo to `SDK/package/sshpass`
8. Install dependencies by  

    ```bash
    sudo apt install libncurses-dev zlib1g-dev gawk subversion python build-essential ccache
    ```
9. Comment `SDK/scripts/feeds`

    ```bash
    # comment the line 25, like this below
    #$mkver[2] >= "3.81" or $valid_mk = 0;
    ```

10. Compile by  

    ```bash
    make package/sshpass/compile V=s
    ```

11. It will take a long time to build  
12. You can find compiled ipk files in `SDK/bin`  

## Install the Compiled Shared Library to Target Device

1. Copy compiled ipk file to the target device by scp  
2. Install ipk files by  

    ```bash
    opkg install sshpass_1.06_ramips_24kec.ipk
    ```

## Sample Usage

`sshpass -p TARGET_PASSWORD ssh -p TARGET_PORT -o StrictHostKeyChecking=no TARGET_USERNAME@TARGET_ADDRESS "TARGET shell commands"`

1. Check target uptime

    ```bash
    sshpass -p TARGET_PASSWORD admin@192.168.1.50 "uptime"
    ```

2. Read target file

    ```bash
    sshpass -p TARGET_PASSWORD ssh admin@192.168.1.50 "cat /etc/ntp.conf"
    ```

## Potential Error and Fix

```bash
  # usr/lib/libc.so: undefined reference to '_dl_app_init_array'
  #
  # Edit Makefile:
  # from TARGET_LDFLAGS:=-L$(STAGING_DIR)/usr/lib -L$(STAGING_DIR)/lib
  # to   TARGET_LDFLAGS+= -L$(TOOLCHAIN_DIR)/usr/lib -L$(TOOLCHAIN_DIR)/lib -Wl,-rpath=$(TOOLCHAIN_DIR)/lib
```

## Tested Hardware Platform

* OpenWRT 14.07 based industrial 4G router

## Acknowledgments

* https://www.haiyun.me/category/openwrt/2/
