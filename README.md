
# 1. flash the TX2
## 1.1 download jetpack L4T 3.0

Follow [flash-small-TX2](https://github.com/gaowenliang/flash-small-TX2 "flash-small-TX2")
to flash the TX2

Warning:

* do not install OpenCV4Tegra V2.4 for TX2, we will use OpenCV3
* for TX2 you may need to flash it tiwce, one just flash the OS, the other install cuda
* if the tx1/tx2 cannot boot the GUI, but shut down after finish loading BIOS, try to change a more powerful power source

After flash, the system can be launch.

username: nvidia

password: nvidia

# 2. Install basic tools

connect host and TX2 on the same local network, install arp-scan and
run: `sudo arp-scan --interface=eth0 192.168.1.0/24 | grep NVIDIA'
find your TX2 device.

ssh to your TX2 and run:

`sudo apt-get install git`

`mkdir setup`

`cd setup`

`git clone https://github.com/gaowenliang/TX2-TX1-setup.git`

`sudo ./install.sh`

`sudo reboot`

to install USB device and reboot.

run one by one:

`sudo apt-get install terminator -y`


`sudo apt-get install cmake -y`

`sudo apt-get install vim -y`

`sudo apt-get install htop`

# 3. Install ROS

follow [ROS](http://wiki.ros.org/Installation/Ubuntu "ROS")

follow [installROSTX2](https://github.com/gaowenliang/installROSTX2 "installROSTX2")

# 4. Eigen manuly
## install Eigen stable release V3.3.3

follow [eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page "eigen")

* it will fail 6 ctest cases out of 798
* or if you build BTL, fail 13 ctest cases out of 832

host:

`scp ~/Downloads/eigen-eigen-67e894c6cd8f.tar.bz2 ubuntu@<YOUR_SSH PATH>:~/`

TX2:
`cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local -DCMAKE_INSTALL_PREFIX=/usr/local -DEIGEN_TEST_CXX11=ON -DEIGEN_CUDA_COMPUTE_ARCH=62 -DEIGEN_BUILD_BTL=ON`

`make check`
this step will take really a long time. Not necessary to check.

Then run:
`sudo make install`

# 5. ceres manly
normal install, without eigen, need pass all ctest cases

typical Ceres test bench mark is 131s for TX2

ceres-solver.org/ceres-solver-1.12.0.tar.gz

`sudo apt-get install libgoogle-glog-dev`

`sudo apt-get install libatlas-base-dev -y`

`sudo apt-get install libsuitesparse-dev -y`

`tar zxf ceres-solver-1.12.0.tar.gz`

`mkdir ceres-bin`

`cd ceres-bin`

`cmake ../ceres-solver-1.12.0 -DCMAKE_INSTALL_PREFIX=/usr/local` 

`make -j4`

`make test`

`make install`

# 6. Download buildOpencvTX2, remove libeigen3-dev, install Opencv manuly

follow [buildOpenCVTX2](https://github.com/gaowenliang/buildOpenCVTX2 "buildOpenCVTX2")
in opencv V3.2.0
opencv_test_cudev will fail, but acturally, it success, ctest made a wrong test compare between 2.0 and 2.
 
## SDL
`sudo apt-get install libsdl-image1.2-dev 

and

sudo apt-get install libsdl-dev

# some useful scropts
monitor the CPU temperture

`watch -n 1 cat /sys/devices/virtual/thermal/thermal_zone1/temp`

check CUDA version

`nvcc -V`

 

### use Manifold2 board USBs

# test Ethernet speeed

```
$ sudo apt-get install iperf
#start server
$ sudo iperf -s
#start client
$ sudo iperf -c 192.168.1.xxx -i 5
```

# install SSH keys
```
ssh-copy-id user@123.45.56.78
```

# bash tools

## devlist
devlist is 
```
aliased to `sudo arp-scan --interface=eth0 192.168.1.0/24 | grep NVIDIA'
```

## dev 
devlist is aliased to `sudo arp-scan --interface=eth0 192.168.1.0/24 | grep NVIDIA'
dev is a function
```
dev () 
{ 
    local ip="$(sudo arp-scan --interface=eth0 192.168.1.0/24 | grep NVIDIA | sed -n $1p | cut -d'	' -f 1)";
    echo "ssh into $ip";
    ssh nvidia@$ip
}
```

# max_performance.sh

```
nvidia@tegra-ubuntu:~$ cat max_performance.sh 
echo "Enabling fan for safety..."
if [ ! -w /sys/kernel/debug/tegra_fan/target_pwm ] ; then
echo "Cannot set fan -- exiting..."
fi
echo 255 > /sys/kernel/debug/tegra_fan/target_pwm

nvpmodel -m 0
./jetson_clocks.sh
```
