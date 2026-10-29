---
title: Trantor
---

## Hardware

The cluster includes several different node types, organized in homogeneous groups:

- **Daneel** : 3 nodes, each equipped with 2 Intel Xeon CPUs, 36 cores (18 cores per socket), 1.5 TB of RAM (about 42 GB/core), 6TB local scratch space and 4 Tesla NVIDIA GPUs with 32 GB of RAM (each).
- Hal : 2 nodes, each equipped with 4 Intel Xeon CPUs, 112 cores (28 cores per socket), 3 TB of RAM (about 28 GB/core) and 11TB local scratch space.
- **Helicon** : 14 nodes, each equipped with 2 Intel Xeon CPUs, 12 cores (6 cores per socket), 20 GB of RAM (about 1.7 GB/core). No local scratch space, only a scratch area shared among the nodes.
- **Artes** : 6 nodes, each equipped with 2 Intel Xeon CPUs, 32 cores (16 cores per socket) and 128 GB of RAM (4 GB/core). The scratch space is shared among the nodes. The use of these nodes is restricted. To access, send a request to Professor Chiara Cappelli.

## Storage
Clustered NAS with Infiniband backend network, 40 GE frontend network and 3.0 PB of raw space.

In addition, Trantor acts as front end node for group owned resources, open to computation only to specific groups. See the page " Group owned resources accessible from Trantor " for details.
Running computations on the cluster

All calculations MUST be submitted as Jobs to the Portable Batch System (PBS) scheduling system, for their execution on the compute nodes.

Running interactively on the "head-nodes" is FORBIDDEN. It is also STRICTLY FORBIDDEN to run your computations on the compute nodes bypassing the job submission mechanism.

You can find a brief introduction to PBS at the following Web page: Submitting, inspecting and cancelling PBS Jobs
Scratch Areas

Every user has a scratch space on every computing node, under /scratch/$USER. This area is a temporary storage designed for Jobs’ I/O operations. When possible, this storage area is allocated on the local hard drives of the compute nodes, thus providing a higher bandwidth and a lower latency than NFS mount points. This is the case, for example, of Daneel and Hal nodes. Helicon and Artes, instead, are only equipped with a "shared" scratch area: this is a NFS storage space which is accessible by all the Helicon and Artes nodes.

You can find further details and important notes on the use of scratch areas at the following Web page: Submitting, inspecting and cancelling PBS Jobs - Scratch Areas
Project areas

Is it possible to request additional storage areas on the NAS for storing data related to specific research projects and sharing files among projects members. Such additional storage will be reserved for a limited amount of time (max 1 year).

In the 'Forms' page you can find a form to request, to the Committee and the Staff, the creation of a project area. The request must be submitted by tenured personnel and must include the list of users that can access the area.
Data protection

Our storage system employs a redundant, distributed file system to avoid data loss in the case of a limited hardware failure (disks in a node or entire nodes). Furthermore, snapshots of the content of homes and projects directories are periodically recorded on the storage system and retained for several months, thus allowing to retrieve files that were deleted or overwritten by accident.

Keep in mind, however, that snapshots are not an actual backup mechanism. In fact, while a backup is a full copy of the data stored on a separate storage device (preferably located on a different location), a snapshot is a sort of immutable “photo” of the file system, generated instantaneously and incrementally on the same storage device. With respect to snapshots, backups allow the recovery of the data even in the case of catastrophic events. On the other hand, backups require a separate large-capacity storage device, a significant amount of time and must be performed on data at rest.

Finally, our storage system does not preserve hard-links in snapshots. That means that if multiple files points to the same blocks of data on disk, only one of those files will be preserved in snapshots (you can find a gentle introduction to hard-links here ). That fact implies that, in those scenarios where hard-links are in use, there may be loss of information when recovering files from snapshots (because only one hard-link for each data file would be recovered). Examples of such scenarios include:

{{< callout type="warning" >}}
Software installations that make use of hard-links such as (conda virtual environments).
Hard-links manually created by users.
Files generated as output by applications, if the software creates hard-links (e.g. as a way to avoid data duplication). Fortunately, such cases are quite rare.
{{< /callout >}}

This limitation cannot be circumvented, so please avoid the use of hard-links as much as possible and use soft-links instead.

## Software

Most of the software installed on the cluster is made available by means of Environment Modules . Use the module avail command to get the list of the currently available modules:

``` bash
user@trantor01 ~$ module avail
------------ /cluster/shared/modules/modulefiles/compilers ---------
cmake/3.10.1    gcc/7.3.0    gcc/8.3.0   
cmake/3.18.2    gcc/9.3.0    gcc/10.2.0  

------------- /cluster/shared/modules/modulefiles/libs -------------
blas-lapack/gcc-10.2.0/3.9.0      libint/gcc-8.3.0/2.6.0
boost/gcc-8.3.0/1.74.0            libint/gcc-8.3.0/2.7.0-beta.1
boost/header-only/1.74.0          libint/gcc-8.3.0/2.7.0-beta.6
cuda/10.2                      	  libxc/gcc-8.3.0/5.0.0
cuda/11.0.2                       openmpi/gcc-8.3.0/4.0.4
eigen/3.3.7                       openmpi/gcc-9.3.0/4.0.4
fftw/gcc-8.3.0/3.3.8              openmpi/gcc-10.2.0/4.0.4
fftw/gcc-9.3.0/3.3.8              scalapack/openmpi-4.0.4/gcc-10.2.0/2.1.0
fftw/gcc-10.2.0/3.3.8          

------------ /cluster/shared/modules/modulefiles/apps --------------
gnuplot/5.2.8  gromacs/gcc-8.3.0/2020.3  openbabel/2.4.1
					
```

