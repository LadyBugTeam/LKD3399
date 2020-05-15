# 系统编译


## 硬件配置

编译 Android 7.1 开发环境硬件配置建议：

- 64 位 CPU
- 16GB  内存 + 交换内存
- 100GB  空闲空间用来编译， 源码树另占 8GB

另外可参考 Google 官方文档硬件和软件配置：

- [https://source.android.com/setup/build/requirements](https://source.android.com/setup/build/requirements)
- [https://source.android.com/setup/initializing](https://source.android.com/setup/initializing)

## 软件配置

**安装 JDK 8**

``` shell
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install openjdk-8-jdk
``` 

**安装环境包**

``` shell
sudo apt-get install git-core gnupg flex bison gperf libsdl1.2-dev \
  libesd0-dev libwxgtk2.8-dev squashfs-tools build-essential zip curl \
  libncurses5-dev zlib1g-dev pngcrush schedtool libxml2 libxml2-utils \
  xsltproc lzop libc6-dev schedtool g++-multilib lib32z1-dev lib32ncurses5-dev \
  lib32readline-gplv2-dev gcc-multilib libswitch-perl

sudo apt-get install gcc-arm-linux-gnueabihf \
  libssl1.0.0 libssl-dev \
  p7zip-full
``` 

## 下载源码

由于 SDK 较大，请选择以下云盘之一下载 `RK3399-LB_Android7.1.2_git.7z`：

- [Baiduyun](https://pan.baidu.com/s/1eRT6isE "Android 7.1 SDK baiduyun")
- [Google Drive](https://drive.google.com/drive/folders/1N8fpfoeWLD4-VJcYN6Qfh_3-YBYzXxGq "Android 7.1 SDK Google Drive")

下载完成后，在解压前先校验下 MD5 码：

``` shell
$ md5sum /path/to/RK3399-LB_Android7.1.2_git.7z
6d34e51fd7d26e9e141e91b0c564cd1f RK3399-LB_Android7.1.2_git.7z
``` 

然后解压：

``` shell
mkdir -p ~/proj/LKD3399_SDK
cd ~/proj/LKD3399_SDK
7z x /path/to/RK3399-LB_Android7.1.2_git.7z
git reset --hard
``` 

更新远程仓库：

``` shell
git remote rm origin
git remote add gitlab  https://gitlab.com/ladybug/RK3399-Nougat.git
``` 

从 gitlab 处同步源码：

``` shell
git pull gitlab lb3399-dev:lb3399-dev
``` 

## 编译固件

编译之前请先执行如下命令配置好环境变量：

``` shell
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
``` 

**编译 U-Boot**

``` shell
cd u-boot
make rk3399_defconfig
make ARCHV=aarch64 -j16
``` 

**编译内核**

``` shell
cd kernel
make ARCH=arm64 lb3399_android_defconfig
make ARCH=arm64 rk3399-epoch-android.img -j16
``` 

**编译 Android**

``` shell
source build/envsetup.sh
lunch rk3399_all-userdebug
make installclean
make -j16
``` 

## 打包固件

编译完成后, 将所有的分区映像打包成RK整包固件格式用于烧录：

``` shell
./metal.sh
``` 

该脚本会将编译生成的固件拷贝至：IMAGE/RK3399 *****_RELEASE_TEST/IMAGES目录下，具体路径以实际生成为准。每次编译都会新建目录保存，自动备份调试开发过程的固件版本，并存放固件版本的各类信息。该目录下的update.img可直接用于Android开发工具及工厂烧写工具下载更新。


## 分区映像

`update.img` 是发布给最终用户的固件，方便升级开发板。而在实际开发中，更多的时候是修改并烧写单个分区映像文件，这样做大大节省开发时间。

如下是映像文件列表：

- `boot.img`： Android 的 initramfs 映像，包含Android根目录的基础文件系统，它负责初始化和加载系统分区。
- `system.img`： ext4 文件系统格式的 Android 文件系统分区映像。
- `kernel.img`： 内核映像。
- `resource.img`： Resource 映像， 包含启动图片和内核设备树。
- `misc.img`： misc 分区映像， 负责启动模式的切换和急救模式参数的传递。
- `recovery.img`： Recovery 模式映像。
- `rk3399_loader_v1.08.244.bin`： Loader 文件。
- `uboot.img`： U-Boot 映像文件。
- `trust.img`： Arm trusted file (ATF) 映像文件。
- `parameter.txt`： 分区布局和内核命令行。

