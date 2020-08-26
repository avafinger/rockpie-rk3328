# RockPi E
RockPi E Eng. sample - model D8W2

This model has a micro HDMI for debugging purpose, but would be nice to have it in the final product or another version.


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
	[    0.000000] Linux version 5.9.0-rc2 (ubuntu@rockpie) (gcc (Ubuntu 9.3.0-10ubuntu2) 9.3.0, GNU ld (GNU Binutils for Ubuntu) 2.34) #1 SMP PREEMPT Mon Aug 24 19:36:08 UTC 2020
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
	[    0.000000] psci: PSCIv1.0 detected in firmware.
	[    0.000000] psci: Using standard PSCI v0.2 function IDs
	[    0.000000] psci: MIGRATE_INFO_TYPE not supported.
	[    0.000000] psci: SMC Calling Convention v1.0
	[    0.000000] percpu: Embedded 32 pages/cpu s91112 r8192 d31768 u131072
	[    0.000000] pcpu-alloc: s91112 r8192 d31768 u131072 alloc=32*4096
	[    0.000000] pcpu-alloc: [0] 0 [0] 1 [0] 2 [0] 3 
	[    0.000000] Detected VIPT I-cache on CPU0
	[    0.000000] CPU features: detected: ARM erratum 845719
	[    0.000000] Built 1 zonelists, mobility grouping on.  Total pages: 257544
	[    0.000000] Kernel command line: earlyprintk root=/dev/mmcblk1p2 rw rootfstype=ext4 rootwait fsck.repair=yes panic=10 no_console_suspend consoleblank=0
	[    0.000000] Dentry cache hash table entries: 131072 (order: 8, 1048576 bytes, linear)
	[    0.000000] Inode-cache hash table entries: 65536 (order: 7, 524288 bytes, linear)
	[    0.000000] mem auto-init: stack:off, heap alloc:off, heap free:off
	[    0.000000] Memory: 981724K/1046528K available (13628K kernel code, 1798K rwdata, 4612K rodata, 5632K init, 802K bss, 48420K reserved, 16384K cma-reserved)
	[    0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=4, Nodes=1
	[    0.000000] ftrace: allocating 45101 entries in 177 pages
	[    0.000000] ftrace: allocated 177 pages with 4 groups
	[    0.000000] rcu: Preemptible hierarchical RCU implementation.
	[    0.000000] rcu: 	RCU restricting CPUs from NR_CPUS=8 to nr_cpu_ids=4.
	[    0.000000] 	Trampoline variant of Tasks RCU enabled.
	[    0.000000] 	Rude variant of Tasks RCU enabled.
	[    0.000000] rcu: RCU calculated value of scheduler-enlistment delay is 25 jiffies.
	[    0.000000] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=4
	[    0.000000] NR_IRQS: 64, nr_irqs: 64, preallocated irqs: 0
	[    0.000000] GIC: Using split EOI/Deactivate mode
	[    0.000000] random: get_random_bytes called from start_kernel+0x378/0x538 with crng_init=0
	[    0.000000] arch_timer: cp15 timer(s) running at 24.00MHz (phys).
	[    0.000000] clocksource: arch_sys_counter: mask: 0xffffffffffffff max_cycles: 0x588fe9dc0, max_idle_ns: 440795202592 ns
	[    0.000008] sched_clock: 56 bits at 24MHz, resolution 41ns, wraps every 4398046511097ns
	[    0.000784] Console: colour dummy device 80x25
	[    0.001567] printk: console [tty0] enabled
	[    0.001639] Calibrating delay loop (skipped), value calculated using timer frequency.. 48.00 BogoMIPS (lpj=96000)
	[    0.001684] pid_max: default: 32768 minimum: 301
	[    0.001901] LSM: Security Framework initializing
	[    0.002030] Mount-cache hash table entries: 2048 (order: 2, 16384 bytes, linear)
	[    0.002074] Mountpoint-cache hash table entries: 2048 (order: 2, 16384 bytes, linear)
	[    0.005086] rcu: Hierarchical SRCU implementation.
	[    0.006697] EFI services will not be available.
	[    0.007621] smp: Bringing up secondary CPUs ...
	[    0.008780] Detected VIPT I-cache on CPU1
	[    0.008877] CPU1: Booted secondary processor 0x0000000001 [0x410fd034]
	[    0.010110] Detected VIPT I-cache on CPU2
	[    0.010191] CPU2: Booted secondary processor 0x0000000002 [0x410fd034]
	[    0.011365] Detected VIPT I-cache on CPU3
	[    0.011442] CPU3: Booted secondary processor 0x0000000003 [0x410fd034]
	[    0.011656] smp: Brought up 1 node, 4 CPUs
	[    0.011913] SMP: Total of 4 processors activated.
	[    0.011943] CPU features: detected: 32-bit EL0 Support
	[    0.011973] CPU features: detected: CRC32 instructions
	[    0.034164] CPU: All CPU(s) started at EL2
	[    0.034273] alternatives: patching kernel code
	[    0.036448] devtmpfs: initialized
	[    0.052997] Registered cp15_barrier emulation handler
	[    0.053061] Registered setend emulation handler
	[    0.053886] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 7645041785100000 ns
	[    0.053952] futex hash table entries: 1024 (order: 4, 65536 bytes, linear)
	[    0.055284] pinctrl core: initialized pinctrl subsystem
	[    0.056594] DMI not present or invalid.
	[    0.057517] NET: Registered protocol family 16
	[    0.061118] DMA: preallocated 128 KiB GFP_KERNEL pool for atomic allocations
	[    0.061410] DMA: preallocated 128 KiB GFP_KERNEL|GFP_DMA pool for atomic allocations
	[    0.061717] DMA: preallocated 128 KiB GFP_KERNEL|GFP_DMA32 pool for atomic allocations
	[    0.061872] audit: initializing netlink subsys (disabled)
	[    0.062260] audit: type=2000 audit(0.060:1): state=initialized audit_enabled=0 res=1
	[    0.063594] thermal_sys: Registered thermal governor 'fair_share'
	[    0.063600] thermal_sys: Registered thermal governor 'step_wise'
	[    0.064231] cpuidle: using governor ladder
	[    0.064318] cpuidle: using governor menu
	[    0.064769] hw-breakpoint: found 6 breakpoint and 4 watchpoint registers.
	[    0.064965] ASID allocator initialised with 65536 entries
	[    0.139733] HugeTLB registered 1.00 GiB page size, pre-allocated 0 pages
	[    0.139788] HugeTLB registered 32.0 MiB page size, pre-allocated 0 pages
	[    0.139820] HugeTLB registered 2.00 MiB page size, pre-allocated 0 pages
	[    0.139850] HugeTLB registered 64.0 KiB page size, pre-allocated 0 pages
	[    0.142740] cryptd: max_cpu_qlen set to 1000
	[    0.154813] iommu: Default domain type: Translated 
	[    0.157177] SCSI subsystem initialized
	[    0.157770] libata version 3.00 loaded.
	[    0.158141] usbcore: registered new interface driver usbfs
	[    0.158248] usbcore: registered new interface driver hub
	[    0.158421] usbcore: registered new device driver usb
	[    0.158571] mc: Linux media interface: v0.10
	[    0.158643] videodev: Linux video capture interface: v2.00
	[    0.158762] pps_core: LinuxPPS API ver. 1 registered
	[    0.158787] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
	[    0.158841] PTP clock support registered
	[    0.160204] Advanced Linux Sound Architecture Driver Initialized.
	[    0.161305] Bluetooth: Core ver 2.22
	[    0.161924] NET: Registered protocol family 31
	[    0.161954] Bluetooth: HCI device and connection manager initialized
	[    0.161998] Bluetooth: HCI socket layer initialized
	[    0.162032] Bluetooth: L2CAP socket layer initialized
	[    0.162086] Bluetooth: SCO socket layer initialized
	[    0.162141] NetLabel: Initializing
	[    0.162163] NetLabel:  domain hash size = 128
	[    0.162184] NetLabel:  protocols = UNLABELED CIPSOv4 CALIPSO
	[    0.162328] NetLabel:  unlabeled traffic allowed by default
	[    0.163471] clocksource: Switched to clocksource arch_sys_counter
	[    1.845777] VFS: Disk quotas dquot_6.6.0
	[    1.845915] VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
	[    1.846208] FS-Cache: Loaded
	[    1.861218] NET: Registered protocol family 2
	[    1.862377] tcp_listen_portaddr_hash hash table entries: 512 (order: 2, 20480 bytes, linear)
	[    1.862472] TCP established hash table entries: 8192 (order: 4, 65536 bytes, linear)
	[    1.862615] TCP bind hash table entries: 8192 (order: 6, 262144 bytes, linear)
	[    1.862954] TCP: Hash tables configured (established 8192 bind 8192)
	[    1.863241] UDP hash table entries: 512 (order: 3, 49152 bytes, linear)
	[    1.863360] UDP-Lite hash table entries: 512 (order: 3, 49152 bytes, linear)
	[    1.864150] NET: Registered protocol family 1
	[    1.865189] RPC: Registered named UNIX socket transport module.
	[    1.865232] RPC: Registered udp transport module.
	[    1.865255] RPC: Registered tcp transport module.
	[    1.865278] RPC: Registered tcp NFSv4.1 backchannel transport module.
	[    1.865320] PCI: CLS 0 bytes, default 64
	[    1.865731] Trying to unpack rootfs image as initramfs...
	[    1.940826] Freeing initrd memory: 1076K
	[    1.942572] hw perfevents: enabled with armv8_cortex_a53 PMU driver, 7 counters available
	[    1.950898] Initialise system trusted keyrings
	[    1.951317] workingset: timestamp_bits=46 max_order=18 bucket_order=0
	[    1.965506] squashfs: version 4.0 (2009/01/31) Phillip Lougher
	[    1.966105] FS-Cache: Netfs 'nfs' registered for caching
	[    1.967111] NFS: Registering the id_resolver key type
	[    1.967196] Key type id_resolver registered
	[    1.967220] Key type id_legacy registered
	[    1.967417] nfs4filelayout_init: NFSv4 File Layout Driver Registering...
	[    1.967943] fuse: init (API version 7.31)
	[    2.040402] Key type asymmetric registered
	[    2.040453] Asymmetric key parser 'x509' registered
	[    2.040564] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 247)
	[    2.040891] io scheduler mq-deadline registered
	[    2.040922] io scheduler kyber registered
	[    2.050039] rockchip-u3phy ff470000.usb3-phy: Rockchip u3phy initialized successfully
	[    2.055951] dma-pl330 ff1f0000.dmac: Loaded driver for PL330 DMAC-241330
	[    2.056009] dma-pl330 ff1f0000.dmac: 	DBUFF-128x8bytes Num_Chans-8 Num_Peri-20 Num_Events-16
	[    2.057016] Serial: 8250/16550 driver, 5 ports, IRQ sharing disabled
	[    2.060153] ff130000.serial: ttyS2 at MMIO 0xff130000 (irq = 14, base_baud = 1500000) is a 16550A
	[    2.135680] printk: console [ttyS2] enabled
	[    2.139035] rockchip-vop ff370000.vop: Adding to iommu group 1
	[    2.147368] rockchip-drm display-subsystem: bound ff370000.vop (ops 0xffffffc010e19630)
	[    2.162929] brd: module loaded
	[    2.182047] loop: module loaded
	[    2.185707] libphy: Fixed MDIO Bus: probed
	[    2.188118] rk_gmac-dwmac ff540000.ethernet: IRQ eth_wake_irq not found
	[    2.188745] rk_gmac-dwmac ff540000.ethernet: IRQ eth_lpi not found
	[    2.189576] rk_gmac-dwmac ff540000.ethernet: PTP uses main clock
	[    2.190461] rk_gmac-dwmac ff540000.ethernet: clock input or output? (input).
	[    2.191112] rk_gmac-dwmac ff540000.ethernet: TX delay(0x24).
	[    2.191677] rk_gmac-dwmac ff540000.ethernet: RX delay(0x18).
	[    2.192213] rk_gmac-dwmac ff540000.ethernet: integrated PHY? (no).
	[    2.192897] rk_gmac-dwmac ff540000.ethernet: cannot get clock clk_mac_speed
	[    2.193533] rk_gmac-dwmac ff540000.ethernet: clock input from PHY
	[    2.199122] rk_gmac-dwmac ff540000.ethernet: init for RGMII
	[    2.200054] rk_gmac-dwmac ff540000.ethernet: User ID: 0x10, Synopsys ID: 0x35
	[    2.200718] rk_gmac-dwmac ff540000.ethernet: 	DWMAC1000
	[    2.201201] rk_gmac-dwmac ff540000.ethernet: DMA HW capability register supported
	[    2.201884] rk_gmac-dwmac ff540000.ethernet: RX Checksum Offload Engine supported
	[    2.202567] rk_gmac-dwmac ff540000.ethernet: COE Type 2
	[    2.203047] rk_gmac-dwmac ff540000.ethernet: TX Checksum insertion supported
	[    2.203726] rk_gmac-dwmac ff540000.ethernet: Wake-Up On Lan supported
	[    2.204435] rk_gmac-dwmac ff540000.ethernet: Normal descriptors
	[    2.204982] rk_gmac-dwmac ff540000.ethernet: Ring mode enabled
	[    2.205517] rk_gmac-dwmac ff540000.ethernet: Enable RX Mitigation via HW Watchdog Timer
	[    2.206259] rk_gmac-dwmac ff540000.ethernet: device MAC address 6a:c6:3a:4e:ee:19
	[    2.267492] libphy: stmmac: probed
	[    2.272927] rk_gmac-dwmac ff550000.ethernet: IRQ eth_wake_irq not found
	[    2.273561] rk_gmac-dwmac ff550000.ethernet: IRQ eth_lpi not found
	[    2.274334] rk_gmac-dwmac ff550000.ethernet: PTP uses main clock
	[    2.275209] rk_gmac-dwmac ff550000.ethernet: clock input or output? (output).
	[    2.275920] rk_gmac-dwmac ff550000.ethernet: Can not read property: tx_delay.
	[    2.276577] rk_gmac-dwmac ff550000.ethernet: set tx_delay to 0x30
	[    2.277137] rk_gmac-dwmac ff550000.ethernet: Can not read property: rx_delay.
	[    2.277787] rk_gmac-dwmac ff550000.ethernet: set rx_delay to 0x10
	[    2.278370] rk_gmac-dwmac ff550000.ethernet: integrated PHY? (yes).
	[    2.279072] rk_gmac-dwmac ff550000.ethernet: cannot get clock clk_mac_refout
	[    2.279824] rk_gmac-dwmac ff550000.ethernet: cannot get clock clk_mac_speed
	[    2.285528] rk_gmac-dwmac ff550000.ethernet: init for RMII
	[    2.323846] rk_gmac-dwmac ff550000.ethernet: User ID: 0x10, Synopsys ID: 0x35
	[    2.324518] rk_gmac-dwmac ff550000.ethernet: 	DWMAC1000
	[    2.325002] rk_gmac-dwmac ff550000.ethernet: DMA HW capability register supported
	[    2.325687] rk_gmac-dwmac ff550000.ethernet: RX Checksum Offload Engine supported
	[    2.326371] rk_gmac-dwmac ff550000.ethernet: COE Type 2
	[    2.326851] rk_gmac-dwmac ff550000.ethernet: TX Checksum insertion supported
	[    2.327523] rk_gmac-dwmac ff550000.ethernet: Wake-Up On Lan supported
	[    2.328240] rk_gmac-dwmac ff550000.ethernet: Normal descriptors
	[    2.328786] rk_gmac-dwmac ff550000.ethernet: Ring mode enabled
	[    2.329324] rk_gmac-dwmac ff550000.ethernet: Enable RX Mitigation via HW Watchdog Timer
	[    2.332663] libphy: stmmac: probed
	[    2.338301] PPP generic driver version 2.4.2
	[    2.339145] usbcore: registered new interface driver asix
	[    2.339769] usbcore: registered new interface driver ax88179_178a
	[    2.340379] usbcore: registered new interface driver cdc_ether
	[    2.340984] usbcore: registered new interface driver qmi_wwan
	[    2.346112] phy phy-ff470000.usb3-phy.2: u3phy u2 power on
	[    2.346672] phy phy-ff470000.usb3-phy.3: u3phy u3 power on
	[    2.350740] dwc2 ff580000.usb: supply vusb_d not found, using dummy regulator
	[    2.351821] dwc2 ff580000.usb: supply vusb_a not found, using dummy regulator
	[    2.365507] dwc2 ff580000.usb: DWC OTG Controller
	[    2.365997] dwc2 ff580000.usb: new USB bus registered, assigned bus number 1
	[    2.366702] dwc2 ff580000.usb: irq 37, io mem 0xff580000
	[    2.367599] usb usb1: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.09
	[    2.368360] usb usb1: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.369020] usb usb1: Product: DWC OTG Controller
	[    2.369456] usb usb1: Manufacturer: Linux 5.9.0-rc2 dwc2_hsotg
	[    2.369993] usb usb1: SerialNumber: ff580000.usb
	[    2.371527] hub 1-0:1.0: USB hub found
	[    2.371957] hub 1-0:1.0: 1 port detected
	[    2.373555] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
	[    2.374185] ehci-pci: EHCI PCI platform driver
	[    2.374692] ehci-platform: EHCI generic platform driver
	[    2.378037] ehci-platform ff5c0000.usb: EHCI Host Controller
	[    2.378595] ehci-platform ff5c0000.usb: new USB bus registered, assigned bus number 2
	[    2.379635] ehci-platform ff5c0000.usb: irq 38, io mem 0xff5c0000
	[    2.395498] ehci-platform ff5c0000.usb: USB 2.0 started, EHCI 1.00
	[    2.396502] usb usb2: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.09
	[    2.397263] usb usb2: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.397924] usb usb2: Product: EHCI Host Controller
	[    2.398374] usb usb2: Manufacturer: Linux 5.9.0-rc2 ehci_hcd
	[    2.398895] usb usb2: SerialNumber: ff5c0000.usb
	[    2.400332] hub 2-0:1.0: USB hub found
	[    2.400751] hub 2-0:1.0: 1 port detected
	[    2.402066] ohci_hcd: USB 1.1 'Open' Host Controller (OHCI) Driver
	[    2.402689] ohci-platform: OHCI generic platform driver
	[    2.404003] ohci-platform ff5d0000.usb: Generic Platform OHCI controller
	[    2.404656] ohci-platform ff5d0000.usb: new USB bus registered, assigned bus number 3
	[    2.405603] ohci-platform ff5d0000.usb: irq 39, io mem 0xff5d0000
	[    2.467863] usb usb3: New USB device found, idVendor=1d6b, idProduct=0001, bcdDevice= 5.09
	[    2.468627] usb usb3: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.469289] usb usb3: Product: Generic Platform OHCI controller
	[    2.469832] usb usb3: Manufacturer: Linux 5.9.0-rc2 ohci_hcd
	[    2.470352] usb usb3: SerialNumber: ff5d0000.usb
	[    2.471784] hub 3-0:1.0: USB hub found
	[    2.472217] hub 3-0:1.0: 1 port detected
	[    2.474598] xhci-hcd xhci-hcd.0.auto: xHCI Host Controller
	[    2.475148] xhci-hcd xhci-hcd.0.auto: new USB bus registered, assigned bus number 4
	[    2.476198] xhci-hcd xhci-hcd.0.auto: hcc params 0x0220fe64 hci version 0x110 quirks 0x0000000002010010
	[    2.477164] xhci-hcd xhci-hcd.0.auto: irq 173, io mem 0xff600000
	[    2.478434] usb usb4: New USB device found, idVendor=1d6b, idProduct=0002, bcdDevice= 5.09
	[    2.479197] usb usb4: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.479966] usb usb4: Product: xHCI Host Controller
	[    2.480422] usb usb4: Manufacturer: Linux 5.9.0-rc2 xhci-hcd
	[    2.480942] usb usb4: SerialNumber: xhci-hcd.0.auto
	[    2.482417] hub 4-0:1.0: USB hub found
	[    2.482832] hub 4-0:1.0: 1 port detected
	[    2.483921] xhci-hcd xhci-hcd.0.auto: xHCI Host Controller
	[    2.484451] xhci-hcd xhci-hcd.0.auto: new USB bus registered, assigned bus number 5
	[    2.485169] xhci-hcd xhci-hcd.0.auto: Host supports USB 3.0 SuperSpeed
	[    2.485886] usb usb5: We don't know the algorithms for LPM for this host, disabling LPM.
	[    2.486852] usb usb5: New USB device found, idVendor=1d6b, idProduct=0003, bcdDevice= 5.09
	[    2.487698] usb usb5: New USB device strings: Mfr=3, Product=2, SerialNumber=1
	[    2.488363] usb usb5: Product: xHCI Host Controller
	[    2.488814] usb usb5: Manufacturer: Linux 5.9.0-rc2 xhci-hcd
	[    2.489335] usb usb5: SerialNumber: xhci-hcd.0.auto
	[    2.490772] hub 5-0:1.0: USB hub found
	[    2.491186] hub 5-0:1.0: 1 port detected
	[    2.492488] usbcore: registered new interface driver cdc_acm
	[    2.493016] cdc_acm: USB Abstract Control Model driver for USB modems and ISDN adapters
	[    2.493818] usbcore: registered new interface driver cdc_wdm
	[    2.494797] usbcore: registered new interface driver uas
	[    2.495566] usbcore: registered new interface driver usb-storage
	[    2.496298] usbcore: registered new interface driver usbserial_generic
	[    2.496935] usbserial: USB Serial support registered for generic
	[    2.497566] usbcore: registered new interface driver ch341
	[    2.498101] usbserial: USB Serial support registered for ch341-uart
	[    2.498753] usbcore: registered new interface driver cp210x
	[    2.499293] usbserial: USB Serial support registered for cp210x
	[    2.499999] usbcore: registered new interface driver ftdi_sio
	[    2.500556] usbserial: USB Serial support registered for FTDI USB Serial Device
	[    2.501574] usbcore: registered new interface driver option
	[    2.502115] usbserial: USB Serial support registered for GSM modem (1-port)
	[    2.503221] usbcore: registered new interface driver pl2303
	[    2.503817] usbserial: USB Serial support registered for pl2303
	[    2.504471] usbcore: registered new interface driver qcserial
	[    2.505027] usbserial: USB Serial support registered for Qualcomm USB modem
	[    2.506526] mousedev: PS/2 mouse device common for all mice
	[    2.508245] i2c /dev entries driver
	[    2.514365] rk808 1-0018: chip id: 0x8050
	[    2.522297] rk808-regulator rk808-regulator: there is no dvs0 gpio
	[    2.522924] rk808-regulator rk808-regulator: there is no dvs1 gpio
	[    2.523674] DCDC_REG1: supplied by vcc_sys
	[    2.526977] DCDC_REG2: supplied by vcc_sys
	[    2.529348] DCDC_REG3: supplied by vcc_sys
	[    2.530218] DCDC_REG4: supplied by vcc_sys
	[    2.532153] LDO_REG1: supplied by vcc_io
	[    2.535937] LDO_REG2: supplied by vcc_io
	[    2.539705] LDO_REG3: supplied by vcc_sys
	[    2.553350] rk808-rtc rk808-rtc: registered as rtc0
	[    2.555605] rk808-rtc rk808-rtc: setting system clock to 2020-08-25T01:32:45 UTC (1598319165)
	[    2.558546] input: rk805 pwrkey as /devices/platform/ff160000.i2c/i2c-1/1-0018/rk805-pwrkey/input/input0
	[    2.561069] IR NEC protocol handler initialized
	[    2.568736] dw_wdt ff1a0000.watchdog: No valid TOPs array specified
	[    2.570470] device-mapper: uevent: version 1.0.3
	[    2.571438] device-mapper: ioctl: 4.42.0-ioctl (2020-02-27) initialised: dm-devel@redhat.com
	[    2.579608] sdhci: Secure Digital Host Controller Interface driver
	[    2.580196] sdhci: Copyright(c) Pierre Ossman
	[    2.580597] Synopsys Designware Multimedia Card Interface Driver
	[    2.582412] dwmmc_rockchip ff500000.dwmmc: IDMAC supports 32-bit address mode.
	[    2.583123] dwmmc_rockchip ff500000.dwmmc: Using internal DMA controller.
	[    2.583905] dwmmc_rockchip ff500000.dwmmc: Version ID is 270a
	[    2.584532] dwmmc_rockchip ff500000.dwmmc: DW MMC controller at irq 33,32 bit host data width,256 deep fifo
	[    2.585646] vcc_sd: supplied by vcc_io
	[    2.603601] mmc_host mmc0: Bus speed (slot 0) = 400000Hz (slot req 400000Hz, actual 400000HZ div = 0)
	[    2.618926] dwmmc_rockchip ff520000.dwmmc: IDMAC supports 32-bit address mode.
	[    2.619699] dwmmc_rockchip ff520000.dwmmc: Using internal DMA controller.
	[    2.620321] dwmmc_rockchip ff520000.dwmmc: Version ID is 270a
	[    2.620925] dwmmc_rockchip ff520000.dwmmc: DW MMC controller at irq 34,32 bit host data width,256 deep fifo
	[    2.623187] mmc_host mmc1: card is non-removable.
	[    2.636632] mmc_host mmc1: Bus speed (slot 0) = 400000Hz (slot req 400000Hz, actual 400000HZ div = 0)
	[    2.650947] sdhci-pltfm: SDHCI platform and OF driver helper
	[    2.653886] ledtrig-cpu: registered to indicate activity on CPUs
	[    2.655260] hid: raw HID events driver (C) Jiri Kosina
	[    2.656724] usbcore: registered new interface driver usbhid
	[    2.657256] usbhid: USB HID core driver
	[    2.668613] drop_monitor: Initializing network drop monitor service
	[    2.669532] Initializing XFRM netlink socket
	[    2.670919] NET: Registered protocol family 10
	[    2.673881] Segment Routing with IPv6
	[    2.678495] bpfilter: Loaded bpfilter_umh pid 153
	[    2.679572] NET: Registered protocol family 17
	[    2.680416] Bluetooth: RFCOMM TTY layer initialized
	[    2.680906] Bluetooth: RFCOMM socket layer initialized
	[    2.681424] Bluetooth: RFCOMM ver 1.11
	[    2.681793] Bluetooth: HIDP (Human Interface Emulation) ver 1.2
	[    2.682343] Bluetooth: HIDP socket layer initialized
	[    2.682936] Key type dns_resolver registered
	[    2.684465] registered taskstats version 1
	[    2.684877] Loading compiled-in X.509 certificates
	[    2.685442] Key type ._fscrypt registered
	[    2.685819] Key type .fscrypt registered
	[    2.686182] Key type fscrypt-provisioning registered
	[    2.734134] rockchip-drm display-subsystem: bound ff370000.vop (ops 0xffffffc010e19630)
	[    2.735551] usb 2-1: new high-speed USB device number 2 using ehci-platform
	[    2.735568] dwhdmi-rockchip ff3c0000.hdmi: Detected HDMI TX controller v2.11a with HDCP (inno_dw_hdmi_phy2)
	[    2.738087] dwhdmi-rockchip ff3c0000.hdmi: registered DesignWare HDMI I2C bus driver
	[    2.739697] rockchip-drm display-subsystem: bound ff3c0000.hdmi (ops 0xffffffc010e1d5b0)
	[    2.740945] rockchip-drm display-subsystem: [drm] Cannot find any crtc or sizes
	[    2.742413] [drm] Initialized rockchip 1.0.0 20140818 for display-subsystem on minor 0
	[    2.746177] asoc-simple-card hdmi-sound: ASoC: no DMI vendor name!
	[    2.746727] mmc_host mmc1: Bus speed (slot 0) = 150000000Hz (slot req 150000000Hz, actual 150000000HZ div = 0)
	[    2.748442] random: fast init done
	[    2.751005] ALSA device list:
	[    2.751328]   #0: rockchip,hdmi
	[    2.752160] dw-apb-uart ff130000.serial: forbid DMA for kernel console
	[    2.760418] rockchip-pm-domain ff100000.syscon:power-controller: failed to get ack on domain 'pd_hevc', val=0x88220
	[    2.766254] Freeing unused kernel memory: 5632K
	[    2.771611] Run /init as init process
	[    2.771960]   with arguments:
	[    2.771965]     /init
	[    2.771969]     earlyprintk
	[    2.771972]   with environment:
	[    2.771976]     HOME=/
	[    2.771979]     TERM=linux
	[    2.896673] usb 2-1: New USB device found, idVendor=0bda, idProduct=c820, bcdDevice= 2.00
	[    2.897429] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
	[    2.898067] usb 2-1: Product: 802.11ac NIC
	[    2.898438] usb 2-1: Manufacturer: Realtek
	[    2.898809] usb 2-1: SerialNumber: 123456
	[    2.961150] dwmmc_rockchip ff520000.dwmmc: Successfully tuned phase to 192
	[    2.962146] mmc1: new HS200 MMC card at address 0001
	[    2.964545] mmcblk1: mmc1:0001 SOD16G 14.6 GiB 
	[    2.965776] mmcblk1boot0: mmc1:0001 SOD16G partition 1 4.00 MiB
	[    2.967059] mmcblk1boot1: mmc1:0001 SOD16G partition 2 4.00 MiB
	[    2.968292] mmcblk1rpmb: mmc1:0001 SOD16G partition 3 4.00 MiB, chardev (244:0)
	[    2.972878]  mmcblk1: p1 p2
	[    3.743707] rockchip-drm display-subsystem: [drm] Cannot find any crtc or sizes
	[    7.881575] EXT4-fs (mmcblk1p2): mounted filesystem without journal. Opts: (null)
	[    8.289552] systemd[1]: systemd 245.4-4ubuntu3.2 running in system mode. (+PAM +AUDIT +SELINUX +IMA +APPARMOR +SMACK +SYSVINIT +UTMP +LIBCRYPTSETUP +GCRYPT +GNUTLS +ACL +XZ +LZ4 +SECCOMP +BLKID +ELFUTILS +KMOD +IDN2 -IDN +PCRE2 default-hierarchy=hybrid)
	[    8.292962] systemd[1]: Detected architecture arm64.
	[    8.348626] systemd[1]: Set hostname to <rockpie>.
	[    8.743682] systemd[1]: /lib/systemd/system/dbus.socket:5: ListenStream= references a path below legacy directory /var/run/, updating /var/run/dbus/system_bus_socket → /run/dbus/system_bus_socket; please update the unit file accordingly.
	[    8.842401] random: systemd: uninitialized urandom read (16 bytes read)
	[    8.847315] systemd[1]: Created slice system-modprobe.slice.
	[    8.859769] random: systemd: uninitialized urandom read (16 bytes read)
	[    8.861943] systemd[1]: Created slice system-serial\x2dgetty.slice.
	[    8.871727] random: systemd: uninitialized urandom read (16 bytes read)
	[    8.873926] systemd[1]: Created slice system-systemd\x2dfsck.slice.
	[    8.885543] systemd[1]: Created slice User and Session Slice.
	[    8.896123] systemd[1]: Started Dispatch Password Requests to Console Directory Watch.
	[    8.908087] systemd[1]: Started Forward Password Requests to Wall Directory Watch.
	[    8.920797] systemd[1]: Set up automount Arbitrary Executable File Formats File System Automount Point.
	[    8.931961] systemd[1]: Reached target Local Encrypted Volumes.
	[    8.943981] systemd[1]: Reached target Paths.
	[    8.955882] systemd[1]: Reached target Remote File Systems.
	[    8.967846] systemd[1]: Reached target Slices.
	[    8.979929] systemd[1]: Reached target Swap.
	[    8.992649] systemd[1]: Listening on Syslog Socket.
	[    9.004698] systemd[1]: Listening on fsck to fsckd communication Socket.
	[    9.016294] systemd[1]: Listening on initctl Compatibility Named Pipe.
	[    9.029127] systemd[1]: Listening on Journal Audit Socket.
	[    9.040625] systemd[1]: Listening on Journal Socket (/dev/log).
	[    9.052940] systemd[1]: Listening on Journal Socket.
	[    9.064977] systemd[1]: Listening on udev Control Socket.
	[    9.076547] systemd[1]: Listening on udev Kernel Socket.
	[    9.085746] systemd[1]: Mounting Huge Pages File System...
	[    9.096011] systemd[1]: Mounting POSIX Message Queue File System...
	[    9.107290] systemd[1]: Mounting Kernel Debug File System...
	[    9.118916] systemd[1]: Mounting Kernel Trace File System...
	[    9.141592] systemd[1]: Starting Journal Service...
	[    9.159357] systemd[1]: Starting Set the console keyboard layout...
	[    9.169161] systemd[1]: Starting Create list of static device nodes for the current kernel...
	[    9.180114] systemd[1]: Condition check resulted in Load Kernel Module drm being skipped.
	[    9.186278] systemd[1]: Condition check resulted in Set Up Additional Binary Formats being skipped.
	[    9.187668] systemd[1]: Condition check resulted in File System Check on Root Device being skipped.
	[    9.199707] systemd[1]: Starting Load Kernel Modules...
	[    9.209101] systemd[1]: Starting Remount Root and Kernel File Systems...
	[    9.239806] systemd[1]: Starting udev Coldplug all Devices...
	[    9.251419] systemd[1]: Mounted Huge Pages File System.
	[    9.255933] systemd[1]: Mounted POSIX Message Queue File System.
	[    9.265951] EXT4-fs (mmcblk1p2): re-mounted. Opts: commit=600,errors=remount-ro
	[    9.274585] systemd[1]: Mounted Kernel Debug File System.
	[    9.277725] systemd[1]: Mounted Kernel Trace File System.
	[    9.293131] systemd[1]: Finished Create list of static device nodes for the current kernel.
	[    9.309844] systemd[1]: Finished Load Kernel Modules.
	[    9.324832] systemd[1]: Finished Remount Root and Kernel File Systems.
	[    9.343395] systemd[1]: Mounting FUSE Control File System...
	[    9.361655] systemd[1]: Mounting Kernel Configuration File System...
	[    9.371177] systemd[1]: Condition check resulted in Rebuild Hardware Database being skipped.
	[    9.372813] systemd[1]: Condition check resulted in Platform Persistent Storage Archival being skipped.
	[    9.380193] systemd[1]: Starting Load/Save Random Seed...
	[    9.389282] systemd[1]: Starting Apply Kernel Variables...
	[    9.412139] systemd[1]: Starting Create System Users...
	[    9.449178] systemd[1]: Mounted FUSE Control File System.
	[    9.452263] systemd[1]: Mounted Kernel Configuration File System.
	[    9.482756] systemd[1]: Finished Set the console keyboard layout.
	[    9.497255] systemd[1]: Finished Create System Users.
	[    9.517399] systemd[1]: Finished Apply Kernel Variables.
	[    9.529286] systemd[1]: Started Journal Service.
	[    9.584129] systemd-journald[216]: Received client request to flush runtime journal.
	[   10.024698] rk3328-codec ff410000.codec: spk_depop_time use default value.
	[   10.052037] asoc-simple-card analog-sound: ASoC: no DMI vendor name!
	[   10.269940] usbcore: registered new interface driver btusb
	[   10.277910] Bluetooth: hci0: RTL: examining hci_ver=08 hci_rev=826c lmp_ver=08 lmp_subver=a99e
	[   10.277937] Bluetooth: hci0: RTL: unknown IC info, lmp subver a99e, hci rev 826c, hci ver 0008
	[   10.277943] Bluetooth: hci0: RTL: assuming no firmware upload needed
	[   10.479424] cfg80211: Loading compiled-in X.509 certificates for regulatory database
	[   10.518195] EXT4-fs (mmcblk1p1): mounted filesystem with ordered data mode. Opts: (null)
	[   10.518264] ext4 filesystem being mounted at /boot supports timestamps until 2038 (0x7fffffff)
	[   10.543014] cfg80211: Loaded X.509 cert 'sforshee: 00b28ddf47aef9cea7'
	[   10.861095] rk_gmac-dwmac ff540000.ethernet eth0: PHY [stmmac-1:00] driver [RTL8211E Gigabit Ethernet] (irq=POLL)
	[   10.879670] rk_gmac-dwmac ff540000.ethernet eth0: No Safety Features support found
	[   10.879701] rk_gmac-dwmac ff540000.ethernet eth0: PTP not supported by HW
	[   10.879717] rk_gmac-dwmac ff540000.ethernet eth0: configuring for phy/rgmii link mode
	[   10.945962] rk_gmac-dwmac ff550000.ethernet eth1: PHY [stmmac-0:00] driver [Rockchip integrated EPHY] (irq=POLL)
	[   10.955645] rk_gmac-dwmac ff550000.ethernet eth1: No Safety Features support found
	[   10.955674] rk_gmac-dwmac ff550000.ethernet eth1: PTP not supported by HW
	[   10.955691] rk_gmac-dwmac ff550000.ethernet eth1: configuring for phy/rmii link mode
	[   11.276852] usbcore: registered new interface driver rtl8821cu
	[   11.291970] rtl8821cu 2-1:1.2 wlx7ca7b0212134: renamed from wlan0
	[   11.906279] zram: Added device: zram0
	[   11.916343] zram: Added device: zram1
	[   11.917548] zram: Added device: zram2
	[   11.918719] zram: Added device: zram3
	[   11.993418] zram0: detected capacity change from 0 to 128618496
	[   12.073837] Bluetooth: BNEP (Ethernet Emulation) ver 1.3
	[   12.073855] Bluetooth: BNEP filters: protocol multicast
	[   12.073893] Bluetooth: BNEP socket layer initialized
	[   12.118642] NET: Registered protocol family 38
	[   12.913147] random: crng init done
	[   12.913164] random: 7 urandom warning(s) missed due to ratelimiting
	[   13.075581] Adding 125600k swap on /dev/zram0.  Priority:5 extents:1 across:125600k SSFS
	[   13.078490] zram1: detected capacity change from 0 to 128618496
	[   13.123616] Adding 125600k swap on /dev/zram1.  Priority:5 extents:1 across:125600k SSFS
	[   13.126249] zram2: detected capacity change from 0 to 128618496
	[   13.179569] Adding 125600k swap on /dev/zram2.  Priority:5 extents:1 across:125600k SSFS
	[   13.182102] zram3: detected capacity change from 0 to 128618496
	[   13.231604] Adding 125600k swap on /dev/zram3.  Priority:5 extents:1 across:125600k SSFS
	[   18.113915] IPv6: ADDRCONF(NETDEV_CHANGE): wlx7ca7b0212134: link becomes ready
	[   33.759561] vcc_sd: disabling


## Monitoring your RockPi E health with Htop 2.2.2-4

Latest htop 2.2.2-4 comes with a full set of tools to monitor your Wlan0, Eth0, Eth1, Cpu Frequency, Cpu Temperature in real-time from ssh section.
Run:

        sudo htop
	

![wlan0](https://github.com/avafinger/rockpie-rk3328/raw/master/wlan0_emmc_sdcard.png)

## Installing Ubuntu 20.04 LTS on eMMC (instructions)

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