You can then use the `module load <MODULENAME>` to "load" a specific module. By doing so, your shell environment will be set up to use that particular software. This usually consists in properly setting a few environment variables (such as `PATH`, `CPATH`, `LD_LIBRARY_PATH` etc.) and loading the related dependencies. For example, doing `module load gromacs/gcc-8.3.0/2020.3` will load the software needed to run this version of Gromacs, such as OpenMPI and CUDA. It will also add the binaries path of Gromacs 2020.3 to your environment and set the relative libraries and man paths, plus other variables specific to this software.

It is important to note that the module load command can also be used in job scripts, so to properly set up the environment prior to a computation (more info on jobs submission with PBS here).

Other commonly used module commands are the following:

| Command                        | Action                              |
| :----------------------------- | :---------------------------------- | 
| `module list`                  | prints the currently loaded modules |
| `module unload <MODULENAME>`   | reverts the modifications that were applied to your shell environment during the loading of the specified module |
| `module purge`                 | unloads all the modules             |
| `module help <MODULENAME>`     | prints a concise description of the module |

Please note that there is no module for Python 3 and related libraries, since the Conda utility provides a better alternative. A brief introduction to using Conda on the Trantor cluster can be found here.

### Orca

{{< callout type="info" >}}
ORCA is a flexible, efficient and easy-to-use general purpose tool for quantum chemistry with specific emphasis on spectroscopic properties of open-shell molecules. It features a wide variety of standard quantum chemical methods ranging from semiempirical methods to DFT to single- and multireference correlated ab initio methods. It can also treat environmental and relativistic effects.
{{< /callout >}}

ORCA may be used exclusively for ACADEMIC PURPOSES (academic research and teaching).
It is FORBIDDEN to use this software in the context of cooperation agreements, project work or other collaboration with for-profit organizations, or with governmental and/or non-profit organizations that do not qualify as academia. This includes contract calculations for third parties as well as to share data generated with the software with third parties for other purposes than academic ones.

Publication of data in a scientific journal is expressly permitted. If results obtained with ORCA are published in the scientific literature, you must reference the software as:

F. Neese: Software update: the ORCA program system, version 4.0 (WIREs Comput Mol Sci 2018, 8:e1327. doi: 10.1002/wcms.1327)

Using specific methods included in ORCA requires citing additional articles, as described in the manual.

In order to use the software, you need to register to the ORCA's forum and accept the EULA when prompted. Finally, you will receive a confirmation email that you need to forward to hpcstaff@sns.it.
Compile your software

To compile your software, the first step is to load the module of the desired compiler. Then load the software libraries you need (e.g. OpenMPI, CUDA, LAPACK etc.). For each library, make sure to load a version which has been compiled with the same compiler you plan to use, otherwise you may encounter compatibility issues! To this end, most of the libraries modules contain in their name the compiler they are compatible with. E.g. "fftw/gcc-8.3.0/3.3.8" is the name of the module for using the FFTW library version 3.3.8 compiled with GCC 8.3.0.

Example:
						$ module load gcc/8.3.0
						$ module load fftw/gcc-8.3.0/3.3.8
						$ module load openmpi/gcc-8.3.0/4.0.4
						

If you need a library which is not currently available, or if you need to compile an already existing library with a different compiler, please send a request to the staff (see below).
Intel OneAPI compilers

The Intel OneAPI compilers suite is available on Trantor. You can use it by loading the module intel/2021.1.1 and any relevant module under intel/*.

The licence is "single fixed Multi-Node", meaning that only one person at a time can use the compiler, from any computer.

Since the suite has many tools and libraries, we decided to install only some of them, to avoid cluttering. Please contact us if you need something that is not installed.
JupyterHub@Trantor

A (customized) installation of JupyterHub is available at the following URL: https://jupyter.sns.it

It provides a user-friendly GUI to create one or more Jupyter notebook servers and scheduling their execution as PBS Jobs. If you are interested in using JupyterHub@Trantor, please carefully read the User Guide .
User support

To activate an account to access the Trantor cluster, send an email to HPC Staff. We will provide you a one-time password that you will have to change at first login. Also, please take care to provide an email address (one you actually use) that will be added to the cluster mailing list, so to stay informed about news and maintenance notices.
If you notice any problem, please contact the staff by writing to HPC Staff. Please contact the staff and NOT a single member. If your problem can be redirected to someone in particular, we'll let you know. 