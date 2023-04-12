# picam2-rpi-opencv

## Motivation

There are several guides out there which demonstrate how the Pi Camera can be used for performing tasks like object detection using OpenCV and [picamera](https://github.com/waveform80/picamera) on Raspberry Pi.

However, the [picamera](https://github.com/waveform80/picamera) module on Bullseye may not detect or recognize camera devices that do not use the legacy camera interface, such as the Pi Camera Module 2. In addition, these cameras however have the tendency to work very well with [libcamera](https://github.com/raspberrypi/libcamera), which is not supported by picamera.

Fortunately, developers can use the [picamera2](https://github.com/raspberrypi/picamera2) module instead. However, at the time this guide was made, the module is still at beta (v0.3.9 Beta Release 8) and there is no highly stable API documentation yet.

This serves as a guide on how to set up camera devices like the Pi Camera Module 2 for compatibility with OpenCV and Raspberry Pi OS (Bullsye).

## Guide

**Note: this guide was tested on the Pi Camera Module 2. This may not be an effective guide if you are using a USB camera.**

You may first need to check whether your camera device can be detected:

```sh
# vcgencmd
vcgencmd get_camera

# via V4L2 (apt install v4l-utils)
v4l2-ctl --list-devices

# libcamera
libcamera-still --list-cameras
```

Based on which one works for your camera device, you may want to check if images/videos can be captured. For example, if libcamera works well for your device, then a simple `libcamera-still` can help you confirm that.

**If your camera works well with libcamera, then this guide is for you.**

Camera devices such as the Pi Camera Module 2 may work well with libcamera. However, this is not supported by `picamera`. In this case, you want to use the `picamera2` module, which supports libcamera.

You need to set up the required development headers:

```sh
sudo apt install libcap2 libcap-dev
```

You also need to set up PyQt5, OpenGL, and other packages such as ffmpeg and prctl:
```sh
sudo apt install -y python3-pyqt5 python3-opengl python3-kms++ python3-prctl libatlas-base-dev ffmpeg
sudo apt install qtbase5-dev
pip install pyqt5
```

If you are having issues in installing PyQt5, you may try the following:

```sh
pip install pyqt5 --config-settings --confirm-license= --verbose
```

Then to set up picamera2:
```sh
sudo apt install -y python3-picamera2 python3-libcamera
pip install numpy picamera2 picamera2[gui]
```

Once you are done with picamera2, you want to set up [libcamera](https://github.com/raspberrypi/libcamera) from source:
```sh
# Meson
pip install meson

# libcamera core
apt install libyaml-dev python3-yaml python3-ply python3-jinja2
# if you are in a virtual environment:
pip install jinja2 ply pyyaml ninja

# IPA module signing
apt install libgnutls28-dev libssl-dev openssl

# lc-compliance
apt install libevent-dev

# libcamera build
git clone https://git.libcamera.org/libcamera/libcamera.git
cd libcamera
meson setup build
ninja -C build install
```

The following may also be helpful depending on your requirements:
```sh
# Debugging
apt install libdw-dev libunwind-dev

# Device Hotplug Enumeration
apt install libudev-dev

# Documentation (optional)
python3-sphinx doxygen graphviz texlive-latex-extra

# Gstreamer (if you want to use gstreamer)
libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev

# Camera: KMS sink
apt install libdrm-dev
# Camera: MJPEG on SDL sink
apt install libjpeg-dev
# Camera: SDL link
apt install libsdl2-dev

# QCam
qtbase5-dev libqt5core5a libqt5gui5 libqt5widgets5 qttools5-dev-tools libtiff-dev

# Tracing with lttng
liblttng-ust-dev python3-jinja2 lttng-tools

# Android
libexif-dev libjpeg-dev
```

To verify your libcamera setup:
```sh
cam
```