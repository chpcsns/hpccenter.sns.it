---
title: PBS
---

## About job scheduling systems
A "job" is essentially a set of commands to be executed on the compute nodes, together with a specification of the computational resources (number of nodes, number of processors per node, amount of RAM memory per node, execution time, etc.) to be reserved for their execution.

Job requests are submitted to a job scheduling system (e.g. PBS). The main purpose of the job scheduler is to dispatch jobs to compute nodes for execution, trying both to maximize and balance the utilization of the computational resources.

Once submitted, the job is appended to a "queue" in which it will be pending until there will be enough free resources to allow its execution. It is common for a cluster to provide different queues, each associated to a different subset of compute nodes, a different maximum execution time, etc.

{{< callout type="warning" >}}

**IMPORTANT NOTE**

All calculations **MUST** be submitted as Jobs to the **Portable Batch System (PBS) scheduling system**, for their execution on the compute nodes.

**Running interactively on the "head-nodes" is FORBIDDEN.**
**It is also STRICTLY FORBIDDEN to run your computations on the compute nodes bypassing the job submission mechanism.**

{{< /callout >}}

## Brief introduction to PBS
Foreword: the following instructions have been written for BASH; however adapting them to CSH or TCSH is straightforward.

Remember that you can always access the online manuals using the command `man`. Also, we strongly suggest to read the PBS user manual, you can download it from the [Altair website](https://www.altair.com/pdfs/pbsworks/PBSUserGuide19.2.3.pdf).

### What is a PBS job and its basic commands
In this section we are going to submit our first **PBS job**. A PBS job is simply a shell script, possibly with some PBS directives. PBS directives look like shell comments, so the shell itself ignores them, but PBS picks them up and processes the job accordingly. A BASH scripts usually begins with `#!/bin/bash`, or if you prefer TCSH `#!/bin/tcsh`.

We are going to start by submitting a very simple shell script, that executes two Unix commands and then exits; it doesn't have any PBS directives. The script must be executable:

```bash
$ pwd
/home/hpcstaff/PBS

$ ls -l
-rw-r--r--  1 hpcstaff hpcstaff   76 Dec  2 22:50 job.sh

$ cat job.sh
#!/bin/bash
hostname
date
exit 0

$ chmod +x job.sh
```

#### qsub
The job is submitted with the command `qsub`:

```bash
$ qsub -q q07daneel job.sh
12248.pbs01
```

The command output is its job ID. It's the same ID that appears in the first column of `qstat` listing (the qstat command is discussed in the next section). The standard output (STDOUT) of a job is written on a file that, at the end of the job's execution, is copied in the same directory from which the job was submitted. Standard error (STDERR) is also returned on another file in the same directory:

```bash
$ ls
job.sh  job.sh.e12248  job.sh.o12248
```

The output file name has this format:

```bash
job name + .o + job ID number
```

The same goes for the errors file name, with `.e` instead of `.o`. In our example the error file is empty and the standard output file contains:

```bash
$ cat job.sh.o12248
daneel03
Sun Sep  7 16:27:25 CEST 2015
```

and this tells us that the job was run on `daneel03`.

Our job executes so fast that we can hardly catch it in action. We are going to slow it down by letting it sleep for a hundred seconds before exiting. Here is our modified version.

```bash
$ cat job.sh
#!/bin/bash
hostname
date
sleep 100
date
exit 0
```

And just to make sure that it's not going to hang forever, we are going to execute it interactively and check that it sleeps for 100 seconds only:

```bash
$ time ./job.sh
trantor01
Sun Sep  7 16:53:41 CEST 2015
Sun Sep  7 16:55:21 CEST 2015

real	1m40.029s
user	0m0.000s
sys 	0m0.010s
```

This worked just fine: the job took 1 minute and 40 seconds, which is 100 seconds, to execute. Now we are going to submit it with `qsub`:

```bash
$ qsub -q q07daneel job.sh
12259.pbs01
```

Now, how can you check its state?

#### qstat
The `qstat` command is used to get information about jobs and queues. To inspect the state of a specific job, run `qstat` with the job ID as argument:

```bash
$ qstat 12259.pbs01
Job id            Name             User              Time Use S Queue
----------------  ---------------- ----------------  -------- - -----
12259.pbs01       job.sh           hpcstaff          00:00:29 R daneel03
```

Pay attention to the 5th column, which reports the status of the job. "R" means that the job is running. Others common status values are the following:

| Status | Description |
|:------:|-------------|
| **E** | the job is exiting after having run |
| **H** | the job is held - this means that it is not going to run until it is released |
| **Q** | the job is queued and will run when the requested resources will become available |
| **R** | the job is running |
| **T** | the job is being transferred to a new location - this may happen, e.g., if the node the job had been running on crashed |
| **W** | the job is waiting to be executed at a later time - you can specify a time after which the job is eligible to run (see section "6.8 Deferring Execution" of the [PBS User's Guide](https://www.altair.com/pdfs/pbsworks/PBSUserGuide19.2.3.pdf) for details) |

Other commonly used arguments for the `qstat` command are the following:

| Command | Description |
|:--------|-------------|
| `qstat -n1` | lists all jobs on system, together with their execution host and running time (walltime) |
| `qstat -n1 jobid` | displays the state of the specified job, its running time (walltime) and the execution host |
| `qstat -s jobid` | displays the state of the specified job, followed by any comment added by the administrator or scheduler |
| `qstat -f jobid` | displays full information about the status of a job |
| `qstat -fx jobid` | displays full information about a finished job |
| `qstat -u username` | lists all jobs owned by the specified user |
| `qstat -q` | lists the available queues, with details about the maximum resources that can be requested by jobs submitted in each queue |
| `qstat -Q` | lists the available queues, with details about their status (whether the queue is enabled, number of running jobs, number of queued jobs, etc.) |
| `qstat -Qf queuename` | displays full information about the status of a queue |
| `qstat -B` | lists summary information about the PBS server |

#### qdel
To delete a job use `qdel`. If the job is running, the command sends `SIGKILL` to it. If the job is merely queued, the command removes it from the queue. Here's an example:

```bash
$ qsub -q q07daneel job.sh
12390.pbs01

$ qdel 12390.pbs01

$ ls
job.sh  job.sh.e12390  job.sh.o12390

$ cat job.sh.o12390
daneel02
Sun Sep  7 17:26:01 CEST 2015
```

#### pbsnodes
To check the availability of a node that you want to request for your submission, you can use:

| Command | Description |
|:--------|-------------|
| `pbsnodes -a` | lists all nodes with their features and current state (free, job-busy, stale, etc.). It's quite verbose. |
| `pbsnodes <nodeid>` | displays features and state of the specified node |
| `pbsnodes -l` | lists all DOWN and OFFLINE nodes (which you can't request immediately) |

## Resources and queues
When you submit a job, you ask for a certain amount of resources to be reserved for that job's execution. There are two types of resources that can be requested: "chunk" resources and "job-wide" resources.

A "chunk" is a collection of host-level resources (such as a given number of CPUs, a given amount of memory, etc.) that are reserved as a unit. The "chunk" is used by the portion of the job which runs on the host on which the resources have been allocated. These resources are requested inside the `select` statement.

"Job-wide" are resources that apply to the entire job, such as the cpu-time or the walltime. These resources are requested outside the `select` statement. You can request for resources by means of dedicated directives within the job script, or by exploiting the `-l` option of the qsub command.

As an example, consider the following job script:

```bash
#!/bin/bash
#PBS -l select=1:ncpus=2:ngpus=1:mem=8096mb
#PBS -l walltime=03:00:00
#PBS -q q07daneel
#PBS -N sleep
sleep 3h
```

In this case, the job needs 1 "vnode" (i.e. compute node), 2 CPUs, 1 GPU and 8GB of RAM to run. Also, the user is requesting that the job is queued to a specific queue (q07daneel in the example) with the `-q` option. Please note that on our cluster the queue name **must** be selected since there is no default queue. `-N` gives a name to the job.

Of course, you can request resources directly within the qsub command:

```bash
$ qsub -l select=1:ncpus=2:ngpus=1:mem=8gb -q q07daneel [...] job.sh
```

The following list describes the resources that can be requested for a job and their default values:

- **ncpus** ["chunk" resource] - The number of logical CPU cores to be reserved. Default value: "1".
- **mem** ["chunk" resource] - The amount of RAM memory to be reserved. Default value: "2048mb".
- **ngpus** ["chunk" resource] - The number of GPUs to be reserved. Default value: "0".
  > *Note: this resource is available only on q07daneel and q14daneel queues.*
- **mpiprocs** ["chunk" resource] - Number of MPI processes for the chunk.
- **host** ["chunk" resource] - The compute node on which the job must be executed.
  > *Note: please avoid forcing the execution of your job on a specific host unless you have very good reasons for doing so!*
- **walltime** ["job-wide" resource] - The maximum execution time for the Job. It's default value depends on the selected queue. If the Job is still executing when the requested walltime expires, it will be killed.
  > *Note: Usually there is no need to explicitly request this resource. Just rely on the queue's limit.*

The resources that have been requested for a job are reserved at the Linux Kernel level when the Job starts its execution, and are not available to other users. Likewise, your job can only use the resources you requested for it. In particular:

- Even if your Job spawns more processes / threads than the number of CPU cores that have been assigned to it, all these processes / threads will be executed only by the assigned CPU cores.
  > *Note: from your code/script you can retrieve the number of cores assigned to the job by reading the **NCPUS** environment variable.*
- On Daneel nodes, you'll be able to use only the GPUs you requested for the job. Note that while your program may see all the installed GPUs, the ones you don't reserved will be detected as "incompatible" (or something similar). When possible, avoid passing to your program/library the IDs of the GPUs to be used. Instead, let the program/library detect the available GPUs by itself.
- As regards to RAM memory, a "soft" limit is enforced: as long as there is enough free memory on the system, the job is allowed to allocate more memory than the reserved amount. However, in the case of shortage of memory, the memory pages of the processes that violate the limit will be swapped out to disk. This usually leads to a sensible slowdown of your computation and, if also the swap area on disk gets filled, to the kill of your job.

It is also important to note that, on each node, ~4GB of RAM are reserved for the Operating System. As a consequence, **the maximum amount of memory you can request for a job is about 4.5 GB less than the total amount of RAM installed on the node**. As an example, you should not request more than 15.5 GB of memory for a job intended to be run on an Helicon node, since these nodes are equipped with 20GB of RAM.

The available queues are listed below. Queues are characterized by the hosts the jobs will be scheduled to and by the maximum execution time ("walltime").

| Queue | Execution hosts | Max walltime | Limits | Interactive |
|:------|-----------------|:------------:|:-------|:-----------:|
| **q02daneel** | Daneel nodes | 2 days | 6 running jobs per user, no limits on queued jobs | **allowed** |
| **q07daneel** | Daneel nodes | 7 days | 2 running and 10 queued jobs per user | denied |
| **q14daneel** | Daneel nodes | 14 days | 2 running and 10 queued jobs per user | denied |
| **q02hal** | Hal nodes | 2 days | 6 running jobs per user, no limits on queued jobs | **allowed** |
| **q07hal** | Hal nodes | 7 days | 1 running and 5 queued jobs per user | denied |
| **q14hal** | Hal nodes | 14 days | 1 running and 5 queued jobs per user | denied |
| **q02helicon** | Helicon nodes | 2 days | 6 running jobs per user, no limits on queued jobs | **allowed** |
| **q07helicon** | Helicon nodes | 7 days | 4 running jobs per user, no limits on queued jobs | denied |
| **q14helicon** | Helicon nodes | 14 days | 4 running jobs per user, no limits on queued jobs | denied |
| **q07artes** | Artes nodes ([artes](#) group) | 7 days | 3 running jobs per user, no limits on queued jobs | **allowed** |
| **q14artes** | Artes nodes ([artes](#) group) | 14 days | 3 running jobs per user, no limits on queued jobs | **allowed** |
| **q07diamond** | Diamond nodes ([diamond](#) group) | 7 days | 10 running jobs per user, no limits on queued jobs | **allowed** |
| **q14diamond** | Diamond nodes ([diamond](#) group) | 14 days | 7 running jobs per user, no limits on queued jobs | **allowed** |
| **q07aurora** | Aurora nodes ([diamond](#) group) | 7 days | 3 running jobs per user, no limits on queued jobs | **allowed** |
| **q14aurora** | Aurora nodes ([diamond](#) group) | 14 days | 3 running jobs per user, no limits on queued jobs | **allowed** |
| **q07kalgan** | Kalgan nodes ([kalgan](#) group) | 7 days | 3 running jobs per user, no limits on queued jobs | **allowed** |
| **q14kalgan** | Kalgan nodes ([kalgan](#) group) | 14 days | 3 running jobs per user, no limits on queued jobs | **allowed** |
| **q05hypnos** | Hypnos nodes ([interstellar](#) group) | 5 days | 3 running jobs per user and 6 queued jobs | **allowed** |
| **q05oromasdes** | oromasdes03 (previously called Ananke) ([astro](#) group) | 5 days | 3 running jobs per user, no limits on queued jobs | **allowed** |
| **q07cinna** | Cinna nodes ([compnanobio](#) group) | 7 days | 3 running jobs per user, no limits on queued jobs | **allowed** |
| **q14cinna** | Cinna nodes ([compnanobio](#) group) | 14 days | 3 running jobs per user, no limits on queued jobs | **allowed** |
| **q02anacreon** | Anacreon nodes ([bioinfo](#) group) | 2 days | No limits on per-user running and queued jobs | **allowed** |
| **q07anacreon** | Anacreon nodes ([bioinfo](#) group) | 7 days | 2 running jobs per user, no limits on queued jobs | denied |
| **q14anacreon** | Anacreon nodes ([bioinfo](#) group) | 14 days | 2 running jobs per user, no limits on queued jobs | denied |
| **q02gaia** | Gaia nodes ([bioinfo](#) group) | 2 days | No limits on per-user running and queued jobs | **allowed** |
| **q07gaia** | Gaia nodes ([bioinfo](#) group) | 7 days | 2 running jobs per user, no limits on queued jobs | denied |
| **q14gaia** | Gaia nodes ([bioinfo](#) group) | 14 days | 2 running jobs per user, no limits on queued jobs | denied |

<u><i>Please try to select nodes and queues according to the real necessities of your calculation, and avoid crowding on the newest nodes only. Also, remember to specify a queue, since there is no default queue on our cluster.</i></u>

For further information, please consult the official [PBS Pro user guide](https://www.altair.com/pdfs/pbsworks/PBSUserGuide19.2.3.pdf).

## Scratch Areas
Every user has a scratch space on every computing node, under `/scratch/$USER`. This area is a temporary storage designed for Jobs' I/O operations.

When possible, this storage area is allocated on the *local hard drives* of the compute nodes, thus providing a *higher bandwidth and a lower latency* than NFS mount points. This is the case, for example, of Daneel and Hal nodes. Helicon and Artes, instead, are only equipped with a "*shared scratch area*": this is a NFS storage space which is accessible by all the Helicon and Artes nodes.

**A few important notes on the use of scratch areas**:

- **You should perform I/O operations on this area and, when the computation is over, copy any relevant output file back to your home directory or project folder.**
- **At the end of your computation, remember to copy any relevant file stored in the scratch folder back into your home or project folder.** In fact, **local scratch areas are not backed up and can be erased by the technical staff** for maintenance purposes.
- **Remember to clean up your scratch folder by deleting the files you don't need anymore** (especially if you killed your job with `qdel`). Note that some computational software creates very big temporary files, so it is mandatory that you clean your scratch area from unnecessary files at least every month. The staff will delete them anyway, when necessary, without further notice.
- **Using your home directory or a project folder as a scratch space is strictly forbidden!**

Here's a recap of the scratch areas available in each nodes group:

| Node group | Scratch space |
|:-----------|:-------------|
| **Daneel** | 6TB local scratch |
| **Hal** | 11TB local scratch |
| **Diamond** | 3TB local scratch |
| **Aurora** | 4.9TB local scratch |
| **Kalgan** | 8.7TB local scratch |
| **Artes and Helicons** | 25TB per-user **shared** scratch |
| **Oromasdes03** (previously called Ananke) | 1.1TB local scratch |
| **Hypnos** | 1.7TB local scratch (mounted under `/scratch`) plus an additional 9.8TB **shared** scratch area with SSD cache (mounted under `/scratch_ssd`) |
| **Cinna** | small local scratch area |
| **Anacreon** | small local scratch area |
| **Gaia01** | 890GB local scratch |

You can use the following commands on the computing node to check how much space you are currently using:

```bash
$ cd /scratch/$USER
$ pwd
/scratch/yourusername
$ du -hs (this command could take a while)
7.5G
```

## FAQ

### Requesting an interactive session, the correct way to directly access a node
If you need to directly access a computation node, for example to test your job, you can use the flag `-I` (that is a capital i) of qsub to request an interactive session. For example:

```bash
$ qsub -I -q q07daneel -l select=1:ncpus=4
```

{{< callout type="warning" >}}

**IMPORTANT NOTE:**

Interactive sessions are forbidden on the 7 and 14 days long queues for the following node groups:
- **daneel**
- **hal**
- **helicon**
- **anacreon**
- **gaia**

If you want to start an interactive session on these nodes, **use the 2 days long queues** instead.

{{< /callout >}}

### I made a bad evaluation of my job resources
Job resources (the ones you previously asked with qsub, ex. `-l walltime=xx:xx:xx`) can be fixed by using the `qalter` command. For example:

```bash
$ qalter -l walltime=xx:xx:xx jobid
```

You can use `qalter` command just to **decrement** the usage of a job resource. If you need to **increment a resource**, email us at hpcstaff@sns.it and explain your reasons. Then, the staff will proceed with the increase.

### What if my execution machine has gone off-line?
If the PBS server shuts down, or in the case of network issues between the server and the compute nodes, the jobs will continue their execution. Of course, in the event of a malfunction of a compute node, all the Jobs assigned to that node will be terminated. It is important to note that, in this last case, the Jobs on the dead node may still appear as "**Running**". If you want to check if your job is **effectively** running, proceed as follows:

```bash
$ qstat -u $USER
```

For each job id you want to check, type:

```bash
$ qstat -f <job ID> | grep exec_host
```

This will print the slave machine on which the job is **presumably** running. So, log into that server and check if there is actually some processes of yours:

```bash
$ top -u $USER
```

If your job is not effectively running, you should provide the deletion of your own jobs with the following command:

```bash
$ qdel -W force <job ID>
```

**Final note:** PBS can be configured so to automatically re-queued the Jobs affected by a malfunctioning event. However, this behaviour may lead to several other (tricky) problems so it has been disabled (the "-r n" flags are automatically added to every qsub).
