# Slurm

## Queues are pools of compute nodes on Pi

```
cpu: 16 cores 64G Memalloc
fat:          256G Mem
gpu:          2K20m GPUs 64G Mem
k40:          2K40 GPUs 64G Mem
k80: 24 cores 2K80 GPUs 96G Mem
```

* 4 jobs at max for each research group
* 4 nodes at max per jobs
* 24 hours on walltime per job

## Different Commands

* LSF `bjobs` `bsub` `bkill`
* SLURM `sinfo` `squeue`

* sinfo: check cluster status
	* drain(something wrong)
	* alloc(in use)
	* idle
	* down

* squeue: show pending and running jobs
	* R running
	* PD pending
	* Pending reasons: priority, rare; AssocMaxJobsLimit; Resources
* scontrol: display and modify job state

* job partition:
	* cpu
	* fat
	* gpu
	* k40
	* k80
* reasonable walltime

```bash
sbatch JOBSCRIPT.slurm
```

NO redirection!

```
options:
-p partition
-n Total processes
--ntasks-per-node=[count]
```

```
#SBATCH --job-name=JOBNAME
#SBATCH --partition=cpu
#SBATCH -n 64
#SBATCH --ntasks-per-node=16
#SBATCH --mail-type=end
#SBATCH --mail-user=YOU@email.com
#SBATCH --output=%j.out
#SBATCH --error=%j.err
#SBATCH --exclusive
#SBATCH --time=00:00:01 // run for only one second
```

## Modules

```bash
# lies in /lustre/usr/utility/pi-code-sample
module load gcc openmpi fftw3
```

## DOs and DONTs on Pi

### DOs
* Login to compute nodes during job execution.
* Use pi.sjtu.edu.cn/ganglia to monitor job.
* Attach username,jobid,error message support@lists.sjtu.edu.cn.

### DONTs
* NO du!!!
* NO parallel jobs on login nodes.