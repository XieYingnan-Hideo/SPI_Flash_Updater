# SPI_Flash_Updater
> Function: Update contents of onboard SPI Flash(i.e. W25Q64) with an MicroSD Card. Based on FatFS and STM32F103.

## Basic infomation
- Project Name:SPI_Flash_Updater
- Revise version:V1.0
- Created date:2020-8-2
- Author:Xie Yingnan (xieyingnan1994@163.com)

## Hardware requirements
### 0. CPU
ARM Cortex-M3 STM32F10x
### 1.MicroSD Card (Operated in SPI mode)
| Function | CMD(3) | DAT0(7) | CLK(5) | CD/DAT3(2) |
| :------: | :----: | :-----: | :----: | :--------: |
| Pin      |  PA7   | PA6     | PA5    | PA4        |

### 2.SPI Flash( Type: W25QXX)
SPI Flash shares same SPI port with SDcard.

| Function | MOSI(5) | MISO(2) | SCK(6) | CS(1) |
| :------: | :-----: | :-----: | :----: | :---: |
| Pin      |  PA7    | PA6     | PA5    | PC13  |
### 3.Serial Port( For debug info print and convey command strobes)
|  Function |UART1 Tx |  UART1 Rx  |
| :-------: | :-----: | :--------: |
| Pin       | PA9     | PA10       |

**Pattern: 115200 bps, No parity, 8 bits, 1 stopbits**
### 4.Indicators(LED)
|  Function | Pin on MCU  |
| :------------: | :------------: |
| STATUS | PA1  |

LED is connected  to MCU with cathode.
## Details
### 1.About the BURNLIST.TXT file
The files to burn are contained in the root directory of SD card. MCU reads the `BURNLIST.TXT` file storged in the root directory of SD card, to determine which file to burn and where to burn. The phrase **Burn** meaning copy the corresponding file to SPI Flash ROM. The layout pattern of`BURNLIST.TXT` file should be as following diagram.

| Filename |File Length(n\*4096)|Start position in Flash(n\*4096)| Toggle burn| Description |
| :------: | :----------------: | :----------------------------: |:-----------: |:-----------: |
| UNIGBK.BIN| 43 | 470 |true|Filesystem Chinese Unicode Supporting Data

This file holds a list of files to burn. Each line signifies an entry, which contains the filename, how many sectors does the file occupy, and the actual sector address position of the file, and a toggle bit to determine burn it or not, as well as an brief description of the file, which will be shown on serial prompt to help us identify it. The example of `BURNLIST.TXT` file is shown below.

> \#Name/Length(n\*4096)/StartPosition(n\*4096)/Toggle burn/Description
HZLIB.BIN 64 true 406 Chinese Kanji type dot matrix data
SPLSSCR.BIN 1 false 470 Splash screen dot matrix data
UNIGBK.BIN 43 true 480 Unicode to GBK converting pairs for FatFS chinese support.

### 2.About the command strings
The operating commands is conducted via serial port, debug and status information of the whole burning procedure is printed via serial port as well. Command strobes to oprate are listed as follow. 
**Each command string should be followed by a Windows-style newline!(aka. CR+LF).**

|Command string|Function |
| :-------: | :-----: |
|$| Show this help tips again |
|$V| Show build information |
|$LIST| List files contain in BURNLIST.TXT |
|$ERASE| ERASE whole SPI Flash chip! |
|$DETECT|Detect chip and show chip serail number. |
|$BURN|Burn the SPI Flash according the file list of BURNLIST.TXT |

### 3.About the status LED
- Status LED will blink once when:
 - Each time a file is successfully burned to Flash.
 - Each time a command is parsed and executed.
- Status LED will blink slowly when:
 - All files of BURNLIST.TXT is burned to Flash successfully.
- Status LED will blink fastly when:
 - Any trouble encountered.

------------

End of File (Edited by Xie Yingnan 2020-8-2 16:21)
