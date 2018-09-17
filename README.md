# 3DOD_thesis

Code will be released before the end of September.

- Youtube video of results (https://youtu.be/KdrHLXpYYlg):
[![demo video with results](https://img.youtube.com/vi/KdrHLXpYYlg/0.jpg)](https://www.youtube.com/watch?v=KdrHLXpYYlg)

******
## Training on Microsoft Azure:

To train the model, I used an NC6 virtual machine on Microsoft Azure. Below I have listed what I needed to do in order to get started, and some things I found useful. For reference, my username was 'fregu856':
- Download KITTI (data_object_image_2.zip and data_object_label_2.zip).

- Install docker-ce:
- - $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
- - $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
- - $ sudo apt-get update
- - $ sudo apt-get install -y docker-ce

- Install CUDA drivers (see "Install CUDA drivers for NC VMs" in https://docs.microsoft.com/en-us/azure/virtual-machines/linux/n-series-driver-setup):
- - $ CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
- - $ wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 
- - $ sudo dpkg -i /tmp/${CUDA_REPO_PKG}
- - $ rm -f /tmp/${CUDA_REPO_PKG}
- - $ sudo apt-get update
- - $ sudo apt-get install cuda-drivers
- - Reboot the VM

- Install nvidia-docker:
- - $ wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.1/nvidia-docker_1.0.1-1_amd64.deb
- - $ sudo dpkg -i /tmp/nvidia-docker*.deb && rm /tmp/nvidia-docker*.deb
- - $ sudo nvidia-docker run --rm nvidia/cuda nvidia-smi

- Download the latest TensorFlow docker image with GPU support (tensorflow 1.3):
- - $ sudo docker pull tensorflow/tensorflow:latest-gpu

- Create start_docker_image.sh containing:
```
#!/bin/bash

# DEFAULT VALUES
GPUIDS="0"
NAME="fregu856_GPU"


NV_GPU="$GPUIDS" nvidia-docker run -it --rm \
        -p 5584:5584 \
        --name "$NAME""$GPUIDS" \
        -v /home/fregu856:/root/ \
        tensorflow/tensorflow:latest-gpu bash
```

- /root/ will now be mapped to /home/fregu856 (i.e., $ cd -- takes you to the regular home folder). 

- To start the image:
- - $ sudo sh start_docker_image.sh 
- To commit changes to the image:
- - Open a new terminal window.
- - $ sudo docker commit fregu856_GPU0 tensorflow/tensorflow:latest-gpu
- To stop the image when it’s running:
- - $ sudo docker stop fregu856_GPU0
- To exit the image without killing running code:
- - Ctrl-P + Q
- To get back into a running image:
- - $ sudo docker attach fregu856_GPU0
- To open more than one terminal window at the same time:
- - $ sudo docker exec -it fregu856_GPU0 bash

- To install the needed software inside the docker image:
- - $ apt-get update
- - $ apt-get install nano
- - $ apt-get install sudo
- - $ apt-get install wget
- - $ sudo apt-get install libopencv-dev python-opencv
- - Commit changes to the image (otherwise, the installed packages will be removed at exit!)