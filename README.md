# Yocto_RaspberryPi
我們可以利用Yocto build一個簡單的image在RaspberryPi上使用，省去傳統boot、kernel、roofs各種複雜的操作。
## Dependencies
sudo apt-get install texinfo  
sudo apt-get install lzop  
sudo apt-get install liblz4-tool  
## Downloads and Install Environment
mkdir yocto  
cd yocto  
git clone git://git.yoctoproject.org/poky.git  
cd poky  
git clone git://git.yoctoproject.org/meta-raspberrypi  
. oe-init-build-env build  

Poky裡面有一些Embedded system的基本Layer，可以幫助我們build，並下載Raspberrypi Layer，接著設定Environment，build也可以替換成你喜歡的目錄名稱。

在build/conf下面修改local.conf  
```
MACHINE ??= "raspberrypi3"    //如果是pi4請改為raspberrypi4  
GPU_MEM = "16"  
IMAGE_FSTYPES += " rpi-sdimg"    //因為要燒入sd卡須生成sdimg檔  
```

在build/conf下面修改bblayers.conf，此動作可以將raspberrypi layer加入其中  
```
BBLAYERS ?= " \
  /YOUR/PATH/yocto/poky/meta \
  /YOUR/PATH/yocto/poky/meta-poky \
  /YOUR/PATH/yocto/poky/meta-yocto-bsp \
  /YOUR/PATH/yocto/poky/meta-raspberrypi \
  "
```
## Build
修改好之後回到build目錄下利用bitbake build image
bitbake rpi-basic-image

build成功後image檔會在/YOUR/PATH/poky/build/tmp/deploy/images/raspberrypi4下面

`rpi-basic-image-raspberrypi4.rpi-sdimg`

## Burn sd card
請利用balneaEtcher燒入至sd卡中，若使用Raspberry Pi Imager燒入請將附檔名修改為img  

燒入後，將sd卡中的cmdlie.txt新增下面兩句  
console=serial0,115200 console=tty1  
ip = 192.168.137.72  

config.txt新增下面內容  
start_x=1  
gpu_mem=128  
enable_uart=1   //後面會利用uart連接Raspberrypi，所以開啟  
dtoverlay=w1-gpio   //如已有此dtoverlay設定，可以不用新增  

最後使用USB轉UART轉接頭連接Raspberrypi和電腦，並使用tera term或putty與Raspberrypi連線，buad rate需設為115200，即可在console下面看到build完的linux

## Reference
https://www.cnx-software.com/2015/02/27/yocto-project-raspberry-pi-2-board-minimal-image/  
https://www.balena.io/etcher/  
https://www.yoctoproject.org/docs/current/mega-manual/mega-manual.html#var-CORE_IMAGE_EXTRA_INSTALL  
https://ttssh2.osdn.jp/index.html.en  
