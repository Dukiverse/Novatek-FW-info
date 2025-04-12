# Overview
This fork of NTKFWinfo addresses an issue with the LZMA compression in BCL1 partitions in NT96670 firmware. The problem was that recompression would shrink the partition, misaligning offsets and invalidating the partition CRC. This patch modifies the LZMA compression branch to pad the compressed data to exactly the expected size (original partition size minus the header) so that the firmware image remains fully intact. The overall firmware CRC remains unchanged.

# Changes
Modified the LZMA (Algorithm 0x0B) branch in the BCL1_compress() function.
Added padding after compression to ensure that the packed data size equals `part_size - 16` bytes.
Ensured that the recalculated CRC for the partition is updated correctly.

This fix preserves the original firmware image layout, ensuring that devices (tested on NT96670 dash cam) can boot reliably with modified firmware.

# Novatek Firmware info (NTKFWinfo)
Python script for work with Novatek firmware files. Show full FW info, allow extract, replace, uncompress, compress partitions, fix CRC for modded partitions and whole firmware file. Useful tool for building custom firmwares.
Use Linux environment or WSL2 (Windows Subsystem for Linux) for properly work with UBI and SPARSE partitions.

Novatek FW info is available under the terms of the GNU Public License version 3.

If this project helps you, you can send me any amount of BTC to address ***12q5kucN1nvWq4gn5V3WJ8LFS6mtxbymdj*** or use QR code below:

![btc](https://github.com/user-attachments/assets/32b5e7a0-0f01-4a38-b062-3162e6a79543)





![Безымянный](https://user-images.githubusercontent.com/4955678/184808463-1b5d62b6-eb76-41d9-a75a-dbd019e8f60f.png)
![NTKFWinfo2](https://user-images.githubusercontent.com/4955678/188560457-54a2b532-61db-4ca8-9b3c-c4916cae1c62.png)


## Installation:
**First of all install:**
```
sudo apt-get install python3
sudo apt-get install python3-pip
sudo apt-get install mtd-utils
sudo apt-get install liblzo2-dev
sudo apt-get install pypy3
```
**Additionaly install this python modules:**
```
sudo pip3 install python-lzo
sudo pip3 install ubi_reader
```
**For SPARSE partitions support install:**
```
sudo apt-get install android-sdk-libsparse-utils
```
**For FDT(DTB) partitions support install:**
```
sudo apt-get install device-tree-compiler
```
## Basic usage:

### Show firmware partitions info:
```
python3 ./NTKFWinfo.py -i FWA229A.bin
```
### Uncompress partition by ID to a file(for BCL1) or folder(for UBI or SPARSE):
```
python3 ./NTKFWinfo.py -i FWA229A.bin -u 6
```
### Compress partition back to firmware binary file from uncompressed file(for BCL1) or folder(for UBI or SPARSE) and fix all CRC:
```
python3 ./NTKFWinfo.py -i FWA229A.bin -c 6
```

## Additional(expert) commands:
### Extract partition by ID number and skip n start bytes:
(as example extract data from CKSM partition require skip first 64 CKSM-header bytes)
```
python3 ./NTKFWinfo.py -i FWA229A.bin -x 6 64
```
### Replace partition with ID=6 and start offset = 64 (CKSM-header size) using file img-726660551.ubi:
```
python3 ./NTKFWinfo.py -i FWA229A.bin -r 6 64 ./img-726660551.ubi
```
### Fix CRC for all known partitions(uboot, MODELEXT INFO, BCL1, CKSM) and whole firmware file:
```
python3 ./NTKFWinfo.py -i FWA229A.bin -fixCRC
```
### Use custom directory for input/output partitions files:
```
python3 ./NTKFWinfo.py -i FWA229A.bin -u 6 -o tempdir
python3 ./NTKFWinfo.py -i FWA229A.bin -c 6 -o tempdir
```

## Speed-up compress BCL1 LZ partitions:
I suggest use pypy3 VS python3 to decrease compression time for BCL1 LZ partitions:
```
pypy3 ./NTKFWinfo.py -i GIT3FWv2.3.bin -c 0
```
![pypy3 speed-up](https://user-images.githubusercontent.com/4955678/188559054-e3ea1152-743b-4686-8a4f-b76c0dd529ba.png)

