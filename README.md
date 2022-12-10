# YOLOv7 with GStreamer on Jetson Nano for JetPack4.4

## Prerequisites

- JetPack4.4 - JetPack stable version for Jetson Nano.
- Python3.6 - available on JetPack4.4.
- PyTorch - for Nano series, [here](https://github.com/patharanordev/jetson-nano-gstreamer-yolov7/releases/tag/torch-jetson-nano).

## Onboard Camera

> **IMPORTANT**: Please install common software(below) first.

Ensure your camera work fine :

```sh
mkdir -p ${HOME}/project
cd ${HOME}/project
wget https://gist.githubusercontent.com/jkjung-avt/86b60a7723b97da19f7bfa3cb7d2690e/raw/3dd82662f6b4584c58ba81ecba93dd6f52c3366c/tegra-cam.py
python3 tegra-cam.py
```

To refresh your camera when it failed :

```sh
sudo systemctl restart nvargus-daemon
```

## Installation

### Common

- Software development environment and swap memory

    ```sh
    sudo apt update -y

    # Basic Set-up of the Software
    mkdir -p ${HOME}/project
    cd ${HOME}/project
    git clone https://github.com/jkjung-avt/jetson_nano.git
    cd jetson_nano/
    ./install_basics.sh

    sudo fallocate -l 25G /mnt/25GB.swap
    sudo mkswap /mnt/25GB.swap
    sudo swapon /mnt/25GB.swap

    cat /etc/fstab > fstab
    echo "/mnt/25GB.swap  none  swap  sw 0  0" >> fstab
    sudo mv fstab /etc/fstab

    reboot
    ```

- Common dependencies

    ```sh
    mkdir -p ${HOME}/project/
    sudo apt update -y
    sudo apt install -y build-essential make cmake cmake-curses-gui \
                        git g++ pkg-config curl libfreetype6-dev \
                        libcanberra-gtk-module libcanberra-gtk3-module \
                        python3-dev python3-pip
    sudo pip3 install -U pip==20.2.1 Cython testresources setuptools
    cd ${HOME}/project/jetson_nano
    ./install_protobuf-3.8.0.sh
    sudo pip3 install numpy==1.16.1 matplotlib==3.2.2

    sudo apt install -y libhdf5-serial-dev hdf5-tools libhdf5-dev zlib1g-dev \
                        zip libjpeg8-dev liblapack-dev libblas-dev gfortran
    sudo pip3 install -U numpy==1.16.1 future==0.18.2 mock==3.0.5 h5py==2.10.0 \
                        keras_preprocessing==1.1.1 keras_applications==1.0.8 \
                        gast==0.2.2 futures pybind11
    sudo pip3 install --pre --extra-index-url \
                        https://developer.download.nvidia.com/compute/redist/jp/v44 \
                        tensorflow==1.15.2
    sudo pip3 install Cython
    sudo pip3 install onnx==1.4.1
    ```

### PyTorch

YOLOv7 requires `torch`, to support Jetson Nano :

- prerequisite

    ```sh
    pip3 install typing-extensions==3.6.2.1
    pip3 install typing-extensions --upgrade
    ```

- torch

    ```sh
    wget https://github.com/patharanordev/jetson-nano-gstreamer-yolov7/releases/download/torch-jetson-nano/torch-1.10.0a0+git36449ea-cp36-cp36m-linux_aarch64.whl \
    -O ${HOME}/Downloads/torch-1.10.0a0+git36449ea-cp36-cp36m-linux_aarch64.whl
    pip3 install ${HOME}/Downloads/torch-1.10.0a0+git36449ea-cp36-cp36m-linux_aarch64.whl
    ```

- torchvision

    ```sh
    wget https://github.com/patharanordev/jetson-nano-gstreamer-yolov7/releases/download/torch-jetson-nano/torchvision-0.11.0a0+fa347eb-cp36-cp36m-linux_aarch64.whl \
    -O ${HOME}/Downloads/torchvision-0.11.0a0+fa347eb-cp36-cp36m-linux_aarch64.whl
    pip3 install ${HOME}/Downloads/torchvision-0.11.0a0+fa347eb-cp36-cp36m-linux_aarch64.whl
    ```

### YOLOv7 repo

```sh
cd ${HOME}/project/
git clone https://github.com/patharanordev/jetson-nano-gstreamer-yolov7.git
```

Don't install dependencies in `requirements.txt`, just install 2-dependencies below :

```sh
pip3 install tqdm seaborn
```

## Usage

Run `detect.py` with options below :

- `--source` - focusing on end with "**camerasrc**" to refer to input from GStreamer.
- `--device` - GPU channel, ex. 0, 1, ...
- `--weights` - your model file path.
- `--img-size` - inference size, ex. 640, 416, ...
- `--onboard` - camera port on Jetson board.
- `--view-img` - debug on screen.

```sh
cd ${HOME}/project/jetson-nano-gstreamer-yolov7
python3 ./detect.py \
--source nvarguscamerasrc \
--device 0 \
--weights YOUR_MODEL_NAME.pt \
--img-size INFERENCE_SIZE \
--onboard 0 \
--view-img
```

Example

```sh
$ python3 ./detect.py --source nvarguscamerasrc --device 0 --weights ../yolov7/yolov7-tiny-custom-best.pt --img-size 416 --onboard 0 --view-img
Namespace(agnostic_nms=False, augment=False, classes=None, conf_thres=0.25, copy_frame=False, device='0', do_resize=False, exist_ok=False, gstr=None, height=480, image=None, img_size=416, iou_thres=0.45, name='exp', no_trace=False, nosave=False, onboard=0, project='runs/detect', rtsp=None, rtsp_latency=200, save_conf=False, save_txt=False, source='nvarguscamerasrc', update=False, usb=None, video=None, video_looping=False, view_img=True, weights=['../yolov7/yolov7-tiny-custom-best.pt'], width=640)
YOLOR 🚀 torch-jetson-nano-4-gec7a7dd torch 1.10.0a0+git36449ea CUDA:0 (NVIDIA Tegra X1, 3956.18359375MB)

Fusing layers... 
IDetect.fuse
Model Summary: 208 layers, 6018420 parameters, 0 gradients
 Convert model to Traced-model... 
 traced_script_module saved! 
 model is traced! 

/home/smart/.local/lib/python3.6/site-packages/torch/functional.py:445: UserWarning: torch.meshgrid: in an upcoming release, it will be required to pass the indexing argument. (Triggered internally at  ../aten/src/ATen/native/TensorShape.cpp:2157.)
  return _VF.meshgrid(tensors, **kwargs)  # type: ignore[attr-defined]
Camera: using Jetson onboard camera
GST_ARGUS: Creating output stream
CONSUMER: Waiting until producer is connected...
GST_ARGUS: Available Sensor modes :
GST_ARGUS: 3264 x 2464 FR = 21.000000 fps Duration = 47619048 ; Analog Gain range min 1.000000, max 10.625000; Exposure Range min 13000, max 683709000;

GST_ARGUS: 3264 x 1848 FR = 28.000001 fps Duration = 35714284 ; Analog Gain range min 1.000000, max 10.625000; Exposure Range min 13000, max 683709000;

GST_ARGUS: 1920 x 1080 FR = 29.999999 fps Duration = 33333334 ; Analog Gain range min 1.000000, max 10.625000; Exposure Range min 13000, max 683709000;

GST_ARGUS: 1640 x 1232 FR = 29.999999 fps Duration = 33333334 ; Analog Gain range min 1.000000, max 10.625000; Exposure Range min 13000, max 683709000;

GST_ARGUS: 1280 x 720 FR = 59.999999 fps Duration = 16666667 ; Analog Gain range min 1.000000, max 10.625000; Exposure Range min 13000, max 683709000;

GST_ARGUS: 1280 x 720 FR = 120.000005 fps Duration = 8333333 ; Analog Gain range min 1.000000, max 10.625000; Exposure Range min 13000, max 683709000;

GST_ARGUS: Running with following settings:
   Camera index = 0 
   Camera mode  = 5 
   Output Stream W = 1280 H = 720 
   seconds to Run    = 0 
   Frame Rate = 120.000005 
GST_ARGUS: Setup Complete, Starting captures for 0 seconds
GST_ARGUS: Starting repeat capture requests.
CONSUMER: Producer has connected; continuing.
[ WARN:0] global /home/nvidia/host/build_opencv/nv_opencv/modules/videoio/src/cap_gstreamer.cpp (933) open OpenCV | GStreamer warning: Cannot query video position: status=0, value=-1, duration=-1
...
```

To close debug window, please press '**e**' button :

```sh
...
CONSUMER: Done Success
GST_ARGUS: Cleaning up
GST_ARGUS: Done Success
$
```

## Issue(s)

### Error: fail to open camera.

Please try to refresh camera :

```sh
sudo systemctl restart nvargus-daemon
```

then run `detect.py` again.

## References

- [Official YOLOv7](https://github.com/WongKinYiu/yolov7)