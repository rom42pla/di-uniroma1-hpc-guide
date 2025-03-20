# How to use Slurm

In this page we'll list some common Slurm interactions to run, cancel or monitor jobs.

## How to monitor jobs

### Monitor the status of cluster's nodes
The command `sinfo` can be used to monitor the state of each node, including partitions, time limits, state, and names of the nodes in that partition:

```bash
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
admin        up   infinite      3   idle node[120-122]
debug        up   infinite     16   idle node[103-114,116,123-124,126]
students*    up   infinite      0    n/a
```

For a full list of the meaning of each state, [refer to Slurm's official docs](https://slurm.schedmd.com/sinfo.html#SECTION_NODE-STATE-CODES). Some of the common states are:

- `*`: the node is presently not responding and will not be allocated any new work;
- `ALLOCATED`: the node has been allocated to one or more jobs;
- {`DOWN`, `DRAINED`, `FAIL`, `INVAL`}: the node is unavailable for use. Slurm can automatically place nodes in this state if some failure occurs, so inform an admin ASAP;
-  `DRAINING`: the node is currently allocated a job, but will not be allocated additional jobs;
- {`IDLE`, `MIXED`}: the node is not allocated to any jobs and is available for use;
- `UNKNOWN`: the Slurm controller has just started and the node's state has not yet been determined.

### Monitor the state of jobs
The command `squeue` can be used to view information about jobs located in the Slurm scheduling queue, including job ID, partition, name, user, state, execution time, node name:

```bash
$ squeue
JOBID   PARTITION   NAME    USER    ST  TIME    NODES   NODELIST(REASON)
326     debug       sleep   guest   R   0:03    1       node123
```

The `ST` column shows the states of the jobs. For a full list of the meaning of each state, [refer to Slurm's official docs](https://slurm.schedmd.com/squeue.html#SECTION_JOB-STATE-CODES).
Some of the common states are:

- {`BF`, `F`, `NF`, `PR`}: the job has failed to launch or complete;
- `OOM`: the job experienced out of memory error;
- `R`: the job is running; 
- `CG`: the job is in the process of completing. Some processes on some nodes may still be active.

## How to submit jobs

### How to submit a non-interactive job

A non-interactive job can be run using `srun <commands>`. 
You have to remember to use the `-p` or `--partition` flag, alongside the requirements of the job.

A job with no requirements can be run without further arguments:
```bash
$ srun -p <partition> bash -c "echo 'It works! My name is:'; hostname"
It works! My name is:
node116
```

A job with certain requirements needs all necessary resources to be specified: 
```bash
srun -p <partition> --gpus=2 bash -c "echo 'It works! Here is a summary of my GPUs:'; nvidia-smi"
It works! Here is a brief of my GPUs:
Tue Mar 11 16:13:07 2025
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 570.86.16              Driver Version: 570.86.16      CUDA Version: 12.8     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  Quadro RTX 6000                Off |   00000000:41:00.0 Off |                    0 |
| N/A   27C    P8             20W /  250W |       1MiB /  23040MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
|   1  Quadro RTX 6000                Off |   00000000:C1:00.0 Off |                    0 |
| N/A   25C    P8             13W /  250W |       1MiB /  23040MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|  No running processes found                                                             |
+-----------------------------------------------------------------------------------------+
```

Read [Slurm's official docs](https://slurm.schedmd.com/srun.html) for a list of all the available flags. 
Some common requirements are the following:

- `--gpus`: the minimum number of GPUs the node must have to run the job;
- `--mem`: the real memory required per node, in MBs;

### How to submit a non-interactive container

You can do it using Singularity.
An interactive job can be run using `srun singularity run <container_path> <commands>`.

In this example, the `hostname` command will be executed into the `pytorch` container run on a node:

```bash
$ srun singularity run docker://pytorch/pytorch:2.6.0-cuda12.6-cudnn9-devel hostname
INFO:    Using cached SIF image

==========
== CUDA ==
==========

CUDA Version 12.6.3

Container image Copyright (c) 2016-2023, NVIDIA CORPORATION & AFFILIATES. All rights reserved.

This container image and its contents are governed by the NVIDIA Deep Learning Container License.
By pulling and using the container, you accept the terms and conditions of this license:
https://developer.nvidia.com/ngc/nvidia-deep-learning-container-license

A copy of this license is made available in this container at /NGC-DL-CONTAINER-LICENSE for your convenience.

WARNING: The NVIDIA Driver was not detected.  GPU functionality will not be available.
   Use the NVIDIA Container Toolkit to start this container with GPU support; see
   https://docs.nvidia.com/datacenter/cloud-native/ .

node111
```
### How to submit an interactive container

You can do it using Singularity.
An interactive job can be run using `srun --pty singularity shell <container_path>`, where the `--pty` flag spawns a pseudo-terminal.

In this example, the `pytorch` container will be executed on a node with 2 GPUs. 
The `--nv` flag tells Singularity to mount the GPUs:

```
$ srun --gpus=2 --pty singularity shell --nv docker://pytorch/pytorch:2.6.0-cuda12.6-cudnn9-devel
INFO:    Using cached SIF image
Singularity> nvidia-smi
Wed Mar 19 16:15:07 2025
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 570.124.06             Driver Version: 570.124.06     CUDA Version: 12.8     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  Quadro RTX 6000                Off |   00000000:41:00.0 Off |                    0 |
| N/A   29C    P0             54W /  250W |       1MiB /  23040MiB |      3%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
|   1  Quadro RTX 6000                Off |   00000000:C1:00.0 Off |                    0 |
| N/A   29C    P0             56W /  250W |       1MiB /  23040MiB |      4%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|  No running processes found                                                             |
+-----------------------------------------------------------------------------------------+
Singularity>
```

## How to cancel jobs
### Cancel a job by its job ID
A particular job can be canceled using the `scancel <job_id>` command:

```bash
$ squeue
JOBID   PARTITION   NAME    USER    ST  TIME    NODES   NODELIST(REASON)
326     debug       sleep   debug   R   0:03    1       node123
$ scancel 326
$ squeue
JOBID   PARTITION   NAME    USER    ST  TIME    NODES   NODELIST(REASON)
326     debug       sleep   debug   CG   0:03    1       node123
```

## Cancel all the jobs of a user
All the jobs of a user can be canceled using the `-u` or `--user` flag, by typing `scancel --user <user_id>` command:

```bash
$ squeue
JOBID   PARTITION   NAME    USER    ST  TIME    NODES   NODELIST(REASON)
326     debug       sleep   debug   R   0:03    1       node123
$ scancel --user debug
$ squeue
JOBID   PARTITION   NAME    USER    ST  TIME    NODES   NODELIST(REASON)
326     debug       sleep   debug   CG   0:03    1       node123
```