
# Jetson NX with JetPack-4.4

Thanks to ([JK Jung](https://jkjung-avt.github.io/jetpack-4.4/)) for providing most of the scripts.
This repo holds the scripts/programs to set up the software development environment on Jetson NX.

March, 16, 2021: Supporting Jetpack 4.4


For Jetson NX
```shell
$ sudo nvpmodel -m 2
$ sudo jetson_clocks 
```
An alternative is to mouse-click power mode options on the top-right corner of the Ubuntu desktop.

The “protobuf” libraries could have a noticeable effect on the performance (inference speed) of tensorflow or else. 

```shell
### Install dependencies for python3 "cv2"
$ sudo apt-get update
$ sudo apt-get install -y build-essential make cmake cmake-curses-gui \
                          git g++ pkg-config curl libfreetype6-dev \
                          libcanberra-gtk-module libcanberra-gtk3-module \
                          python3-dev python3-testresources python3-pip
$ sudo pip3 install -U pip Cython
$ cd ${HOME}/project/jetson_nano
$ ./install_protobuf-3.8.0.sh
$ sudo pip3 install numpy matplotlib
```
## Testing camera and cv2
```shell
$python3 tegra-cam.py --usb --vid 0
```
## Install DeepStream

DeepStream is an optimized graph architecture built using the open source GStreamer framework. The graph below shows a typical video analytic application starting from input video to outputting insights.
All the individual blocks are various plugins that are used. At the bottom are the different hardware engines that are utilized throughout the application. 
Optimum memory management with zero-memory copy between plugins and the use of various accelerators ensure the highest performance.

![Deepstream](https://github.com/T-DevH/Jetson-NX/blob/master/images/DS_overview_graph_architecture.png)

Enter the following commands to install the prerequisite packages:

```shell
$ sudo apt install \
libssl1.0.0 \
libgstreamer1.0-0 \
gstreamer1.0-tools \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly \
gstreamer1.0-libav \
libgstrtspserver-1.0-0 \
libjansson4=2.11-1
```

Install librdkafka (to enable Kafka protocol adaptor for message broker):

```shell
$ git clone https://github.com/edenhill/librdkafka.git
```

Configure and build the library:

```shell
$ cd librdkafka
$ git reset --hard 7101c2310341ab3f4675fc565f64f0967e135a6a
./configure
$ make
$ sudo make install
```

Copy the generated libraries to the deepstream directory:

```shell
$ sudo mkdir -p /opt/nvidia/deepstream/deepstream-5.1/lib
$ sudo cp /usr/local/lib/librdkafka* /opt/nvidia/deepstream/deepstream-5.1/lib
```

The next step is to install Deepstream SDK. I am using debian package that can be downloaded from the nvidia develper website. 

```shell
$ sudo apt-get install ./deepstream-5.1_5.1.0-1_arm64.deb
```

Run deepstream-app (the reference application):

```shell
deepstream-app -c /opt/nvidia/deepstream/deepstream-5.0/samples/configs/deepstream-app/source1_usb_dec_infer_resnet_int8.txt
```



 
