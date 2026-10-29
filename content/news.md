---
title: "News"
---

#### May 12, 2022 - Matlab Parallel Server
MATLAB Parallel Server is now available on the Trantor cluster, 
allowing to execute long-running CPU and/or GPU intensive 
computations on high-performance compute nodes.  
Jobs submission and results retrieval is performed directly
from the MATLAB instance running on your personal workstation.

Refer to the [**user guide**](/page/wiki/pages/Running_MATLAB_computations_on_Trantor.pdf)
for details on the required configuration and instructions on how
to submit jobs and retrieve results.

---

#### March 29, 2022 - JupyterHub improvements
A new version of our customized JupyterHub installation is now available!  
Release notes:

- Notebooks can now be run on any compute node you have access to.
- You can now provide a bash script to be executed before starting the notebook server, e.g. with the purpose of initializing the environment.
- Several usability improvements and bug fixes.

Please report any problem to hpcstaff@sns.it.

---

#### February 8, 2022 - New compute nodes: Anacreon and Cinna
Two new groups of nodes has been added to Trantor, "Anacreon" and "Cinna".  
Technical specifications are listed [here](../wiki/pages/Group_owned_resources_on_Trantor.html).

**Access to Anacreon and Cinna nodes is restricted respectively to members of the "*bioinfo*" and "*compnanobio*" groups.**

Use the corresponding *q07anacreon*, *q14anacreon*, *q07cinna* and *q14cinna* PBS queues to submit jobs to these nodes (see [here](../wiki/pages/Submitting_Inspecting_and_Cancelling_PBS_Jobs.html) for details).

---

#### January 13, 2022 - Cluster stop from January 31 to February 2
Due to a maintenance intervention on the cooling system in San Silvestro, there will be a cluster shutdown from Monday January 31 10:00 AM to Wednesday February 2.

**Please be sure to terminate all jobs and recover any data you may need before Monday January 31 10:00 AM.**

---

