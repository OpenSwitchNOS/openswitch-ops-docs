# Deploy image to physical switch

After your build completes you will have an ONIE file that can be uploaded to the switch. The file can be found in the `images` directory and is typically called `onie-installer-x86_64-as5712_54x`. For an as5712 platform, follow these instructions:

1. Copy the ONIE file to your TFTP directory.
2. Reboot the switch.
3. When switch boots it will bring up a GNU GRUB selection screen.  Using your arrow keys move down to **ONIE** then select **ONIE : Install OS**.
4. Enter the following commands:
```
tftp -g -r <onie-file> -l <onie-file> <tftp-server>
chmod +x <onie-file>
./<onie-file>
reboot
```
