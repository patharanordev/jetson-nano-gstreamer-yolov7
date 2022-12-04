# YOLOv7 with GStreamer on Jetson Nano for JetPack4.4

## Prerequisites

- JetPack4.4 - JetPack stable version for Jetson Nano.
- Python3.6 - available on JetPack4.4.
- PyTorch - for Nano series, [here](https://github.com/patharanordev/jetson-nano-gstreamer-yolov7/releases/tag/torch-jetson-nano).

## Installation

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

## References

- [Official YOLOv7](https://github.com/WongKinYiu/yolov7)