#### December 17, 2021 - Important limitations of the storage system's snapshotting feature
The native snapshotting mechanism our storage system does not support the preservation of **hard-links** in snapshots.
That means that if multiple files points to the same blocks of data on disk, **only one of those files will be preserved in snapshots** (you can find a gentle introduction to hard-links [here](https://www.redhat.com/sysadmin/linking-linux-explained)).

That fact implies that, in those scenarios where hard-links are in use, there may be loss of information when recovering files from snapshots (since only one hard-link for each data file would be recovered). Examples of such scenarios include:

- Software installations that make use of hard-links (such as **conda virtual environments**).
- Hard-links manually created by users.
- Files generated as output by applications, if the software creates hard-links (e.g. as a way to avoid data duplication). Fortunately, such cases are quite rare.

Unfortunately, there is nothing we can do to overcome this limitation, so please **avoid the use of hard-links as much as possible and use soft-links instead**.

---

#### October 11, 2021 - New head node with remote desktop
A new head-node equipped with the Xfce desktop environment is now available: *trantor03.sns.it*

You can connect to the new head-node via ssh (as usual) or by opening a remote desktop session. Detailed instructions are discussed in the [wiki](../wiki/pages/Connecting_to_the_head_node.html#Getting_started_X2Go).

---

#### September 15, 2021 - New compute node: Kalgan01
A new node (DELL R750) has been added to Trantor.
It is named *kalgan01* and equipped with 2 Intel Xeon Platinum 8380 CPU @ 2.30GHz (40 physical cores per socket), 2 TB of memory and a scratch area of 8.7 TB.

The corresponding *q07kalgan* e *q14kalgan* PBS queues have been created.

**Access to Kalgan01 is restricted to members of the "*kalgan*" group**.

---

#### August 16, 2021 - New Aurora nodes
A group of 11 new nodes (HPDL650 Gen10) has been added to Trantor.
They are named *aurora[01-11]* and are equipped with 4 Intel Xeon 6252N @2.3 GHz (24 physical cores per socket), 512 GB of memory and a scratch area of 4.9 TB (with 750 GB of SSD cache).

The corresponding *q07aurora* e *q14aurora* PBS queues have been created.

**Access to Aurora nodes is restricted to members of the "*diamond*" group**.

---

#### April 12, 2021 - Request of creation of a storage area destined to a research project
In the ['Forms'](../forms/index.html) page you can find a form to request, to the Committee and the Staff, additional storage for a specific research project.
Such additional storage will be reserved for a limited amount of time (max 1 year).

The creation of a new area must be proposed by tenured personnel and must include the list of users that can share read/write access to the area.

You can download the form [here](../forms/pages/project_area_request.docx).

---

#### March 18, 2021 - Changes to PBS queues settings
We changed the settings related to the maximum number of running and queued jobs (per user) accepted within a queue, as follows:

- **q07daneel**: max 2 running jobs and 8 queued jobs per user.
- **q14daneel**: max 2 running jobs and 8 queued jobs per user.
- **q07hal**: max 1 running job and 4 queued jobs per user.
- **q14hal**: max 1 running job and 4 queued jobs per user.
- **q07helicon**: max 4 running jobs per user. No limits on queued jobs.
- **q14helicon**: max 4 running jobs per user. No limits on queued jobs.
- **q07artes**: max 3 running jobs per user. No limits on queued jobs.
- **q14artes**: max 3 running jobs per user. No limits on queued jobs.
- **q07diamond**: max 10 running jobs per user. No limits on queued jobs.
- **q14diamond**: max 7 running jobs per user. No limits on queued jobs.

---

#### March 8, 2021 - New Intel compilers suite available on Trantor
The Intel OneAPI compilers suite is now available on Trantor.
You can use it by loading the module `intel/2021.1.1` and any relevant module under `intel/*`.

**The licence is "single fixed Multi-Node", meaning that only one person at a time can use the compiler, from any computer.**

Since the suite has many tools and libraries, we decided to install only some of them, to avoid cluttering.
Please contact us if you need something that is not installed.

---

#### Dec 23st, 2020 - Singularity is now available on daneels and hals
[Singularity Community Edition](https://sylabs.io/singularity/) is now available on the Daneels and Hals nodes of the Trantor cluster.  
For details, please refer to the [dedicated section of the wiki](../wiki/pages/notes_on_singularity.html).

---

#### Dec 23st, 2020 - Daneel01 temporarily back in the queues
Daneel01 has been put back in the queues; a few caveats on this node:

1. GPU0 is detected but does not work correctly, so be sure not to use it in your applications.
2. We started a maintenance call on this; likely the replacement will be available in january 2021, but in any case consider that we may be forced to shut it down with very short notice.

---

#### Dec 21st, 2020 - Hal nodes
The hal01 and hal02 nodes (Quad socket Dell R840 with 56 core Xeon Platinum 8280, 3.0 TB of memory and 9 TB of scratch) are now available in the Trantor cluster; the q02hal and q07hal queues have been created.

Note that even if these nodes have a lot of powerful cores scaling and running multiple jobs has not been up to expectations, so consider the current queue and node setting experimental.

---

#### Dec 7th, 2020 - artes MPI nodes
A new group of nodes, called `artes0[1-6]` (dual Xeon(R) Gold 5218 CPU @ 2.30GHz, 32 cores, 128 GB memory ) is now open to computation in the Trantor cluster, for people in the `diamond` group, set up for MPI type jobs.

---

#### Dec 1st, 2020 - R support in JupyterHub
JupyterHub@Trantor ([https://jupyter.sns.it](https://jupyter.sns.it)) now supports R-based notebooks.  
In order to exploit this feature, you have to create an R-based Conda environment as explained in the updated [User Guide](https://hpccenter.sns.it/page/wiki/pages/JupyterhubTrantor-UserGuide.pdf).  
In addition, the "[Brief introduction to Conda](https://hpccenter.sns.it/page/wiki/pages/A_brief_introduction_to_using_Conda_on_the_cluster.pdf)" has also been updated with instructions on creating R-based environments.  
As before, the access to JupyterHub@Trantor must be explicitly enabled, so if you are interested send a request to hpcstaff@sns.it.
