---
title: Usage of Singularity
---



[Singularity](https://sylabs.io/singularity/) is a container platform designed to run complex applications on HPC clusters in a simple, portable, reproducible and escalation-safe way. You can build a container using Singularity on your workstation, and then run it on the compute nodes of the Trantor cluster.

[Here](https://singularity-tutorial.github.io/) you can find a very good tutorial about Singularity, for any more advanced topic you can refer to the [official user guide](https://sylabs.io/guides/3.6/user-guide/index.html).

Currently, you can run Singularity containers on the following nodes:

- Daneel
- Hal
- Helicon
- Hypnos
- Oromasdes03

Please note that, due to technical and security reasons, you are not allowed to build containers on the cluster. At this time, the suggested workflow is the following:

{{% steps %}}

### Build the container on your workstation as a SIF file.

### Copy the SIF file in your home directory on Trantor (e.g. by using scp or sftp).

### Run the container on compute nodes by submitting a PBS job.

{{% /steps %}}

Due to known incompatibilities with NFS, you should not convert SIF images to "sandboxes" on the Trantor cluster (e.g. via the singularity build or the singularity run --writable commands). Running SIF images from NFS locations (such as your home directory) is the best and safe way.

CUDA-based containers can be run only on daneel01-03 nodes (the only nodes equipped with GPUs). To this end, you need to provide the "--nv" flag when executing the singularity shell or the singularity run commands. Also, do not forget to reserve one or more GPUs by explicitly requesting "ngpus" resources when submitting the job (for details: Submitting, inspecting and cancelling PBS Jobs ).

To create a container for a CUDA-based application, it is recommended to start from the [CUDA Docker images](https://hub.docker.com/r/nvidia/cuda) provided by NVIDIA.

You have to pay close attention to choose an image with an associated CUDA version equal to, or lower than, the one supported by the drivers currently installed on the Daneels. You can find the maximum CUDA version supported by the drivers by running the nvidia-smi command on a Daneel node. Then, to find the tag name associated to the required CUDA image, click on the "Tags" tab on the top of the https://hub.docker.com/r/nvidia/cuda web page and filter the list by entering the desired CUDA version in the search box. You can finally download the image by using the following command:

```bash
singularity pull docker://nvidia/cuda:tag_name
```

As an example, to download the image with the "devel" flavour of CUDA 10.2 based on Ubuntu 18.4 you have to execute the following command:

``` bash
singularity pull docker://nvidia/cuda:10.2-devel-ubuntu18.04
```

Please note that support for CUDA-based containers should be considered experimental. If you find any problem, please report them to [hpcstaff@sns.it](mailto:hpcstaff@sns.it)