# Getting these examples to work on an Azure-CUDA-server

For the purpose of this repository I first point to the original README: 
https://github.com/WangJieying/SDMD-resources/blob/main/README.md

To learn more about image compression, CUDA and git I was interested in getting these examples to work on an Azure-CUDA-server within my MSDN-credits.
As I could only rent a NC6 or NC6 Promo for about (half) a dollar an hour I spent the effort in getting such an 'old' machine to work.
They are built with a NVIDIA GK210 K80 on the Kepler-architecture, which is already abandoned by NVIDIA in the newest drivers.
I was not able to find a matching Ubuntu-image in Azure that provided the right nvidia-driver and Cuda-toolkit, so I guess most of these old CUDA-Azure-machines are left unused at the moment in favor of machines with matching images or even new datacenters for which we don't know how to provide them with enough renewable energy.

CUDA-setup on Ubuntu 20.04 server LTS on a NC6 (Promo) with the package manager can be done with the following commands (done from MobaXterm via ssh):

```
sudo apt-get update
```
```
sudo apt-get install xfce4-session xfce4-goodies build-essential autotools-dev cmake libpng-dev libxext-dev libglvnd-dev pkg-config openjdk-11-jre libpng-dev
```
choose lightDM

Install the Nvidia-driver and CUDA-toolkit via the package manager (resulting in files to be used with CUDAROOT=/usr)
```
sudo apt-get install nvidia-driver-460-server xserver-xorg-video-nvidia-460-server nvidia-cuda-toolkit-gcc
```
```
sudo reboot
```
see the driver work:
```
nvidia-smi

+-----------------------------------------------------------------------------+
| NVIDIA-SMI 460.106.00   Driver Version: 460.106.00   CUDA Version: 11.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla K80           Off  | 00000001:00:00.0 Off |                    0 |
| N/A   34C    P0    71W / 149W |      4MiB / 11441MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A       853      G   /usr/lib/xorg/Xorg                  3MiB |
+-----------------------------------------------------------------------------+
```
see the X-server work correctly on screen :0:
```
vi /etc/X11/xorg.conf
vi /var/log/Xorg.0.log

ps -ef|grep X
root        1670    1664 77 14:41 tty7     00:00:02 /usr/lib/xorg/Xorg -core :0 -seat seat0 -auth /var/run/lightdm/root/:0 -nolisten tcp vt7 -novtswitch

lsmod shows the nvidia-modules:
lsmod|grep nvidia
nvidia_uvm           1011712  0
nvidia_drm             57344  0
nvidia_modeset       1228800  1 nvidia_drm
nvidia              34172928  2 nvidia_uvm,nvidia_modeset
```

Install the prerequisites for these CUDA-examples:
```
sudo apt-get install zpaq libzstd-dev libsnappy-dev libboost-all-dev freeglut3-dev libglew-dev ragel glmark2 valac liblz4-dev liblzma-dev 
```
```
git clone https://github.com/rmast/SDMD-resources.git
```
```
cd SDMD-resources/SourceCode/Code/imConvert/
make
cuda-memcheck ./skeletonify config.txt # runs the program and shows any CUDA error that has to be solved.
cd ../imShow
./show_skeleton output.sir #results in actual R.pgm, G.pgm and B.pgm subpictures
```

