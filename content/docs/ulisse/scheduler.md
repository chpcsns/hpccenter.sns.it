---
title: Submitting Batch Jobs
weight: 20
---

Ulisse uses SLURM as scheduler. For a detailed reference, please refer to the [official documentation](https://slurm.schedmd.com/documentation.html).
In this page you can find can just a quick reference to get started.

### PBS Users
If you are used to another scheduler, as PBS/Torque, you can find a useful cheatsheet [here](https://slurm.schedmd.com/rosetta.pdf) that will 
help you to "translate" the command you already know into SLURM commands.
Moreover, on the headnode, you can find a compatibility layer toolset. 
As a metter of fact you can find most of the usual PBS commands (`qsub`, `qstat`, `qdel`, `pbsnodes`...) and you can use them to run your jobs. 
Be aware that this compatibility layer is not guaranteed to be complete and we strongly recommand to switch to slurm commands as soon as possible.

### New Users
If you are a new user, we strongly recommend you to start from reading the very good [Quick Start Guide](https://slurm.schedmd.com/quickstart.html) that
give you a complete overview of how the scheduler works and how to send your jobs.

Main commands are:
* `sbatch` To submit a job in a queue for later execution
* `scancel` To remove a job from the queue
* `sinfo` To inspect the states of partitions (there is a state name and a symbol for the power state of the node, you can find a legend [here](https://slurm.schedmd.com/sinfo.html#SECTION_NODE-STATE-CODES))
* `squeue` To see the state of the queues (legend of states is available [here](https://slurm.schedmd.com/squeue.html#SECTION_JOB-STATE-CODES))

You can get more detailed informations about how does those commands work by using the `--help` option. 
Here is a [cheatsheet](https://slurm.schedmd.com/pdfs/summary.pdf) you can keep as quick reference 
(it also includes the most common SLURM-set variables).

A simple example of how to run a script in the queue system is:

```bash
sbatch -N 1 -n 48 -p kim -J test_job -d 60 test.sh
```

To simply test that the scheduling system is working you can create test.sh in your home with this content:

```bash
#!/bin/bash

echo "$HOSTNAME" > /home/$USER/test_job.$SLURM_JOBID
sleep 120
```
In this way you are asking a job called `test_job` with a maximum duration of `60` minutes
in the partition `kim` using `1` node and `48` cores (that is, a full `kim` node).

After the sbatch command you will get an output like:
```bash
$ sbatch -N 1 -n 48 -p kim -J test_job -d 60 test.sh
Submitted batch job <JOBID>
```

The job will write a file on your home and then wait for 2 minutes, so you can check that it is in the queue
using `squeue`; if there are many jobs from other user you can simply use `squeue -u $USER` to get only your jobs
or `squeue -j <JOBID>` to get information about the specific job yoyu just submitted.

### Interactive Session
If you need to interactively on a compute node, you can ask the scheduler an interactive session. This can be easily done using srun:
```bash
$ srun -p <PARTITION> -N 1 -n 1 -c <NUMBER OF CPUS> -t <REQUESTED TIME HH:MM:SS> --pty bash
```
the next prompt should be a shell on the node allocated by the scheduler. 

Since the scheduler implement power saving on compute nodes, if the required resource is not already powered-on it
will wait for the compute node to boot and this can take a moment (up to 10 minutes), so if it hangs, try to check 
`squeue` from another shell to see if the process is waiting for the booting node.

### Requesting a Whole Node
It is sometimes useful to request an entire compute node without thinking too much about the amount 
CPUs it has (eg. for benchmarking). In this case the following shortcut can be used:
```bash
$ srun -p <PARTITION> -N 1 --exclusive --mem=<MEMORY OF NODE> ...
```
* `--exclusive` is equivalent to requesting all CPUs in the node
* GPUs should be requested with a separate option.

{{< callout type="warning">}}
  It is sometimes reported that `--mem=0` is equivalent to requesting all RAM in the compute node.
  This configuration is not supported by Ulisse.
{{< /callout >}}

### GPU Nodes
GPUs resources can be accessed via slurm scheduler. The options  used to request GPUs (in `sbatch` or `srun`):
```
-G, --gpus=n            count of GPUs required for the job
--gpus-per-node=n       number of GPUs required per allocated node
--gpus-per-socket=n     number of GPUs required per allocated socket
--gpus-per-task=n       number of GPUs required per spawned task
```

### Power Saving
Since inactive compute nodes still consume a lot of power and generate considerable heat,
Ulisse implement a modern strategy to cut useless power consumption. All compute nodes are kept
in a power-off state by the scheduler and turned on upon scheduler request. After the scheduler
(SLURM) decide to run a job on a compute node, it sends a signal to turn it on, waits for the 
node to boot (this can require from 5 to 10 minutes) and finally runs the job. After the job 
completed SLURM wait one hour before turning the node down (if there is a job ready to start a 
power cycle is avoided). This mechanism drastically reduce the amount of useless energy consumed 
by the cluster at the cost of short wait at the beginning of some compute jobs.



