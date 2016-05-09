# CM 13.x 编译流程

## 编译环境搭建

### 操作系统选择
推荐使用 Linux 操作系统，比如 Ubuntu，版本不要太新也不要太旧，比如 16.04 默认使用的是 JDK8，而且官方软件仓库里也没有 JDK7 的源了，只能通过添加 PPA 源安装到 JDK7，而且很多软件的名字也变了，14.04 这个是比较推荐的版本，不管是 Android 还是 CyanogenMod 都是推荐使用这个版本。

我自己的实际情况是，本地编译的话使用的是 ArchLinux，这个是我的日常使用系统，服务器上编译的话使用的是 Ubuntu 15.10，这个跟 14.04 差别很小，也就一个所需的软件包名字不一样而已。

### 软件包安装
选择好一个操作系统后开始安装编译环境，主要就是一些软件的安装。

Ubuntu 的话：
```
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y bison build-essential curl flex git gnupg gperf libesd0-dev libncurses5-dev libsdl1.2-dev libwxgtk2.8-dev libxml2 libxml2-utils lzop openjdk-7-jdk openjdk-7-jre pngcrush schedtool squashfs-tools xsltproc zip zlib1g-dev g++-multilib gcc-multilib lib32ncurses5-dev lib32readline-dev lib32z1-dev python2.7 python2.7-dev python-pip liblzma-dev bc unzip

pip install backports.lzma
```

ArchLinux 的话比较麻烦，可以参考[官方 Wiki](https://wiki.archlinux.org/index.php/android)。
#### 安装编译软件
```
sudo pacman -Syu

# 32-bit and 64-bit systems:
sudo pacman -Sy gcc git gnupg flex bison gperf sdl wxgtk squashfs-tools curl ncurses zlib schedtool perl-switch zip unzip libxslt python2-virtualenv bc rsync maven jdk7-openjdk

# 64-bit systems only
sudo pacman -Sy gcc-multilib lib32-zlib lib32-ncurses lib32-readline

# AUR
yaourt libtinfo
yaourt ncurses5-compat-libs
yaourt lib32-ncurses5-compat-libs
```

#### 设置 repo
```
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/repo
chmod a+x ~/repo
sudo mv ~/repo /usr/bin
```

#### 设置目录
```
mkdir -p ~/CodeLife/src/cm-13.x
cd ~/CodeLife/src/cm-13.x
```

#### 设置 Python
ArchLinux 自带了 Python 3，但是编译 Android 只能使用 Python2，所以需要使用 virtualenv2 来实现临时切换到 Python2。
```
virtualenv2 ~/venv
source ~/venv/bin/active
pip install backports.lzma
```

#### 下载安卓源码
```
cd ~/CodeLife/src/cm-13.x
repo init -u git://github.com/CyanogenMod/android.git -b stable/cm-13.0-ZNH2K
mkdir .repo/local_manifests
wget -q https://raw.githubusercontent.com/YumeMichi/android/master/onyx/cm-13.0-stable/local_manifest.xml .repo/local_manifests
repo sync -j4 --force-sync --force-broken
```

## 编译源码
```
. build/envsetup.sh
lunch cm_onyx-userdebug
make bacon -j8
```

## 检验成果
编译完的 ROM 在 ~/CodeLife/src/cm-13.x/out/target/product/onyx 下，名为 cm-13.x-xxxx-STABLE-YumeMichi-onyx.zip，拿起手机刷起～