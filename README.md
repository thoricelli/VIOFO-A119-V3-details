# VIOFO-A119-V3-details
This project contains reverse engineering efforts for the firmware/hardware of the VIOFO A119 V3.<br><br>
Please note that addresses/partitions/hardware details only apply for the VIOFO A119 V3, but there are a lot of simularities to simular Novatek devices. <br> <br>
The firmware/memory/coding is a result of the Novatek NT96670 SDK. But the SDK is not publicly available.

# Hardware details
| Type | Name | Size |
| --- | --- | --- |
| Processor | Novatek NT96670 | N/A |
| CMOS | <a href="https://www.phase1vision.com/userfiles/product_files/imx335lln_lqn_flyer.pdf">Sony Exmor IMX335</a> | N/A |
| Flash | <a href="https://www.macronix.com/Lists/Datasheet/Attachments/8660/MX25L6436F,%203V,%2064Mb,%20v1.2.pdf">Macronix MX25L6436F</a> | 64 MB |
| RAM | <a href="https://download.semiconductor.samsung.com/resources/data-sheet/DS_K4B2G1646F_BY_M_Rev1_0-1.pdf">Samsung K4B2G1646F-BYMA</a> | 2 GB |
| G-Sensor | DA380 | N/A |
| GPS-module | <a href="http://www.gotop-zzu.com/images/u/1593493826507.pdf">GAM-2525-MTR</a> + <a href="https://videoregforum.ru/threads/datakam-g5.946/page-349">Firmware</a> | N/A |

The NOVATEK NT96670, does not have publicly accessable datasheets. <br>
This SoC has 2 cpu's.

The first CPU (CPU1) is used for handling the image sensor (see below what its tasks are). <br>
The second CPU (CPU2) is used for handling WIFI (not included), saving to the SD card, handling the UI, etc..

# Partitions in memory
The memory map can be changed by the configuration specified in the firmware file.<br>
This configuration is used by U-BOOT to initialize and boot into the OS.
For more details see <a href="https://web.archive.org/web/20230216140514/https://www.twblogs.net/a/5e91e7c8bd9eee34b83e9da8">here</a>. (Chinese)

| Name | Address | Size | Description |
| --- | --- | --- | --- |
| IPC | 0x00002000 | 0x000FF000 | Shared memory between the two CPU cores. |
| DSP1 | 0x00101000 | 0x000FF000 | SDRAM? |
| ECOS | 0x00200000 | 0x00200000 | Ecos is used for threading, exchanging messages between cores, etc... |
| UITRON | 0x00400000 | 0x0FC00000 | After booting is completed, CPU1 and CPU2 boot into UITRON, the main OS. |
| UBOOT | 0x07900000 | 0x00700000 | CPU2 uses UBOOT to check for firmware upgrades on the SD card or ethernet, copies and prepares the OS |
| LOADER | 0xC07C0000 | 0x0000A000 | CPU1 uses this LOADER to initialize basic I/O and DRAM |

<b>!! U-BOOT and the LOADER are overwritten and used as memory for UITRON after the boot is completed. !!</b>

# Firmware partitions
The firmware is created by a program called NVTpack (not publicly available).
This program will pack the different partitions described here:
| ID | Name | Filename | Address offset (FWA119V3 ver2.6) | Size | Used by A119V3? |
| --- | --- | --- | --- | --- | --- |
| 0 | LOADER | LD96670A.bin | 0x0 (not included in FWA119V3.bin, is in LD96670A.bin) | 0x7FF0 | YES |
| 1 | MODELEXT | FW96670A.ext.bin | 0xB0 | 0xA58 | YES |
| 2 | UITRON | FW96670A.bin | 0xB08 | 0x4FCB08 | YES |
| 3 | UBOOT | u-boot.bin | 0x4FD610 | 0x41B68 | YES |
| 4 | UENV | ??? | N/A | N/A | NO |
| 5 | LINUX | uImage.bin | N/A | N/A | NO |
| 6 | ROOTFS (eCos in case of the A119V3) | rootfs.ubifs.bin (eCos.bin?) | 0x53F178 | 0x2ADB8 | YES | 
| 7 | PSTORE | ??? | N/A | N/A | NO |

You can download the loader for the A119V3 <a href="https://videoreg.forum2.net/viewtopic.php?id=92&p=3">here</a>. (Russian)<br>
And you can find the official firmware for the A119V3 <a href="https://viofo.com/content/41-viofo-a119-v3-dash-cam-support/">here</a>.

## BCL1 header
The eCos and uITRON partitions have been compressed using LZMA compression. <br>
The header follows a <a href="https://github.com/MariadeAnton/bcl">BCL header</a> (Basic Compression Library)
| Offset | Size | Name |
| --- | --- | --- |
| +0x0 | 4 | Magic number, always BCL1 |
| +0x4 | 4 | (Last byte) Compression type, 0x0B = LZMA |
| +0x8 | 4 | Original size in bytes |
| +0x10 | 4 | Compressed size in bytes |

For extracting partitions from the firmware file see <a href="https://github.com/EgorKin/Novatek-FW-info">here</a>.

# Flash partitions
| Name | Used by VIOFO A119V3?)
| --- | --- |
| Loader | YES |
| ModelExt | YES |
| uITron | YES |
| U-Boot | YES |
| Uenv (optional) | NO |
| Linux kernel (optional) | NO |
| ROOTFS (eCos) | YES |
| PStore (optional) | NO |
| DSP (optional) | NO? |
| FAT (optional) | NO |

Resources used:
<br>
<a href="https://web.archive.org/web/20230216140514/https://www.twblogs.net/a/5e91e7c8bd9eee34b83e9da8">https://www.twblogs.net/a/5e91e7c8bd9eee34b83e9da8</a>
<br>
<a href="https://github.com/hn/reolink-camera">https://github.com/hn/reolink-camera</a>
<br>
<a href="https://4pda.to/forum/index.php?showtopic=955294&st=220">https://4pda.to/forum/index.php?showtopic=955294&st=220</a>
<br>
<a href="https://videoreg.forum2.net/">https://videoreg.forum2.net/</a>
