# What is Slurm?
Slurm is a system that helps manage and run jobs on a group of computer *nodes* called a *cluster*. 
It makes sure that the right jobs are running on the right computers at the right time.

![Slurm's architecture](assets/images/slurm_arch.png)

Slurm has three main purposes:

- *Allocates* exclusive and/or non-exclusive *access to resources* (compute nodes) to users for some duration of time so they can perform work;
- *Provides a framework* for starting, executing, and monitoring work (normally a parallel job) on the set of allocated nodes;
- *Arbitrates contention* for resources by managing a queue of pending work;

## Slurm's internal structure
Slurm consists of a set of daemons:
- The `slurmctld` daemon, a centralized manager to monitor resources and work. There may also be a backup manager to assume those responsibilities in the event of failure;
- The `slurmd` daemon running on each compute node, which provide fault-tolerant hierarchical communications and lets users interact with the cluster. Basically: it waits for work, executes that work, returns status, and waits for more work;
- The optional `slurmdbd` daemon, which can be used to record accounting information for multiple Slurm-managed clusters in a single database;
- The optional `slurmrestd` daemon which can be used to interact with Slurm through its REST API.

## Slurm commands
All Slurm commands start with `s`:

- `sacct` is used to report job or job step accounting information about active or completed jobs.

- `salloc` is used to allocate resources for a job in real time. Typically this is used to allocate resources and spawn a shell. The shell is then used to execute srun commands to launch parallel tasks.

- `sattach` is used to attach standard input, output, and error plus signal capabilities to a currently running job or job step. One can attach to and detach from jobs multiple times.

- `sbatch` is used to submit a job script for later execution. The script will typically contain one or more srun commands to launch parallel tasks.

- `sbcast` is used to transfer a file from local disk to local disk on the nodes allocated to a job. This can be used to effectively use diskless compute nodes or provide improved performance relative to a shared file system.

- `scancel` is used to cancel a pending or running job or job step. It can also be used to send an arbitrary signal to all processes associated with a running job or job step.

- `scontrol` is the administrative tool used to view and/or modify Slurm state. Note that many scontrol commands can only be executed as user root.

- `sinfo` reports the state of partitions and nodes managed by Slurm. It has a wide variety of filtering, sorting, and formatting options.

- `sprio` is used to display a detailed view of the components affecting a job's priority.

- `squeue` reports the state of jobs or job steps. It has a wide variety of filtering, sorting, and formatting options. By default, it reports the running jobs in priority order and then the pending jobs in priority order.

- `srun` is used to submit a job for execution or initiate job steps in real time. srun has a wide variety of options to specify resource requirements, including: minimum and maximum node count, processor count, specific nodes to use or not use, and specific node characteristics (so much memory, disk space, certain required features, etc.). A job can contain multiple job steps executing sequentially or in parallel on independent or shared resources within the job's node allocation.

- `sshare` displays detailed information about fairshare usage on the cluster. Note that this is only viable when using the priority/multifactor plugin.

- `sstat` is used to get information about the resources utilized by a running job or job step.

- `strigger` is used to set, get or view event triggers. Event triggers include things such as nodes going down or jobs approaching their time limit.

- `sview` is a graphical user interface to get and update state information for jobs, partitions, and nodes managed by Slurm.