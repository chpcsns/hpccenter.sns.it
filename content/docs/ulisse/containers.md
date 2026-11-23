---
title: Using Containers
weight: 40
------

Ulisse supports the usage of container-shipped software via [Apptainer](https://apptainer.org/).
Apptainer is a container platform designed to run complex applications on HPC clusters in a simple, portable, reproducible and escalation-safe way. You can build or download a container and then run it on different systems (within and outside Ulisse).
[Here](https://apptainer.org/docs/user/quick_start.html) you can find a beginner guide to Apptainer;  
for any more advanced topic you can refer to the [official documentation](https://apptainer.org/docs).
Apptainer is supported on both CPU and GPU nodes of the Ulisse cluster.

## Pulling a Container

You can download pre-built containers from public registries such as [Docker Hub](https://hub.docker.com/) and save them as `.sif` files on the cluster.

### Example: pulling and running the lolcow container

First, pull the container and save it as a `.sif` file on your home or project directory:

```bash
mkdir -p ~/apptainer_images
apptainer pull ~/apptainer_images/lolcow.sif library://london.mirror/apptainer/lolcow:latest
```

Then run it interactively on a compute node:

```bash
apptainer run ~/apptainer_images/lolcow.sif
```

This will print a fun message from inside the container.

## Running a Container

Apptainer is available directly on the cluster without needing to load any module.

### Interactive session

For testing or interactive use, you can start a container interactively on a compute node:

```bash
apptainer shell <path-to-singularity-file>
```

For example, to start a container stored in your `apptainer_images` directory:

```bash
apptainer shell ~/apptainer_images/my-container.sif
```

### Batch job

For production workloads, submit a batch job with `sbatch`. Below is a minimal example (`run-job.sh`):

```bash
#!/bin/bash
#SBATCH --job-name=my-container-job
#SBATCH --output=job_%j.out
#SBATCH --error=job_%j.err
#SBATCH --time=01:00:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=8G

# Run the container
apptainer run <PATH-TO-SIF-FILE> <arguments>
```

Then submit it with:

```bash
sbatch run-job.sh
```

The container has access to your home directory, shared project directories, and scratch storage areas.

## Running a Container with GPU support

To use GPUs inside a container, add the `--nv` flag to your Apptainer command. This passes through the NVIDIA drivers and GPU devices into the container.

### Verifying GPU access

A simple way to verify that GPU support works is to run an NVIDIA CUDA container and check for the driver:

```bash
apptainer run --nv docker://nvidia/cuda:12.2.0-base-ubuntu20.04 nvidia-smi
```

Or, to run an interactive shell with GPU passthrough:

```bash
apptainer shell --nv docker://nvidia/cuda:12.2.0-base-ubuntu20.04
```

Once inside, you can run `nvidia-smi` to see your GPU(s).

### Interactive session

For general container runs with GPU support:

```bash
apptainer run --nv <PATH-TO-SIF-FILE>
```

### Batch job

Add the GPU request to your `sbatch` script:

```bash
#!/bin/bash
#SBATCH --job-name=my-gpu-job
#SBATCH --output=job_%j.out
#SBATCH --error=job_%j.err
#SBATCH --time=01:00:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=8G
#SBATCH --gres=gpu:1          # request 1 GPU

# Run the container with GPU support
apptainer run --nv <PATH-TO-SIF-FILE> <arguments>
```

Make sure to request GPUs with `--gres=gpu:<N>` in your `sbatch` script, matching the number of GPUs the container needs.

## Building a Container

If you need to build your own container, the easiest way is to define a `.def` file and build it directly using `apptainer build` ([see documentation](https://apptainer.org/docs/user/main/cli/apptainer_build.html)).
For a full reference on container definitions, consult the [Apptainer documentation](https://apptainer.org/docs/user/latest/definition_files.html).