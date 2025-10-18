This command is used to built a custom OS from the start. Here I am telling a few main points that is not very well documented but can be hear-scratching to find out

- To put a file in the OS, you use the map flag in the following fashion
	```bash
xorisso \
	-indev "OATH/TO/BASEIMAGE.ISO"
	-outdev "PATH/FOR/FINALIMAGE.ISO"
		-map "PATH/OF/FILE/TO/BE/TRANSFERRED" /PATH/OF/FILE/IN/CUSTOM-OS
	```
	Here, the path of file in the custom-os `/PATH/OF/FILE/IN/CUSTOM-OS` is only available during the OS installation and first boot. And during that time also the path would be inside the `cdrom` folder. So during the OS installation, you can find the files at `/cdrom//PATH/OF/FILE/IN/CUSTOM-OS`. The right approach would be, to write a copy file command in late commands and if the destination is user-specific which is not available during execution of late commands, write an additional copy command in the cloud-init ( which is written inside user-data directive of user-data file) section, inside a `runcmd` directive. Here is an complete auto-install user-data file with all necessary configurations
	```yaml
	#cloud-config
autoinstall:
  version: 1
  identity:
    hostname: ubuntu
    username: ubuntu
    # Password is required by the installer
    # Generate with: mkpasswd --method=SHA-512 ubuntu
    password: $6$TGY8WTYpDMnvBdyr$OnAGyuJKe6ti6DPsmQK40vHfjj7pm5.siZyKH/pODZC0M3UUJUUUs9/8wLmZfUc6QKawgrrU0P0XyEg8cCxK6/
  locale: en_US.UTF-8
  keyboard: { layout: us, variant: "" }

  # Cloud-init config for the *installed* system
  user-data:
    # UFW baseline and Docker daemon tweaks
    runcmd:
      # Adding and moving files
      - mkdir -p /home/fps/Desktop
      - cp /etc/cctv/fpcctv.desktop /home/fps/Desktop/
      - chown fps:fps /home/fps/Desktop/fpcctv.desktop
      - chmod +x /home/fps/Desktop/fpcctv.desktop

  # Installer-side commands (run before first boot of the target system)
  late-commands:
    - mkdir -p /target/etc/cctv
    - curtin in-target -- cp /cdrom/custom/fpcctv.desktop /etc/cctv/
    - curtin in-target -- echo "Template ready"

	```
	and I have used the following `xorisso` command to get the custom-installed system
	```bash
	xorriso \
	  -indev  "$TMP/ubuntu.iso" \
	  -outdev "$OUTDIR/box-autoinstall.iso" \
	  -map box-os/autoinstall/user-data /nocloud/user-data \
	  -map box-os/autoinstall/meta-data /nocloud/meta-data \
	  -map "$TMP/grub.cfg" /boot/grub/grub.cfg \
	  "${LOOPBACK_MAP_ARGS[@]}" \
	  -map "./fpcctv.desktop" /custom/fpcctv.desktop \
	  -boot_image any replay
	```