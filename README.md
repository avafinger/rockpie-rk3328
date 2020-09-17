# RockPi E
RockPi E Eng. sample - model D8W2

This model has a micro HDMI for debugging purpose, but would be nice to have it in the final product or another version.

Releases:

* [v1.0 - Kernel 5.9.0 RC2](#installing-ubuntu-2004-lts-on-emmc-instructions)
* [v1.2 - Kernel 5.9.0 RC5](#kernel-590-rc5)
* [u-boot - mainline](#u-boot)
* Ubuntu 20.04 LTS

## U-boot

Uboot Build instructions.

Assuming you want to build mainline u-boot on the **RockPi E** instead of CROSS COMPILING it:

* Install dependencies

                sudo apt-get install device-tree-compiler python3-dev python3-distutils swig


* Build

		mkdir u-boot
		cd u-boot/
		git clone https://github.com/ARM-software/arm-trusted-firmware 
		cd arm-trusted-firmware/
		make PLAT=rk3328 DEBUG=0 bl31
		cd ..
		export BL31=$PWD/arm-trusted-firmware/build/rk3328/release/bl31/bl31.elf
		git clone https://gitlab.denx.de/u-boot/u-boot.git
		make -C u-boot distclean
		make -C u-boot rock-pi-e-rk3328_defconfig
		make -C u-boot 

* Install u-boot (eMMC)

		sudo dd if=u-boot/idbloader.img of=/dev/mmcblk1 seek=64
		sudo dd if=u-boot/u-boot.itb of=/dev/mmcblk1 seek=16384
		sync
		sudo reboot

* Pre-built

		https://github.com/avafinger/rockpie-rk3328/releases/tag/v1.0.1


## Kernel 5.9.0-rc1

Testing mainline **kernel 5.9.0-rc1**, current status:

* Eth0 - ok
* Eth1 - ok
* Wifi (5 GHz) - ok
* BT - ok
* eMMC - ok
* hdmi - ok (for debugging)
* sound - JACK output and HDMI output (ok)
* DVFS - ok
* CPU thermal trhottling at 85ºC (a bit late and slow)
* USB2 - ok
* USB3 (?)

## Benchmarks

* 7z b

		ubuntu@rockpie:~$ 7z b
		
		7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
		p7zip Version 16.02 (locale=C.UTF-8,Utf16=on,HugeFiles=on,64 bits,4 CPUs LE)
		
		LE
		CPU Freq: - - - - - 256000000 - - -
		
		RAM size:     981 MB,  # CPU hardware threads:   4
		RAM usage:    882 MB,  # Benchmark threads:      4
		
		                       Compressing  |                  Decompressing
		Dict     Speed Usage    R/U Rating  |      Speed Usage    R/U Rating
		         KiB/s     %   MIPS   MIPS  |      KiB/s     %   MIPS   MIPS
		
		22:       2021   362    543   1966  |      60186   394   1302   5135
		23:       1934   371    531   1971  |      58505   394   1285   5062
		24:       1851   377    528   1990  |      56935   394   1267   4998
		25:       1624   362    513   1855  |      54457   395   1228   4847
		----------------------------------  | ------------------------------
		Avr:             368    529   1945  |              394   1271   5010
		Tot:             381    900   3478


* openssl (crypto)

		ubuntu@rockpie:~$ openssl speed -elapsed -evp aes-128-gcm aes-128-cbc sha256
		You have chosen to measure elapsed time instead of user CPU time.
		Doing sha256 for 3s on 16 size blocks: 9150967 sha256's in 3.00s
		Doing sha256 for 3s on 64 size blocks: 7027438 sha256's in 3.00s
		Doing sha256 for 3s on 256 size blocks: 4207227 sha256's in 3.00s
		Doing sha256 for 3s on 1024 size blocks: 1606661 sha256's in 3.00s
		Doing sha256 for 3s on 8192 size blocks: 238675 sha256's in 3.00s
		Doing sha256 for 3s on 16384 size blocks: 120888 sha256's in 3.00s
		Doing aes-128 cbc for 3s on 16 size blocks: 10258819 aes-128 cbc's in 3.00s
		Doing aes-128 cbc for 3s on 64 size blocks: 2789280 aes-128 cbc's in 3.00s
		Doing aes-128 cbc for 3s on 256 size blocks: 717682 aes-128 cbc's in 3.00s
		Doing aes-128 cbc for 3s on 1024 size blocks: 180492 aes-128 cbc's in 3.00s
		Doing aes-128 cbc for 3s on 8192 size blocks: 22633 aes-128 cbc's in 3.00s
		Doing aes-128 cbc for 3s on 16384 size blocks: 11317 aes-128 cbc's in 3.00s
		Doing aes-128-gcm for 3s on 16 size blocks: 15026576 aes-128-gcm's in 3.00s
		Doing aes-128-gcm for 3s on 64 size blocks: 10950150 aes-128-gcm's in 3.00s
		Doing aes-128-gcm for 3s on 256 size blocks: 5233470 aes-128-gcm's in 3.00s
		Doing aes-128-gcm for 3s on 1024 size blocks: 1714034 aes-128-gcm's in 3.00s
		Doing aes-128-gcm for 3s on 8192 size blocks: 233980 aes-128-gcm's in 3.00s
		Doing aes-128-gcm for 3s on 16384 size blocks: 117457 aes-128-gcm's in 3.00s
		OpenSSL 1.1.1f  31 Mar 2020
		built on: Mon Apr 20 11:53:50 2020 UTC
		options:bn(64,64) rc4(char) des(int) aes(partial) blowfish(ptr) 
		compiler: gcc -fPIC -pthread -Wa,--noexecstack -Wall -Wa,--noexecstack -g -O2 -fdebug-prefix-map=/build/openssl-9j6sUa/openssl-1.1.1f=. -fstack-protector-strong -Wformat -Werror=format-security -DOPENSSL_TLS_SECURITY_LEVEL=2 -DOPENSSL_USE_NODELETE -DOPENSSL_PIC -DOPENSSL_CPUID_OBJ -DOPENSSL_BN_ASM_MONT -DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DKECCAK1600_ASM -DVPAES_ASM -DECP_NISTZ256_ASM -DPOLY1305_ASM -DNDEBUG -Wdate-time -D_FORTIFY_SOURCE=2
		The 'numbers' are in 1000s of bytes per second processed.
		type             16 bytes     64 bytes    256 bytes   1024 bytes   8192 bytes  16384 bytes
		aes-128 cbc      54713.70k    59504.64k    61242.20k    61607.94k    61803.18k    61805.91k
		aes-128-gcm      80141.74k   233603.20k   446589.44k   585056.94k   638921.39k   641471.83k
		sha256           48805.16k   149918.68k   359016.70k   548406.95k   651741.87k   660209.66k


## Bluetooth

		ubuntu@rockpie:~$ sudo hcitool dev
		Devices:
			hci0	7C:A7:B0:21:21:35
		ubuntu@rockpie:~$ 
		hci0:	Type: Primary  Bus: USB
			BD Address: 7C:A7:B0:21:21:35  ACL MTU: 1021:8  SCO MTU: 255:12
			UP RUNNING 
			RX bytes:2002 acl:0 sco:0 events:177 errors:0
			TX bytes:24878 acl:0 sco:0 commands:177 errors:0
			Features: 0xff 0xff 0xff 0xfe 0xdb 0xfd 0x7b 0x87
			Packet type: DM1 DM3 DM5 DH1 DH3 DH5 HV1 HV2 HV3 
			Link policy: RSWITCH HOLD SNIFF PARK 
			Link mode: SLAVE ACCEPT 
			Name: 'rockpie'
			Class: 0x000000
			Service Classes: Unspecified
			Device Class: Miscellaneous, 
			HCI Version: 4.2 (0x8)  Revision: 0x826c
			LMP Version: 4.2 (0x8)  Subversion: 0xa99e
			Manufacturer: Realtek Semiconductor Corporation (93)

## Kernel 5.9.0-rc5

Kernel 5.9.0-rc2 is rock solid in RockPi E, building **Kernel 5.9.0-rc5** with gcc-10 on eMMC for testing new devfreq and checking stability.
As usual, stressing the board using Wifi and lots of R/W operations in eMMC requires a real-time monitoring tool, especially if Cpu Throttling Temperature isn't working nicely.

**WiP**

CPU Temp on the build process:

![Kernel build](https://github.com/avafinger/rockpie-rk3328/raw/master/cuild.png)


CPU Temp linmits:

![Limits](https://github.com/avafinger/rockpie-rk3328/raw/master/kernel.png)


Kernel 5.9.0-rc5 stability test passed (ok):

![Kernel 5.9.0-rc5](https://github.com/avafinger/rockpie-rk3328/raw/master/kernel-5.9.0-rc5.png)


Linux-image for testing:

https://github.com/avafinger/rockpie-rk3328/releases/tag/v1.2



## Bluetooth for audio streaming with RockPi E

Realtek rtl8821cu with BT 4.2 are capable of working consistently, streaming video while using the Wifi.
Streaming audio from a Phone to the RockpiE works really nice. In the example we are going to make RockpiE output the sound to analog jack.

* Install Bluetooth

		apt-get install bluez bluez-tools bluetooth pulseaudio-module-bluetooth

* Find our phone

		hcitool scan
		Scanning ...
			00:17:CA:F7:38:18	Haier HW-W910


* Make our RockPi E visible to our Phone

		ubuntu@rockpie:~$ bluetoothctl discoverable on
		Changing discoverable on succeeded
		ubuntu@rockpie:~$ hciconfig -a
		hci0:	Type: Primary  Bus: USB
			BD Address: 7C:A7:B0:21:21:35  ACL MTU: 1021:8  SCO MTU: 255:12
			UP RUNNING PSCAN ISCAN 
			RX bytes:311518993 acl:532158 sco:0 events:549 errors:0
			TX bytes:16671 acl:412 sco:0 commands:94 errors:0
			Features: 0xff 0xff 0xff 0xfe 0xdb 0xfd 0x7b 0x87
			Packet type: DM1 DM3 DM5 DH1 DH3 DH5 HV1 HV2 HV3 
			Link policy: RSWITCH HOLD SNIFF PARK 
			Link mode: SLAVE ACCEPT 
			Name: 'rockpie'
			Class: 0x0c0000
			Service Classes: Rendering, Capturing
			Device Class: Miscellaneous, 
			HCI Version: 4.2 (0x8)  Revision: 0x826c
			LMP Version: 4.2 (0x8)  Subversion: 0xa99e
			Manufacturer: Realtek Semiconductor Corporation (93)

* Make sure our RockPi E is running pulse and has audio sink

		ubuntu@rockpie:~$ pactl info
		Server String: /run/user/1000/pulse/native
		Library Protocol Version: 33
		Server Protocol Version: 33
		Is Local: yes
		Client Index: 6
		Tile Size: 65472
		User Name: ubuntu
		Host Name: rockpie
		Server Name: pulseaudio
		Server Version: 13.99.1
		Default Sample Specification: s16le 2ch 44100Hz
		Default Channel Map: front-left,front-right
		Default Sink: alsa_output.default
		Default Source: bluez_source.00_17_CA_F7_38_18.a2dp_source
		Cookie: fb4a:9eb4

* From the Phone, scan and find RockPi E

* pair as usual and connect

		ubuntu@rockpie:~$ bluetoothctl
		Agent registered
		[CHG] Controller 7C:A7:B0:21:21:35 Pairable: yes
		[bluetooth]# discoverable on
		Changing discoverable on succeeded
		[CHG] Controller 7C:A7:B0:21:21:35 Discoverable: yes
		[bluetooth]# pairable on
		Changing pairable on succeeded
		[bluetooth]# agent on
		Agent is already registered
		[bluetooth]# default-agent
		Default agent request successful
		[bluetooth]# advertise on
		[CHG] Controller 7C:A7:B0:21:21:35 SupportedInstances: 0x04
		[CHG] Controller 7C:A7:B0:21:21:35 ActiveInstances: 0x01
		Advertising object registered
		Tx Power: off
		Name: off
		Apperance: off
		Discoverable: off
		[bluetooth]# discoverable on
		Changing discoverable on succeeded
		[CHG] Controller 7C:A7:B0:21:21:35 Discoverable: yes
		[bluetooth]# advertise on
		Advertisement is already registered
		[bluetooth]# discoverable on
		Changing discoverable on succeeded
		[bluetooth]# agent on
		Agent is already registered
		[bluetooth]# default-agent
		Default agent request successful
		[CHG] Controller 7C:A7:B0:21:21:35 Discoverable: no
		[bluetooth]# default-agent
		Default agent request successful
		[bluetooth]# discoverable on
		Changing discoverable on succeeded
		[CHG] Controller 7C:A7:B0:21:21:35 Discoverable: yes
		[CHG] Controller 7C:A7:B0:21:21:35 Discoverable: no
		[NEW] Device 00:17:CA:F7:38:18 Haier HW-W910
		Request confirmation
		[agent] Confirm passkey 756666 (yes/no): yes
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001108-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 Modalias: usb:v000Ap0000d0000
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001105-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001108-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000110a-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000110c-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001112-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001116-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000111f-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000112f-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001132-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001200-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001801-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 ServicesResolved: yes
		[CHG] Device 00:17:CA:F7:38:18 Paired: yes
		Authorize service
		[agent] Authorize service 00001108-0000-1000-8000-00805f9b34fb (yes/no): yes
		Authorize service
		[agent] Authorize service 0000110d-0000-1000-8000-00805f9b34fb (yes/no): yes
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001105-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001108-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000110a-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000110c-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000110d-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001112-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001116-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000111f-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000112f-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001132-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001200-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001801-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001105-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001108-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000110a-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000110c-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000110d-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000110e-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001112-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001116-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000111f-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 0000112f-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001132-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001200-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
		[CHG] Device 00:17:CA:F7:38:18 UUIDs: 00001801-0000-1000-8000-00805f9b34fb
		Authorize service
		[agent] Authorize service 0000110e-0000-1000-8000-00805f9b34fb (yes/no): yes
		[Haier HW-W910]# info
		Device 00:17:CA:F7:38:18 (public)
			Name: Haier HW-W910
			Alias: Haier HW-W910
			Class: 0x005a020c
			Icon: phone
			Paired: yes
			Trusted: no
			Blocked: no
			Connected: yes
			LegacyPairing: no
			UUID: OBEX Object Push          (00001105-0000-1000-8000-00805f9b34fb)
			UUID: Headset                   (00001108-0000-1000-8000-00805f9b34fb)
			UUID: Audio Source              (0000110a-0000-1000-8000-00805f9b34fb)
			UUID: A/V Remote Control Target (0000110c-0000-1000-8000-00805f9b34fb)
			UUID: Advanced Audio Distribu.. (0000110d-0000-1000-8000-00805f9b34fb)
			UUID: A/V Remote Control        (0000110e-0000-1000-8000-00805f9b34fb)
			UUID: Headset AG                (00001112-0000-1000-8000-00805f9b34fb)
			UUID: NAP                       (00001116-0000-1000-8000-00805f9b34fb)
			UUID: Handsfree Audio Gateway   (0000111f-0000-1000-8000-00805f9b34fb)
			UUID: Phonebook Access Server   (0000112f-0000-1000-8000-00805f9b34fb)
			UUID: Message Access Server     (00001132-0000-1000-8000-00805f9b34fb)
			UUID: PnP Information           (00001200-0000-1000-8000-00805f9b34fb)
			UUID: Generic Access Profile    (00001800-0000-1000-8000-00805f9b34fb)
			UUID: Generic Attribute Profile (00001801-0000-1000-8000-00805f9b34fb)
			Modalias: usb:v000Ap0000d0000


* Now play a song on the phone and listen to it on RockPiE (Bluetooth).

Enjoy the Music

## Networking (Wifi / Eth0 / Eth1)

		ubuntu@rockpie:~$ 
		ubuntu@rockpie:~$ iw wlx7ca7b0212134 info
		Interface wlx7ca7b0212134
			ifindex 4
			wdev 0x1
			addr 7c:a7:b0:21:21:34
			type managed
			wiphy 0
			txpower 12.00 dBm
		ubuntu@rockpie:~$ sudo iw dev wlx7ca7b0212134 scan | egrep "signal:|SSID:" | sed -e "s/\tsignal: //" -e "s/\tSSID: //" | awk '{ORS = (NR % 2 == 0)? "\n" : " "; print}' | sort
		-65.00 dBm FIB-8784-5G
		-66.00 dBm FIB-8784
		-82.00 dBm foxy
		-92.00 dBm FIB-8784
		ubuntu@rockpie:~$ 

**Wifi**
![Eth0](https://github.com/avafinger/rockpie-rk3328/raw/master/wlan.png)


**1.5 Ghz**
![RockPiE Limits](https://github.com/avafinger/rockpie-rk3328/raw/master/limits.png)

## eMMC

eMMC is configured and works fine.

![eMMC](https://github.com/avafinger/rockpie-rk3328/raw/master/emmc.png)

## Kernel 5.9.0-rc2

Mainline kernel 5.9.0-rc2 was built with gcc 9.3 onboard to pass the stability test.

![Htop](https://github.com/avafinger/rockpie-rk3328/raw/master/kernel_5.9.0-rc2.png)

* Sound (analog and hdmi)

**JACK**

	ubuntu@rockpie:~$ play SoundHelix-Song-1.mp3 

	SoundHelix-Song-1.mp3:

	 File Size: 8.95M     Bit Rate: 192k
	  Encoding: MPEG audio    Info: 2009
	  Channels: 2 @ 16-bit   
	Samplerate: 44100Hz      
	Replaygain: off         Artist: SoundHelix
	  Duration: 00:06:12.73  

	In:6.50% 00:00:24.24 [00:05:48.48] Out:1.07M [======|======] Hd:0.5 Clip:0    
	Aborted.

**HDMI**

	ubuntu@rockpie:~$ AUDIODEV=hw:0,0 play SoundHelix-Song-1.mp3 
	play WARN alsa: can't encode 0-bit Unknown or not applicable

	SoundHelix-Song-1.mp3:

	 File Size: 8.95M     Bit Rate: 192k
	  Encoding: MPEG audio    Info: 2009
	  Channels: 2 @ 16-bit   
	Samplerate: 44100Hz      
	Replaygain: off         Artist: SoundHelix
	  Duration: 00:06:12.73  

	In:3.91% 00:00:14.58 [00:05:58.14] Out:643k  [  -===|==    ] Hd:0.8 Clip:0    
	Aborted.



## Boot log (booting from eMMC)

	[    0.000000] Booting Linux on physical CPU 0x0000000000 [0x410fd034]
	[    0.000000] Linux version 5.9.0-rc5 (ubuntu@rockpie) (gcc (Ubuntu 10-20200411-0ubuntu1) 10.0.1 20200411 (experimental) [master revision bb87d5cc77d:75961caccb7:f883c46b4877f637e0fa5025b4d6b5c9040ec566], GNU ld (GNU Binutils for Ubuntu) 2.34) #1 SMP PREEMPT Tue Sep 15 22:31:13 UTC 2020
	[    0.000000] Machine model: Radxa RockPiE Eng
	[    0.000000] efi: UEFI not found.
	[    0.000000] cma: Reserved 16 MiB at 0x000000003f000000
	[    0.000000] Zone ranges:
	[    0.000000]   DMA      [mem 0x0000000000200000-0x000000003fffffff]
	[    0.000000]   DMA32    empty
	[    0.000000]   Normal   empty
	[    0.000000] Movable zone start for each node
	[    0.000000] Early memory node ranges
	[    0.000000]   node   0: [mem 0x0000000000200000-0x000000003fffffff]
	[    0.000000] Initmem setup node 0 [mem 0x0000000000200000-0x000000003fffffff]
	[    0.000000] On node 0 totalpages: 261632
	[    0.000000]   DMA zone: 4088 pages used for memmap
	[    0.000000]   DMA zone: 0 pages reserved
	[    0.000000]   DMA zone: 261632 pages, LIFO batch:63
	[    0.000000] psci: probing for conduit method from DT.
	[    0.000000] psci: PSCIv1.1 detected in firmware.
	[    0.000000] psci: Using standard PSCI v0.2 function IDs
	[    0.000000] psci: MIGRATE_INFO_TYPE not supported.
	[    0.000000] psci: SMC Calling Convention v1.2
	[    0.000000] percpu: Embedded 32 pages/cpu s91112 r8192 d31768 u131072
	[    0.000000] pcpu-alloc: s91112 r8192 d31768 u131072 alloc=32*4096
	[    0.000000] pcpu-alloc: [0] 0 [0] 1 [0] 2 [0] 3 
	[    0.000000] Detected VIPT I-cache on CPU0
	[    0.000000] CPU features: detected: ARM erratum 845719
	[    0.000000] Speculative Store Bypass Disable mitigation not required
	[    0.000000] Built 1 zonelists, mobility grouping on.  Total pages: 257544
	[    0.000000] Kernel command line: earlyprintk root=/dev/mmcblk1p2 rw rootfstype=ext4 rootwait fsck.repair=yes panic=10 no_console_suspend consoleblank=0
	[    0.000000] Dentry cache hash table entries: 131072 (order: 8, 1048576 bytes, linear)
	[    0.000000] Inode-cache hash table entries: 65536 (order: 7, 524288 bytes, linear)
	[    0.000000] mem auto-init: stack:off, heap alloc:off, heap free:off
	[    0.000000] Memory: 981672K/1046528K available (13756K kernel code, 1800K rwdata, 4596K rodata, 5568K init, 804K bss, 48472K reserved, 16384K cma-reserved)
	[    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=4, Nodes=1
	[    0.000000] ftrace: allocating 43756 entries in 171 pages
	[    0.000000] ftrace: allocated 171 pages with 5 groups
	[    0.000000] rcu: Preemptible hierarchical RCU implementation.
	[    0.000000] rcu: 	RCU restricting CPUs from NR_CPUS=8 to nr_cpu_ids=4.
	[    0.000000] 	Trampoline variant of Tasks RCU enabled.
	[    0.000000] 	Rude variant of Tasks RCU enabled.
	[    0.000000] rcu: RCU calculated value of scheduler-enlistment delay is 25 jiffies.
	[    0.000000] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=4
	[    0.000000] NR_IRQS: 64, nr_irqs: 64, preallocated irqs: 0
	[    0.000000] GIC: Using split EOI/Deactivate mode
	[    0.000000] random: get_random_bytes called from start_kernel+0x384/0x548 with crng_init=0
	[    0.000000] arch_timer: cp15 timer(s) running at 24.00MHz (phys).
	[    0.000000] clocksource: arch_sys_counter: mask: 0xffffffffffffff max_cycles: 0x588fe9dc0, max_idle_ns: 440795202592 ns
	[    0.000007] sched_clock: 56 bits at 24MHz, resolution 41ns, wraps every 4398046511097ns
	[    0.000697] Console: colour dummy device 80x25
	[    0.001385] printk: console [tty0] enabled
	[    0.001455] Calibrating delay loop (skipped), value calculated using timer frequency.. 48.00 BogoMIPS (lpj=96000)
	[    0.001495] pid_max: default: 32768 minimum: 301
	[    0.001695] LSM: Security Framework initializing
	[    0.001817] Mount-cache hash table entries: 2048 (order: 2, 16384 bytes, linear)
	[    0.001860] Mountpoint-cache hash table entries: 2048 (order: 2, 16384 bytes, linear)
	[    0.004838] rcu: Hierarchical SRCU implementation.
	[    0.006392] EFI services will not be available.
	[    0.007288] smp: Bringing up secondary CPUs ...
	[    0.008431] Detected VIPT I-cache on CPU1
	[    0.008524] CPU1: Booted secondary processor 0x0000000001 [0x410fd034]
	[    0.009732] Detected VIPT I-cache on CPU2
	[    0.009813] CPU2: Booted secondary processor 0x0000000002 [0x410fd034]
	[    0.010976] Detected VIPT I-cache on CPU3
	[    0.011058] CPU3: Booted secondary processor 0x0000000003 [0x410fd034]
	[    0.011262] smp: Brought up 1 node, 4 CPUs
	[    0.011386] SMP: Total of 4 processors activated.
	[    0.011411] CPU features: detected: 32-bit EL0 Support
	[    0.011436] CPU features: detected: CRC32 instructions
	[    0.036020] CPU: All CPU(s) started at EL2
	[    0.036094] alternatives: patching kernel code
	[    0.038107] devtmpfs: initialized
	[    0.053618] Registered cp15_barrier emulation handler
	[    0.053680] Registered setend emulation handler
	[    0.054476] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645041785100000 ns
	[    0.054538] futex hash table entries: 1024 (order: 4, 65536 bytes, linear)
	[    0.055833] pinctrl core: initialized pinctrl subsystem
	[    0.057107] DMI not present or invalid.
	[    0.057922] NET: Registered protocol family 16
	[    0.061331] DMA: preallocated 128 KiB GFP_KERNEL pool for atomic allocations
	[    0.061611] DMA: preallocated 128 KiB GFP_KERNEL|GFP_DMA pool for atomic allocations
	[    0.061920] DMA: preallocated 128 KiB GFP_KERNEL|GFP_DMA32 pool for atomic allocations
	[    0.062067] audit: initializing netlink subsys (disabled)
	[    0.062443] audit: type=2000 audit(0.060:1): state=initialized audit_enabled=0 res=1
	[    0.063706] thermal_sys: Registered thermal governor 'fair_share'
	[    0.063713] thermal_sys: Registered thermal governor 'step_wise'
	[    0.064313] cpuidle: using governor ladder
	[    0.064393] cpuidle: using governor menu
	[    0.064656] hw-breakpoint: found 6 breakpoint and 4 watchpoint registers.
	[    0.064843] ASID allocator initialised with 65536 entries
	[    0.135816] HugeTLB registered 1.00 GiB page size, pre-allocated 0 pages
	[    0.135862] HugeTLB registered 32.0 MiB page size, pre-allocated 0 pages
	[    0.135889] HugeTLB registered 2.00 MiB page size, pre-allocated 0 pages
	[    0.135915] HugeTLB registered 64.0 KiB page size, pre-allocated 0 pages
	[    0.138624] cryptd: max_cpu_qlen set to 1000
	[    0.150482] iommu: Default domain type: Translated 
	[    0.153334] SCSI subsystem initialized
	[    0.153766] libata version 3.00 loaded.
	[    0.154133] usbcore: registered new interface driver usbfs
	[    0.154231] usbcore: registered new interface driver hub
	[    0.154388] usbcore: registered new device driver usb
	[    0.154520] mc: Linux media interface: v0.10
	[    0.154589] videodev: Linux video capture interface: v2.00
	[    0.154694] pps_core: LinuxPPS API ver. 1 registered
	[    0.154717] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
	[    0.154767] PTP clock support registered
	[    0.156162] Advanced Linux Sound Architecture Driver Initialized.
	[    0.157343] Bluetooth: Core ver 2.22
	[    0.157448] NET: Registered protocol family 31
	[    0.157469] Bluetooth: HCI device and connection manager initialized
	[    0.157510] Bluetooth: HCI socket layer initialized
	[    0.157540] Bluetooth: L2CAP socket layer initialized
	[    0.157590] Bluetooth: SCO socket layer initialized
	[    0.157638] NetLabel: Initializing
	[    0.157658] NetLabel:  domain hash size = 128
	[    0.157677] NetLabel:  protocols = UNLABELED CIPSOv4 CALIPSO
	[    0.157797] NetLabel:  unlabeled traffic allowed by default
	[    0.158749] clocksource: Switched to clocksource arch_sys_counter
	[    1.859692] VFS: Disk quotas dquot_6.6.0
	[    1.859828] VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
	[    1.860109] FS-Cache: Loaded
	[    1.874643] NET: Registered protocol family 2
	[    1.875836] tcp_listen_portaddr_hash hash table entries: 512 (order: 2, 20480 bytes, linear)
	[    1.875924] TCP established hash table entries: 8192 (order: 4, 65536 bytes, linear)
	[    1.876072] TCP bind hash table entries: 8192 (order: 6, 262144 bytes, linear)
	[    1.876404] TCP: Hash tables configured (established 8192 bind 8192)
	[    1.876683] UDP hash table entries: 512 (order: 3, 49152 bytes, linear)
	[    1.876789] UDP-Lite hash table entries: 512 (order: 3, 49152 bytes, linear)
	[    1.877480] NET: Registered protocol family 1
	[    1.878483] RPC: Registered named UNIX socket transport module.
	[    1.878522] RPC: Registered udp transport module.
	[    1.878544] RPC: Registered tcp transport module.
	[    1.878564] RPC: Registered tcp NFSv4.1 backchannel transport module.
	[    1.878602] PCI: CLS 0 bytes, default 64
	[    1.879058] Trying to unpack rootfs image as initramfs...
	[    1.953312] Freeing initrd memory: 1076K
	[    1.954894] hw perfevents: enabled with armv8_cortex_a53 PMU driver, 7 counters available
	[    1.963037] Initialise system trusted keyrings
	[    1.963450] workingset: timestamp_bits=46 max_order=18 bucket_order=0
	[    1.976769] squashfs: version 4.0 (2009/01/31) Phillip Lougher
	[    1.977338] FS-Cache: Netfs 'nfs' registered for caching
	[    1.978288] NFS: Registering the id_resolver key type
	[    1.978363] Key type id_resolver registered
	[    1.978384] Key type id_legacy registered
	[    1.978569] nfs4filelayout_init: NFSv4 File Layout Driver Registering...
	[    1.979169] fuse: init (API version 7.31)
	[    2.051378] Key type asymmetric registered
	[    2.051425] Asymmetric key parser 'x509' registered
	[    2.051535] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 247)
	[    2.051863] io scheduler mq-deadline registered
	[    2.051891] io scheduler kyber registered
	[    2.060939] rockchip-u3phy ff470000.usb3-phy: Rockchip u3phy initialized successfully
	[    2.066530] dma-pl330 ff1f0000.dmac: Loaded driver for PL330 DMAC-241330
	[    2.066581] dma-pl330 ff1f0000.dmac: 	DBUFF-128x8bytes Num_Chans-8 Num_Peri-20 Num_Events-16
	[    2.067715] Serial: 8250/16550 driver, 5 ports, IRQ sharing disabled
	[    2.070632] ff130000.serial: ttyS2 at MMIO 0xff130000 (irq = 14, base_baud = 1500000) is a 16550A
	[    2.147569] printk: console [ttyS2] enabled
	[    2.150718] rockchip-vop ff370000.vop: Adding to iommu group 2
	[    2.158846] rockchip-drm display-subsystem: bound ff370000.vop (ops 0xffffffc010e399b0)
	[    2.173401] brd: module loaded
	[    2.192487] loop: module loaded
	[    2.195927] libphy: Fixed MDIO Bus: probed
	[    2.198205] rk_gmac-dwmac ff540000.ethernet: IRQ eth_wake_irq not found
	[    2.198900] rk_gmac-dwmac ff540000.ethernet: IRQ eth_lpi not found
	[    2.199733] rk_gmac-dwmac ff540000.ethernet: PTP uses main clock
	[    2.200595] rk_gmac-dwmac ff540000.ethernet: clock input or output? (input).
	[    2.201244] rk_gmac-dwmac ff540000.ethernet: TX delay(0x24).
	[    2.201767] rk_gmac-dwmac ff540000.ethernet: RX delay(0x18).
	[    2.202298] rk_gmac-dwmac ff540000.ethernet: integrated PHY? (no).
	[    2.203008] rk_gmac-dwmac ff540000.ethernet: cannot get clock clk_mac_speed
	[    2.203641] rk_gmac-dwmac ff540000.ethernet: clock input from PHY
	[    2.209262] rk_gmac-dwmac ff540000.ethernet: init for RGMII
	[    2.210181] rk_gmac-dwmac ff540000.ethernet: User ID: 0x10, Synopsys ID: 0x35
	[    2.210878] rk_gmac-dwmac ff540000.ethernet: 	DWMAC1000
	[    2.211360] rk_gmac-dwmac ff540000.ethernet: DMA HW capability register supported
	[    2.212041] rk_gmac-dwmac ff540000.ethernet: RX Checksum Offload Engine supported
	[    2.212722] rk_gmac-dwmac ff540000.ethernet: COE Type 2
	[    2.213199] rk_gmac-dwmac ff540000.ethernet: TX Checksum insertion supported
	[    2.213838] rk_gmac-dwmac ff540000.ethernet: Wake-Up On Lan supported
	[    2.214536] rk_gmac-dwmac ff540000.ethernet: Normal descriptors
	[    2.215096] rk_gmac-dwmac ff540000.ethernet: Ring mode enabled
	[    2.215631] rk_gmac-dwmac ff540000.ethernet: Enable RX Mitigation via HW Watchdog Timer
	[    2.216368] rk_gmac-dwmac ff540000.ethernet: device MAC address 06:30:db:98:d5:1b
	[    2.278765] libphy: stmmac: probed
	[    2.284028] rk_gmac-dwmac ff550000.ethernet: IRQ eth_wake_irq not found
	[    2.284654] rk_gmac-dwmac ff550000.ethernet: IRQ eth_lpi not found
	[    2.285402] rk_gmac-dwmac ff550000.ethernet: PTP uses main clock
	[    2.286270] rk_gmac-dwmac ff550000.ethernet: clock input or output? (output).
	[    2.286991] rk_gmac-dwmac ff550000.ethernet: Can not read property: tx_delay.
	[    2.287641] rk_gmac-dwmac ff550000.ethernet: set tx_delay to 0x30
	[    2.288198] rk_gmac-dwmac ff550000.ethernet: Can not read property: rx_delay.
	[    2.288844] rk_gmac-dwmac ff550000.ethernet: set rx_delay to 0x10
	[    2.289422] rk_gmac-dwmac ff550000.ethernet: integrated PHY? (yes).
	[    2.290112] rk_gmac-dwmac ff550000.ethernet: cannot get clock clk_mac_refout
	[    2.290867] rk_gmac-dwmac ff550000.ethernet: cannot get clock clk_mac_speed
	[    2.296562] rk_gmac-dwmac ff550000.ethernet: init for RMII
	[    2.335118] rk_gmac-dwmac ff550000.ethernet: User ID: 0x10, Synopsys ID: 0x35
	[    2.335791] rk_gmac-dwmac ff550000.ethernet: 	DWMAC1000
	[    2.336275] rk_gmac-dwmac ff550000.ethernet: DMA HW capability register supported
	[    2.336954] rk_gmac-dwmac ff550000.ethernet: RX Checksum Offload Engine supported
	[    2.337633] rk_gmac-dwmac ff550000.ethernet: COE Type 2
	[    2.338110] rk_gmac-dwmac ff550000.ethernet: TX Checksum insertion supported
	[    2.338776] rk_gmac-dwmac ff550000.ethernet: Wake-Up On Lan supported
	[    2.339488] rk_gmac-dwmac ff550000.ethernet: Normal descriptors
	[    2.340029] rk_gmac-dwmac ff550000.ethernet: Ring mode enabled
	[    2.340560] rk_gmac-dwmac ff550000.ethernet: Enable RX Mitigation via HW Watchdog Timer
	[    2.343745] libphy: stmmac: probed
	[    2.349109] PPP generic driver version 2.4.2
	[    2.349936] usbcore: registered new interface driver asix
	[    2.350505] usbcore: registered new interface driver ax88179_178a
	[    2.351171] usbcore: registered new interface driver cdc_ether
	[    2.351759] usbcore: registered new interface driver qmi_wwan
	[    2.356647] phy phy-ff470000.usb3-phy.2: u3phy u2 power on
	[    2.357203] phy phy-ff470000.usb3-phy.3: u3phy u3 power on
	[    2.361038] dwc2 ff580000.usb: supply vusb_d not found, using dummy regulator
	[    2.361930] dwc2 ff580000.usb: supply vusb_a not found, using dummy regulator
	[    2.377195] dwc2 ff580000.usb: DWC OTG Controller
	[    2.377685] dwc2 ff580000.usb: new USB bus registered, assigned bus number 1
	[    2.378385] dwc2 ff580000.usb: irq 38, io mem 0xff580000
	[    2.379251] usb usb1: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.09
	[    2.380006] usb usb1: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.380664] usb usb1: Product: DWC OTG Controller
	[    2.381096] usb usb1: Manufacturer: Linux 5.9.0-rc5 dwc2_hsotg
	[    2.381628] usb usb1: SerialNumber: ff580000.usb
	[    2.383089] hub 1-0:1.0: USB hub found
	[    2.383500] hub 1-0:1.0: 1 port detected
	[    2.385025] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
	[    2.385648] ehci-pci: EHCI PCI platform driver
	[    2.386147] ehci-platform: EHCI generic platform driver
	[    2.389475] ehci-platform ff5c0000.usb: EHCI Host Controller
	[    2.390028] ehci-platform ff5c0000.usb: new USB bus registered, assigned bus number 2
	[    2.391067] ehci-platform ff5c0000.usb: irq 39, io mem 0xff5c0000
	[    2.406775] ehci-platform ff5c0000.usb: USB 2.0 started, EHCI 1.00
	[    2.407768] usb usb2: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.09
	[    2.408522] usb usb2: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.409179] usb usb2: Product: EHCI Host Controller
	[    2.409626] usb usb2: Manufacturer: Linux 5.9.0-rc5 ehci_hcd
	[    2.410142] usb usb2: SerialNumber: ff5c0000.usb
	[    2.411541] hub 2-0:1.0: USB hub found
	[    2.411951] hub 2-0:1.0: 1 port detected
	[    2.413252] ohci_hcd: USB 1.1 'Open' Host Controller (OHCI) Driver
	[    2.413862] ohci-platform: OHCI generic platform driver
	[    2.415103] ohci-platform ff5d0000.usb: Generic Platform OHCI controller
	[    2.415744] ohci-platform ff5d0000.usb: new USB bus registered, assigned bus number 3
	[    2.416690] ohci-platform ff5d0000.usb: irq 40, io mem 0xff5d0000
	[    2.479221] usb usb3: New USB device found, idVendor=1d6b, idProduct=0001, bcdDevice= 5.09
	[    2.479979] usb usb3: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.480638] usb usb3: Product: Generic Platform OHCI controller
	[    2.481177] usb usb3: Manufacturer: Linux 5.9.0-rc5 ohci_hcd
	[    2.481695] usb usb3: SerialNumber: ff5d0000.usb
	[    2.483105] hub 3-0:1.0: USB hub found
	[    2.483516] hub 3-0:1.0: 1 port detected
	[    2.485767] xhci-hcd xhci-hcd.0.auto: xHCI Host Controller
	[    2.486312] xhci-hcd xhci-hcd.0.auto: new USB bus registered, assigned bus number 4
	[    2.487349] xhci-hcd xhci-hcd.0.auto: hcc params 0x0220fe64 hci version 0x110 quirks 0x0000000002010010
	[    2.488295] xhci-hcd xhci-hcd.0.auto: irq 174, io mem 0xff600000
	[    2.489565] usb usb4: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.09
	[    2.490320] usb usb4: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.491073] usb usb4: Product: xHCI Host Controller
	[    2.491525] usb usb4: Manufacturer: Linux 5.9.0-rc5 xhci-hcd
	[    2.492041] usb usb4: SerialNumber: xhci-hcd.0.auto
	[    2.493466] hub 4-0:1.0: USB hub found
	[    2.493875] hub 4-0:1.0: 1 port detected
	[    2.494911] xhci-hcd xhci-hcd.0.auto: xHCI Host Controller
	[    2.495436] xhci-hcd xhci-hcd.0.auto: new USB bus registered, assigned bus number 5
	[    2.496149] xhci-hcd xhci-hcd.0.auto: Host supports USB 3.0 SuperSpeed
	[    2.496862] usb usb5: We don't know the algorithms for LPM for this host, disabling LPM.
	[    2.497826] usb usb5: New USB device found, idVendor=1d6b, idProduct=0003, bcdDevice= 5.09
	[    2.498578] usb usb5: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.499314] usb usb5: Product: xHCI Host Controller
	[    2.499764] usb usb5: Manufacturer: Linux 5.9.0-rc5 xhci-hcd
	[    2.500280] usb usb5: SerialNumber: xhci-hcd.0.auto
	[    2.501730] hub 5-0:1.0: USB hub found
	[    2.502142] hub 5-0:1.0: 1 port detected
	[    2.503444] usbcore: registered new interface driver cdc_acm
	[    2.503969] cdc_acm: USB Abstract Control Model driver for USB modems and ISDN adapters
	[    2.504762] usbcore: registered new interface driver cdc_wdm
	[    2.505712] usbcore: registered new interface driver uas
	[    2.506373] usbcore: registered new interface driver usb-storage
	[    2.507143] usbcore: registered new interface driver usbserial_generic
	[    2.507770] usbserial: USB Serial support registered for generic
	[    2.508396] usbcore: registered new interface driver ch341
	[    2.508925] usbserial: USB Serial support registered for ch341-uart
	[    2.509570] usbcore: registered new interface driver cp210x
	[    2.510106] usbserial: USB Serial support registered for cp210x
	[    2.510799] usbcore: registered new interface driver ftdi_sio
	[    2.511353] usbserial: USB Serial support registered for FTDI USB Serial Device
	[    2.512262] usbcore: registered new interface driver option
	[    2.512800] usbserial: USB Serial support registered for GSM modem (1-port)
	[    2.513752] usbcore: registered new interface driver pl2303
	[    2.514289] usbserial: USB Serial support registered for pl2303
	[    2.514973] usbcore: registered new interface driver qcserial
	[    2.515531] usbserial: USB Serial support registered for Qualcomm USB modem
	[    2.516924] mousedev: PS/2 mouse device common for all mice
	[    2.518475] i2c /dev entries driver
	[    2.524450] rk808 1-0018: chip id: 0x8050
	[    2.530984] rk808-regulator rk808-regulator: there is no dvs0 gpio
	[    2.531603] rk808-regulator rk808-regulator: there is no dvs1 gpio
	[    2.532287] DCDC_REG1: supplied by vcc_sys
	[    2.535594] DCDC_REG2: supplied by vcc_sys
	[    2.537860] DCDC_REG3: supplied by vcc_sys
	[    2.538713] DCDC_REG4: supplied by vcc_sys
	[    2.540671] LDO_REG1: supplied by vcc_io
	[    2.544425] LDO_REG2: supplied by vcc_io
	[    2.548130] LDO_REG3: supplied by vcc_sys
	[    2.561691] rk808-rtc rk808-rtc: registered as rtc0
	[    2.563955] rk808-rtc rk808-rtc: setting system clock to 2020-09-16T18:34:43 UTC (1600281283)
	[    2.566940] input: rk805 pwrkey as /devices/platform/ff160000.i2c/i2c-1/1-0018/rk805-pwrkey/input/input0
	[    2.569329] IR NEC protocol handler initialized
	[    2.576787] dw_wdt ff1a0000.watchdog: No valid TOPs array specified
	[    2.578445] device-mapper: uevent: version 1.0.3
	[    2.579471] device-mapper: ioctl: 4.42.0-ioctl (2020-02-27) initialised: dm-devel@redhat.com
	[    2.587612] sdhci: Secure Digital Host Controller Interface driver
	[    2.588196] sdhci: Copyright(c) Pierre Ossman
	[    2.588595] Synopsys Designware Multimedia Card Interface Driver
	[    2.590328] dwmmc_rockchip ff500000.dwmmc: IDMAC supports 32-bit address mode.
	[    2.591129] dwmmc_rockchip ff500000.dwmmc: Using internal DMA controller.
	[    2.591758] dwmmc_rockchip ff500000.dwmmc: Version ID is 270a
	[    2.592379] dwmmc_rockchip ff500000.dwmmc: DW MMC controller at irq 34,32 bit host data width,256 deep fifo
	[    2.593473] vcc_sd: supplied by vcc_io
	[    2.610888] mmc_host mmc0: Bus speed (slot 0) = 400000Hz (slot req 400000Hz, actual 400000HZ div = 0)
	[    2.625718] dwmmc_rockchip ff520000.dwmmc: IDMAC supports 32-bit address mode.
	[    2.626416] dwmmc_rockchip ff520000.dwmmc: Using internal DMA controller.
	[    2.627082] dwmmc_rockchip ff520000.dwmmc: Version ID is 270a
	[    2.627681] dwmmc_rockchip ff520000.dwmmc: DW MMC controller at irq 35,32 bit host data width,256 deep fifo
	[    2.629784] mmc_host mmc1: card is non-removable.
	[    2.643322] mmc_host mmc1: Bus speed (slot 0) = 400000Hz (slot req 400000Hz, actual 400000HZ div = 0)
	[    2.657444] sdhci-pltfm: SDHCI platform and OF driver helper
	[    2.660232] ledtrig-cpu: registered to indicate activity on CPUs
	[    2.661448] SMCCC: SOC_ID: ARCH_SOC_ID not implemented, skipping ....
	[    2.662145] hid: raw HID events driver (C) Jiri Kosina
	[    2.663406] usbcore: registered new interface driver usbhid
	[    2.663932] usbhid: USB HID core driver
	[    2.675303] drop_monitor: Initializing network drop monitor service
	[    2.676193] Initializing XFRM netlink socket
	[    2.677560] NET: Registered protocol family 10
	[    2.680436] Segment Routing with IPv6
	[    2.684894] bpfilter: Loaded bpfilter_umh pid 153
	[    2.685892] NET: Registered protocol family 17
	[    2.686863] Bluetooth: RFCOMM TTY layer initialized
	[    2.687349] Bluetooth: RFCOMM socket layer initialized
	[    2.687870] Bluetooth: RFCOMM ver 1.11
	[    2.688235] Bluetooth: HIDP (Human Interface Emulation) ver 1.2
	[    2.688782] Bluetooth: HIDP socket layer initialized
	[    2.689373] Key type dns_resolver registered
	[    2.690448] registered taskstats version 1
	[    2.690922] Loading compiled-in X.509 certificates
	[    2.691483] Key type ._fscrypt registered
	[    2.691859] Key type .fscrypt registered
	[    2.692219] Key type fscrypt-provisioning registered
	[    2.744554] rockchip-drm display-subsystem: bound ff370000.vop (ops 0xffffffc010e399b0)
	[    2.745791] dwhdmi-rockchip ff3c0000.hdmi: Detected HDMI TX controller v2.11a with HDCP (inno_dw_hdmi_phy2)
	[    2.746881] usb 2-1: new high-speed USB device number 2 using ehci-platform
	[    2.747835] dwhdmi-rockchip ff3c0000.hdmi: registered DesignWare HDMI I2C bus driver
	[    2.749431] rockchip-drm display-subsystem: bound ff3c0000.hdmi (ops 0xffffffc010e3d930)
	[    2.750773] rockchip-drm display-subsystem: [drm] Cannot find any crtc or sizes
	[    2.751236] mmc_host mmc1: Bus speed (slot 0) = 150000000Hz (slot req 150000000Hz, actual 150000000HZ div = 0)
	[    2.752299] [drm] Initialized rockchip 1.0.0 20140818 for display-subsystem on minor 0
	[    2.752828] random: fast init done
	[    2.756215] asoc-simple-card hdmi-sound: ASoC: no DMI vendor name!
	[    2.760996] ALSA device list:
	[    2.761294]   #0: rockchip,hdmi
	[    2.761958] dw-apb-uart ff130000.serial: forbid DMA for kernel console
	[    2.769355] Freeing unused kernel memory: 5568K
	[    2.770536] rockchip-pm-domain ff100000.syscon:power-controller: failed to get ack on domain 'pd_hevc', val=0x88220
	[    2.774816] Run /init as init process
	[    2.775165]   with arguments:
	[    2.775171]     /init
	[    2.775176]     earlyprintk
	[    2.775181]   with environment:
	[    2.775186]     HOME=/
	[    2.775191]     TERM=linux
	[    2.908509] usb 2-1: New USB device found, idVendor=0bda, idProduct=c820, bcdDevice= 2.00
	[    2.909274] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
	[    2.909922] usb 2-1: Product: 802.11ac NIC
	[    2.910301] usb 2-1: Manufacturer: Realtek
	[    2.910681] usb 2-1: SerialNumber: 123456
	[    2.963892] dwmmc_rockchip ff520000.dwmmc: Successfully tuned phase to 195
	[    2.964650] mmc1: new HS200 MMC card at address 0001
	[    2.966560] mmcblk1: mmc1:0001 SOD16G 14.6 GiB 
	[    2.967638] mmcblk1boot0: mmc1:0001 SOD16G partition 1 4.00 MiB
	[    2.968786] mmcblk1boot1: mmc1:0001 SOD16G partition 2 4.00 MiB
	[    2.969786] mmcblk1rpmb: mmc1:0001 SOD16G partition 3 4.00 MiB, chardev (244:0)
	[    2.973181]  mmcblk1: p1 p2
	[    3.774964] rockchip-drm display-subsystem: [drm] Cannot find any crtc or sizes
	[    7.911095] EXT4-fs (mmcblk1p2): warning: mounting unchecked fs, running e2fsck is recommended
	[    7.914011] EXT4-fs (mmcblk1p2): mounted filesystem without journal. Opts: (null)
	[    8.321952] systemd[1]: systemd 245.4-4ubuntu3.2 running in system mode. (+PAM +AUDIT +SELINUX +IMA +APPARMOR +SMACK +SYSVINIT +UTMP +LIBCRYPTSETUP +GCRYPT +GNUTLS +ACL +XZ +LZ4 +SECCOMP +BLKID +ELFUTILS +KMOD +IDN2 -IDN +PCRE2 default-hierarchy=hybrid)
	[    8.325067] systemd[1]: Detected architecture arm64.
	[    8.363872] systemd[1]: Set hostname to <rockpie>.
	[    8.755834] systemd[1]: /lib/systemd/system/dbus.socket:5: ListenStream= references a path below legacy directory /var/run/, updating /var/run/dbus/system_bus_socket → /run/dbus/system_bus_socket; please update the unit file accordingly.
	[    8.855248] random: systemd: uninitialized urandom read (16 bytes read)
	[    8.860018] systemd[1]: Created slice system-modprobe.slice.
	[    8.871001] random: systemd: uninitialized urandom read (16 bytes read)
	[    8.873142] systemd[1]: Created slice system-serial\x2dgetty.slice.
	[    8.882986] random: systemd: uninitialized urandom read (16 bytes read)
	[    8.885140] systemd[1]: Created slice system-systemd\x2dfsck.slice.
	[    8.896895] systemd[1]: Created slice User and Session Slice.
	[    8.907440] systemd[1]: Started Dispatch Password Requests to Console Directory Watch.
	[    8.919410] systemd[1]: Started Forward Password Requests to Wall Directory Watch.
	[    8.932260] systemd[1]: Set up automount Arbitrary Executable File Formats File System Automount Point.
	[    8.943283] systemd[1]: Reached target Local Encrypted Volumes.
	[    8.955217] systemd[1]: Reached target Paths.
	[    8.967199] systemd[1]: Reached target Remote File Systems.
	[    8.979141] systemd[1]: Reached target Slices.
	[    8.991224] systemd[1]: Reached target Swap.
	[    9.003992] systemd[1]: Listening on Syslog Socket.
	[    9.015893] systemd[1]: Listening on fsck to fsckd communication Socket.
	[    9.027681] systemd[1]: Listening on initctl Compatibility Named Pipe.
	[    9.040585] systemd[1]: Listening on Journal Audit Socket.
	[    9.051988] systemd[1]: Listening on Journal Socket (/dev/log).
	[    9.064166] systemd[1]: Listening on Journal Socket.
	[    9.076241] systemd[1]: Listening on udev Control Socket.
	[    9.087812] systemd[1]: Listening on udev Kernel Socket.
	[    9.096900] systemd[1]: Mounting Huge Pages File System...
	[    9.117220] systemd[1]: Mounting POSIX Message Queue File System...
	[    9.129790] systemd[1]: Mounting Kernel Debug File System...
	[    9.151571] systemd[1]: Mounting Kernel Trace File System...
	[    9.164640] systemd[1]: Starting Journal Service...
	[    9.176164] systemd[1]: Starting Set the console keyboard layout...
	[    9.195595] systemd[1]: Starting Create list of static device nodes for the current kernel...
	[    9.207360] systemd[1]: Condition check resulted in Load Kernel Module drm being skipped.
	[    9.213185] systemd[1]: Condition check resulted in Set Up Additional Binary Formats being skipped.
	[    9.214431] systemd[1]: Condition check resulted in File System Check on Root Device being skipped.
	[    9.225856] systemd[1]: Starting Load Kernel Modules...
	[    9.247564] systemd[1]: Starting Remount Root and Kernel File Systems...
	[    9.262188] systemd[1]: Starting udev Coldplug all Devices...
	[    9.274585] systemd[1]: Mounted Huge Pages File System.
	[    9.288873] systemd[1]: Mounted POSIX Message Queue File System.
	[    9.306672] EXT4-fs (mmcblk1p2): re-mounted. Opts: commit=600,errors=remount-ro
	[    9.313074] systemd[1]: Mounted Kernel Debug File System.
	[    9.324715] systemd[1]: Mounted Kernel Trace File System.
	[    9.332243] systemd[1]: Finished Create list of static device nodes for the current kernel.
	[    9.348220] systemd[1]: Finished Load Kernel Modules.
	[    9.363548] systemd[1]: Finished Remount Root and Kernel File Systems.
	[    9.382706] systemd[1]: Mounting FUSE Control File System...
	[    9.393403] systemd[1]: Mounting Kernel Configuration File System...
	[    9.408021] systemd[1]: Condition check resulted in Rebuild Hardware Database being skipped.
	[    9.409132] systemd[1]: Condition check resulted in Platform Persistent Storage Archival being skipped.
	[    9.416371] systemd[1]: Starting Load/Save Random Seed...
	[    9.434226] systemd[1]: Starting Apply Kernel Variables...
	[    9.461093] systemd[1]: Starting Create System Users...
	[    9.474826] systemd[1]: Mounted FUSE Control File System.
	[    9.492412] systemd[1]: Finished Set the console keyboard layout.
	[    9.505018] systemd[1]: Mounted Kernel Configuration File System.
	[    9.540088] systemd[1]: Finished Create System Users.
	[    9.552783] systemd[1]: Started Journal Service.
	[    9.623487] systemd-journald[216]: Received client request to flush runtime journal.
	[   10.062495] rk3328-codec ff410000.codec: spk_depop_time use default value.
	[   10.086458] asoc-simple-card analog-sound: ASoC: no DMI vendor name!
	[   10.272586] usbcore: registered new interface driver btusb
	[   10.274878] Bluetooth: hci0: RTL: examining hci_ver=08 hci_rev=826c lmp_ver=08 lmp_subver=a99e
	[   10.274892] Bluetooth: hci0: RTL: unknown IC info, lmp subver a99e, hci rev 826c, hci ver 0008
	[   10.274904] Bluetooth: hci0: RTL: assuming no firmware upload needed
	[   10.380185] cfg80211: Loading compiled-in X.509 certificates for regulatory database
	[   10.424999] cfg80211: Loaded X.509 cert 'sforshee: 00b28ddf47aef9cea7'
	[   10.667787] EXT4-fs (mmcblk1p1): mounted filesystem with ordered data mode. Opts: (null)
	[   10.667858] ext4 filesystem being mounted at /boot supports timestamps until 2038 (0x7fffffff)
	[   11.060867] rk_gmac-dwmac ff550000.ethernet eth1: PHY [stmmac-0:00] driver [Rockchip integrated EPHY] (irq=POLL)
	[   11.070878] rk_gmac-dwmac ff550000.ethernet eth1: No Safety Features support found
	[   11.070903] rk_gmac-dwmac ff550000.ethernet eth1: PTP not supported by HW
	[   11.070916] rk_gmac-dwmac ff550000.ethernet eth1: configuring for phy/rmii link mode
	[   11.075817] rk_gmac-dwmac ff540000.ethernet eth0: PHY [stmmac-1:00] driver [RTL8211E Gigabit Ethernet] (irq=POLL)
	[   11.090851] rk_gmac-dwmac ff540000.ethernet eth0: No Safety Features support found
	[   11.090878] rk_gmac-dwmac ff540000.ethernet eth0: PTP not supported by HW
	[   11.090893] rk_gmac-dwmac ff540000.ethernet eth0: configuring for phy/rgmii link mode
	[   11.333454] usbcore: registered new interface driver rtl8821cu
	[   11.354636] rtl8821cu 2-1:1.2 wlx7ca7b0212134: renamed from wlan0
	[   11.975132] zram: Added device: zram0
	[   11.983859] zram: Added device: zram1
	[   11.986659] zram: Added device: zram2
	[   11.994544] zram: Added device: zram3
	[   12.069098] zram0: detected capacity change from 0 to 128602112
	[   12.188654] Bluetooth: BNEP (Ethernet Emulation) ver 1.3
	[   12.188664] Bluetooth: BNEP filters: protocol multicast
	[   12.188698] Bluetooth: BNEP socket layer initialized
	[   12.254286] NET: Registered protocol family 38
	[   12.927354] random: crng init done
	[   12.927371] random: 7 urandom warning(s) missed due to ratelimiting
	[   13.014884] Adding 125584k swap on /dev/zram0.  Priority:5 extents:1 across:125584k SSFS
	[   13.017637] zram1: detected capacity change from 0 to 128602112
	[   13.062860] Adding 125584k swap on /dev/zram1.  Priority:5 extents:1 across:125584k SSFS
	[   13.065404] zram2: detected capacity change from 0 to 128602112
	[   13.106813] Adding 125584k swap on /dev/zram2.  Priority:5 extents:1 across:125584k SSFS
	[   13.109300] zram3: detected capacity change from 0 to 128602112
	[   13.158868] Adding 125584k swap on /dev/zram3.  Priority:5 extents:1 across:125584k SSFS
	[   18.225318] IPv6: ADDRCONF(NETDEV_CHANGE): wlx7ca7b0212134: link becomes ready
	[   33.759041] vcc_sd: disabling
	[18887.127699] input: Haier HW-W910 (AVRCP) as /devices/virtual/input/input1


## Monitoring your RockPi E health with Htop 2.2.2-4

Latest htop 2.2.2-4 comes with a full set of tools to monitor your Wlan0, Eth0, Eth1, Cpu Frequency, Cpu Temperature in real-time from ssh section.
Run:

        sudo htop
	

![wlan0](https://github.com/avafinger/rockpie-rk3328/raw/master/wlan0_emmc_sdcard.png)

## Installing Ubuntu 20.04 LTS on eMMC (instructions)

[v1.0 - Kernel 5.9.0 RC2](https://github.com/avafinger/rockpie-rk3328/releases/tag/v1.0)

**Requirements**

* sd card (8GB or higher)
* eMMC (8GB or higher)
* Ethernet connection or Wifi or debug ttl uart

**Steps (WIP)**

* Flash the OS img file using Win32DiskImager or Etcher to a SD card
  Use 7zip to unzip the img file
* If you have eMMC, plugin the eMMC
* Boot RockPi E with the SD CARD inserted into the slot
  You will see the green led blinking (heartbeat)
  login: ubuntu
  paswd: ubuntu
* run the eMMC install script:
	
	  cd emmc
	  sudo ./flash_focal_mainline.sh /dev/mmcblk1
* Wait till finished and then run:

	  sync
	  sudo reboot


## Issues

* shutdown always reboot  the board
* need to figure out some pmic configuration


## Credits

I would like to thank Tom Cubie from Radxa for providing this RockPi E (Eng) sample
