# RockPi E
RockPi E Eng. sample - Blind testing

* u-boot
* Ubuntu 20.04 LTS

## U-boot

Bootloader based on Android.
* Burn SDCARD

      cd u-boot
      sudo dd if=./idbloader.img of=/dev/sdX seek=64 conv=notrunc
      sync
      sudo dd if=./trust.img of=/dev/sdX seek=24576 conv=notrunc
      sync
      sudo dd if=./uboot.img of=/dev/sdX seek=16384 conv=notrunc
      sync

where X is the device letter (b,c...)
