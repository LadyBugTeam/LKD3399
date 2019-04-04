# Buildroot

## 系统编译

### 硬件配置

- 64 位 CPU
- 16GB  内存 + 交换内存
- 40GB  空闲空间用来编译，如果您进行多个构建，将需要更大的硬盘空间

### 软件配置

Buildroot Linux SDK 是在 Ubuntu 16.04 上开发测试的。因此，我们推荐使用Ubuntu 16.04 的系统进行编译。其他版本没有具体测试，可能需要对软件包做相应调整。

**安装uboot/kernel编译所需的依赖包**

``` shell
sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl \
  zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev \
  x11proto-core-dev libx11-dev lib32z1-dev ccache libgl1-mesa-dev libxml2-utils \
  xsltproc unzip device-tree-compiler liblz4-tool
``` 

**安装buildroot编译所需依赖包**

``` shell
sudo apt-get install libfile-which-perl sed make binutils gcc g++ bash patch gzip \
  bzip2 perl tar cpio python unzip rsync file bc libmpc3 git repo texinfo pkg-config cmake tree texinfo
``` 
若编译遇到报错，可以视报错信息，安装对应的软件包。

### 下载源码

``` shell
mkdir LB3399
cd LB3399
repo init -u https://github.com/rockchip-linux/manifests -b master -m rk3399_linux_release.xml
repo sync
``` 

代码将开始自动下载，后面只需耐心等待。初始同步操作将需要 1 个小时或更长时间才能完成。


### 脚本编译

为了提高编译的效率，降低人工编译可能出现的误操作，该 SDK 中集成了全自动化编译脚本，方便固件编译、备份。强烈建议使用脚本编译。

``` shell
cd LB3399
./build.sh
``` 

该脚本会自动配置环境变量，编译U-Boot，编译Kernel，编译Buildroot，编译Recovery， 继而生成供烧录的整包固件。
脚本会将编译生成的固件拷贝至：
IMAGE/RK3399_****_RELEASE_TEST/IMAGES 目录下，具体路径以实际生成为准。每次编译都会新建目录保存，自动备份调试开发过程的固件版本，并存放固件版本的各类信息。

### 手动编译


**编译 U-Boot**

``` shell
cd u-boot
./make.sh evb-rk3399
``` 
编译完，会生成 trust.img、rk3399_loader_v1.12.112.bin、uboot.img 三个镜像文件。

**编译内核**

``` shell
cd kernel
make ARCH=arm64 ladybug_linux_defconfig
make ARCH=arm64 rk3399-epoch-linux.img -j16
``` 
编译完成后，kernel 根目录，生成 boot.img 镜像文件。

**编译 Buildroot**

``` shell
source envsetup.sh  //选择对应的序号
make -j16
./mkfirmware.sh
``` 

`mkfirmware.sh` 会将所有烧写所需的镜像将都拷贝到`rockdev`目录。

### 分区映像

如下是映像文件列表：

- `boot.img`： 内核映像 + Resource 映像，包含启动图片和内核设备树。
- `misc.img`： misc 分区映像， 负责启动模式的切换和急救模式参数的传递。
- `parameter.txt`： 分区布局和内核命令行。
- `recovery.img`： Recovery 模式映像。
- `MiniLoaderAll.bin`： Loader 文件。
- `oem.img`： OEM 映像文件。
- `userdata.img`： userdata 映像文件。
- `rootfs.img`： ext4 文件系统格式的 buildroot 文件系统分区映像。
- `uboot.img`： U-Boot 映像文件。
- `trust.img`： Arm trusted file (ATF) 映像文件。
