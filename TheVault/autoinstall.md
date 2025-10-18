- [[xorisso]]
---

- So here I am presenting a complete step-by-step process for writing a complete unattended installation of `ubuntu 24` LTS version. There's not much explanation but it's mostly because I don't want to get into it. 
- You can go through these two blogs one by one to understand the details at some level [1](https://medium.com/@Phoenixforge/getting-your-feet-wet-with-ubuntu-autoinstall-e2c012454a37) [2](https://medium.com/@Phoenixforge/diving-deeper-with-ubuntu-autoinstall-b15e5bdffbb5)
- Also, Here's the [official Ubuntu autoinstall docs](https://canonical-subiquity.readthedocs-hosted.com/en/latest/intro-to-autoinstall.html), and [autoinstall reference](https://canonical-subiquity.readthedocs-hosted.com/en/latest/reference/autoinstall-reference.html) and [Clout-init docs](https://cloudinit.readthedocs.io/en/latest/tutorial/index.html)
----
1. Have at least 100 GB of empty space in your OS, and this whole setup is based from Ubuntu only.
2. Download `Ubuntu 24.04 LTS` desktop version from official ubuntu website
3. Install `QEMU` to be used for testing the images in a `VM`
```bash
sudo apt-get install qemu-system
```
4. this is a `CLI`, so let's get a GUI for it `virt-manager`
```bash
sudo apt-get install virt-manager
```
5. Restart your system so as appropriate `daemon` processes can start
6. We need to install `Cubic` for customising the `iso` images and get a custom `iso` Ubuntu auto-install image, for that you follow [this](https://github.com/PJ-Singh-001/Cubic) or follow the commands below
```
sudo apt-add-repository universe
sudo apt-add-repository ppa:cubic-wizard/release
sudo apt update
sudo apt install --no-install-recommends cubic
```
7. Open the cubic installer, select the folder where you want for the custom image to be installed and where we have to make changes eventually.
	1. Here is where you'll select the folder where you want to install the custom image ![[Cubic_Start_Page.png]]
	2. Just select the Ubuntu `iso` image you downloaded here in the Filename box and everything will be filled automatically, click next. ![[Cubic_Project_Page.png]]
	3. Cubic will extract the image and do a bunch of stuff then ![[Cubic_Extract_Page.png]]
	4. Now, here you have a terminal where you can add files or folders that you want your final OS to have at last ![[Cubic_Terminal_Page.png]]
		For ex, if you have a script that will install `docker` on the system in automated fashion and you want it to access in the final OS build, you can follow like this
		```bash
		> nano /usr/local/bin/install-docker.sh
		# paste the script in the nano editor, and exit
		> chmod +x /usr/local/bin/install-docker.sh # making script executable
		```
		Now the script is added where it should be, after the OS is installed you can execute the script using
		```bash
		sudo install-docker.sh
		```
		Let's move ahead now, click next after all of this done
		
	5. The preparation preparation will start here![[Cubic_Prepare_Page.png]]
	6. Now, here we add the custom install `GRUB` install option, and the `ubuntu autoinstall` script.
		1. open the folder in the `VSCode` that you selected in the first step
		2. follow the given commands
		```bash
		cd custom-disk
		mkdir nocloud
		cd nocloud
		touch user-data
		touch meta-data
		```
		3. Now open the user-data file and here we would be writing our autoinstall/cloud config. here is a basic config example
		```yaml
#cloud-config
autoinstall:
  version: 1
  identity:
    hostname: ubuntu
    username: ubuntu
    password: <mkpasswd -m sha-512 ubuntu> # run this command, paste the output
  ssh:
    install-server: yes
    allow-pw: yes
  late-commands:
    - curtin in-target -- sh -c "apt-get update"
    - poweroff
		```
		So here, we are installing setting a user with `hostname`, `username`, `passowrd` set to `ubuntu` and, we are installing a `ssh-server`, and there's a `late-commands` section that tells the commands to run when the OS installation is complete. Also, the prefix `curtin in-target -- sh -c ""` is necessary to run the command in the shell. At last we are shutting down the system to indicate the installation has been complete.
		4. `meta-data` would remain empty
		5. open the `grub.cfg` by `code custom-disk/boot/grub/grub.cfg` from root of the folder
		6. Reduce the timing to wait for boot from 30 to 10 and add the first option as the `autoinstall` option in the menuentries. The final `grub.cfg` should look like this with `<<<<` and `>>>>` marking the changed sections
		```bash
# >>>>>>>>>>
set timeout=10
# <<<<<<<<<<
loadfont unicode

set menu_color_normal=white/black
set menu_color_highlight=black/light-gray
# >>>>>>>>>>
menuentry "Ubuntu autoinstall v2.0" {
	set fgxpayload=keep
	linux /casper/vmlinuz "ds=nocloud;s=/cdrom/nocloud" debug autoinstall ---
	initrd /casper/initrd.gz
}
# <<<<<<<<<<
menuentry "Try or Install Ubuntu" {
	set gfxpayload=keep
	linux /casper/vmlinuz --- quiet splash
	initrd /casper/initrd.gz
}
menuentry "Ubuntu (safe graphics)" {
	set gfxpayload=keep
	linux /casper/vmlinuz nomodeset --- quiet splash
	initrd /casper/initrd.gz
}
grub_platform
if [ "$grub_platform" = "efi" ]; then
menuentry 'Boot from next volume' {
	exit 1
}
menuentry 'UEFI Firmware Settings' {
	fwsetup
}
else
menuentry 'Test memory' {
	linux16 /boot/memtest86+x64.bin
}
fi
```
		So this is how the a `GRUB` config should look like at the end

		7. Save all three files and we are done
	
	7. On clicking next, we have to choose the compression, choose the most compressed, that is, `.xz` ![[Cubic_Compression_Page.png]]
	8. Now, you everything is done, you just have to just sit-back and relax while cubic does the heavy lifting and creates a new `iso` Ubuntu image ![[Cubic_Generate_Page.png]]
	9. This is how your finish looks like, you can test if you want but it will fail most probably due to less permissions, so for that we have our own testing environment ready to be engaged ![[Cubic_Finish_Page.png]] 
8. Ok, so we have created a complete working image, with both late-commands to run and a custom script residing in `/usr/local/bin` now to test all this, start `virt-manager`
	1. Select the `QEMU/KVM` option, `right-click` and select `new` ![[QEMU_setup_1.png]]
	2. Select the 1st option of `Local install media` and click forward ![[QEMU_setup_2.png]]
	3. Select the new image generated from `Cubic` option in the selected folder, uncheck the `Automatically detect ...` option present at the bottom and select `Ubuntu 24.04 LTS` form the drop down menu, then click forward ![[QEMU_setup_3.png]]
	4. select the number of CPU cores to allocate and RAM to allocated ![[QEMU_setup_4.png]]
	5. Choose `Select or create custom storage`, then click on manage. Select you folder where you want the storage, click on the `+` button there, go with default, a volume would be created there. Then select that volume, and click `Choose Volume` ![[QEMU_setup_5.png]]![[QEMU_setup_6.png]]
	6. Next, everything is setup and click `Finish` ![[QEMU_setup_7.png]]
	7. If everything went right, your `VM` will start and will look like this and soon you will see everything running perfectly. ![[QEMU_setup_8.png]]![[QEMU_setup_9.png]]