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

* U-boot (shutdown issue, reboot works)

## Kernel 5.9.0-rc1

Testing mainline **kernel 5.9.0-rc1**, current status:

* Eth0 - ok
* Eth1 - ok
* Wifi (5 GHz) - ok
* BT - ok
* eMMC - need more tests
* hdmi - need more tests (?)
* sound (?)

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



## Credits

I would like to thanks Tom Cubie from Radxa for providing this RockPi E (Eng) sample
