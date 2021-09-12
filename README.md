# julia_gpu_playground

For those who want to use Julia with GPU

# Setup

- If you've installed nvidia-docker2, you can skip this chapter
- We will assume we are using a machine Ubuntu 20.04 with GPU e.g. GTX1080 or GTX1080-ti

```console
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.3 LTS
Release:	20.04
Codename:	focal
$ nvidia-smi
Sun Sep 12 09:32:22 2021       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 470.63.01    Driver Version: 470.63.01    CUDA Version: 11.4     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:03:00.0 Off |                  N/A |
|  0%   43C    P8    11W / 280W |      8MiB / 11178MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
|   1  NVIDIA GeForce ...  Off  | 00000000:04:00.0 Off |                  N/A |
|  0%   45C    P8    12W / 280W |     17MiB / 11177MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
```

## TL;DR

- For those who hesitate to read my English, here are what we should do
    - Install NVIDIA driver
    - Install NVIDIA Container Toolkit
    - Install Docker/Docker Compose
    - Install GNU Make
- That's all. You can skip this chapter.

## Install drivers

```console
$ sudo ubuntu-drivers autoinstall
```

## Install Docker

- Please follow [this instruction](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#)
- After reading this instruction, you'll find the following command works:

```
$ sudo docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
```

## Having trouble ?

Well, the following commands to reset environments might be helpful for you:

```console
$ sudo apt remove --purge "nvidia-*" -y && sudo apt autoremove
$ sudo apt remove --purge "cuda-*" -y && sudo apt autoremove
$ sudo apt remove --purge "libcudnn*" -y && sudo apt autoremove
$ sudo apt remove --purge "libnvidia-*" -y && sudo apt autoremove
```

```console
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```

## set `"default-runtime": "nvidia"`

- Edit [/etc/docker/daemon.json](https://github.com/nvidia/nvidia-container-runtime#daemon-configuration-file) to set `"default-runtime": "nvidia"`
  - See https://stackoverflow.com/questions/59691207/docker-build-with-nvidia-runtime

# How to use this repository

## Building Docker image

```console
$ make
```

## Initialize Docker containerd

```console
$ $ docker-compose run --rm julia
Creating juliagpu_julia_run ... done
               _
   _       _ _(_)_     |  Documentation: https://docs.julialang.org
  (_)     | (_) (_)    |
   _ _   _| |_  __ _   |  Type "?" for help, "]?" for Pkg help.
  | | | | | | |/ _` |  |
  | | |_| | | | (_| |  |  Version 1.6.2 (2021-07-14)
 _/ |\__'_|_|_|\__'_|  |  Official https://julialang.org/ release
|__/                   |

julia> using Flux

julia> x = rand(10) |> gpu
10-element CUDA.CuArray{Float32, 1, CUDA.Mem.DeviceBuffer}:
 0.481513
 0.8270318
 0.27988738
 0.6559783
 0.6754821
 0.084349565
 0.005481618
 0.3933596
 0.4915605
 0.112065755
```

## Initialize JupyterLab

```console
$ docker-compose up lab
Starting gpujl-lab ... done
Attaching to gpujl-lab
# some stuff happen
gpujl-lab  | [I 2021-09-12 09:50:20.717 ServerApp] jupyter_nbextensions_configurator | extension was found and enabled by nbclassic. Consider moving the extension 
gpujl-lab  | [I 2021-09-12 09:50:21.040 ServerApp] Jupyter Server 1.11.0 is running at:
gpujl-lab  | [I 2021-09-12 09:50:21.040 ServerApp] http://blahblah:8888/lab?token=xxxxxxxxxxxxxxxxx
gpujl-lab  | [I 2021-09-12 09:50:21.040 ServerApp]  or http://127.0.0.1:8888/lab?token=xxxxxxxxxxxxxxxxx
gpujl-lab  | [I 2021-09-12 09:50:21.040 ServerApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
gpujl-lab  | [W 2021-09-12 09:50:21.043 ServerApp] No web browser found: could not locate runnable browser.
gpujl-lab  | [C 2021-09-12 09:50:21.044 ServerApp]
gpujl-lab  |
gpujl-lab  |     To access the server, open this file in a browser:
gpujl-lab  |         file:///home/jovyan/.local/share/jupyter/runtime/jpserver-1-open.html
gpujl-lab  |     Or copy and paste one of these URLs:
gpujl-lab  |         http://blahblah:8888/lab?token=xxxxxxxxxxxxxxxxx
gpujl-lab  |      or http://127.0.0.1:8888/lab?token=xxxxxxxxxxxxxxxxx
```

Then open your browser and go to `http://127.0.0.1:8888/lab?token=xxxxxxxxxxxxxxxxx`
