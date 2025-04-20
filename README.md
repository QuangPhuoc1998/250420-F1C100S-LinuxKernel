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

---
---

<h1>
Enable LCD display (1024*600 pixel)
</h1>

You can check available LCD support on "250420-F1C100S-LinuxKernel/linux/drivers/gpu/drm/panel/panel-simple.c"

Example with 7 inchs 1024*600 LCD, You can use

```C
static const struct drm_display_mode auo_b101aw03_mode = {
	.clock = 51450,
	.hdisplay = 1024,
	.hsync_start = 1024 + 156,
	.hsync_end = 1024 + 156 + 8,
	.htotal = 1024 + 156 + 8 + 156,
	.vdisplay = 600,
	.vsync_start = 600 + 16,
	.vsync_end = 600 + 16 + 6,
	.vtotal = 600 + 16 + 6 + 16,
	.vrefresh = 60,
};

static const struct panel_desc auo_b101aw03 = {
	.modes = &auo_b101aw03_mode,
	.num_modes = 1,
	.bpc = 6,
	.size = {
		.width = 223,
		.height = 125,
	},
};
```

Update 250420-F1C100S-LinuxKernel/linux/scripts/dtc/include-prefixes/arm/suniv-f1c100s-licheepi-nano.dts to below:

```bash
Change:
/***/
	panel: panel {
		compatible = "lg,lb070wv8", "simple-panel";

		#address-cells = <1>;
		#size-cells = <0>;
		enable-gpios = <&pio 4 6 GPIO_ACTIVE_HIGH>;
		power-supply = <&reg_vcc3v3>;

 		port@0 {
			reg = <0>;
			#address-cells = <1>;
			#size-cells = <0>;

 			panel_input: endpoint@0 {
				reg = <0>;
				remote-endpoint = <&tcon0_out_lcd>;
			};
		};
	};
/***/    
To
/***/
	panel: panel {
		//compatible = "lg,lb070wv8", "simple-panel";   //  Comment this line
		compatible = "auo,b101aw03", "simple-panel";    // Add this line
		
		#address-cells = <1>;
		#size-cells = <0>;
		enable-gpios = <&pio 4 6 GPIO_ACTIVE_HIGH>;
		power-supply = <&reg_vcc3v3>;

 		port@0 {
			reg = <0>;
			#address-cells = <1>;
			#size-cells = <0>;

 			panel_input: endpoint@0 {
				reg = <0>;
				remote-endpoint = <&tcon0_out_lcd>;
			};
		};
	};    
/***/
```