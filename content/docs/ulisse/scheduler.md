---
title: Submitting Batch Jobs
weight: 20
---
{{< callout type="warning">}}
  Interactive Batch Jobs are currently not supported.
{{< /callout >}}

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




