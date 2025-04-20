<h1>
Linux for F1C100S Processer from Allwinner
</h1>

Visit https://ohmbedkernel.substack.com/ to read more articles about F1C100S

<h2>
1. Install the necessary library packages
</h2>

```bash
sudo apt-get install gcc make cmake rsync wget unzip build-essential git bc swig libncurses-dev libpython3-dev libssl-dev python3-distutils android-tools-mkbootimg -y
```

<h2>
2. Download Linux source
</h2>

```bash
git clone -b nano-5.2-tf https://github.com/Lichee-Pi/linux.git
cd linux
```

<h2>
3. Compile the linux kernel
</h2>

<h4>
Export the path first
</h4>

```bash
export ARCH=arm
export CROSS_COMPILE=arm-buildroot-linux-gnueabi-
export PATH=$PATH:~/250420-F1C100S-Builroot-2022.02/output/host/bin/
```

```bash
wget http://dl.sipeed.com/LICHEE/Nano/SDK/config
cp config .config
make ARCH=arm menuconfig
make -jx
# where x is the number of threads your computer can run.
```
<h2>
4. Output
</h2>

After compile completed, you will have the bootloader file for F1C100S:<br>
ZImage: arch/arm/boot/zImage /mnt/<br>
Device tree: arch/arm/boot/dts/suniv-f1c100s-licheepi-nano.dtb /mnt/