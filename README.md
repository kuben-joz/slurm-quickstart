# Slurm Quickstart

A short guide to running your CUDA programs on the cluster for the CUDA programming course.

> **Note:** This guide is a work in progress and will be expanded later.

## Overview

**Slurm** is the job scheduler that gives you access to the cluster. You log in to a server called the **access node**, which is your control panel for running programs on the **compute nodes**.

## Logging in

You should have received a Teams message with your username and a one-time password link.

Log in to the access node over SSH:

```bash
ssh ${username}@eden.mini.pw.edu.pl
```

## Running programs

There are two ways to run programs through Slurm: `srun` and `sbatch`. In both cases, if the resources you **allocate** (CPU, GPU, RAM) are not available, your job is **queued** and starts once they free up.

### `srun`

`srun` runs a single "step" of computation, much like `torchrun` or `mpirun`. It can run a command directly or give you an interactive shell on a compute node. Run `srun --help` for the full list of options.

#### Common options used below

| Option | Meaning |
| --- | --- |
| `--account` | Account the job is billed to (for this course: `10-stud-2627-z`) |
| `-p` | Partition to run in |
| `-N` | Number of nodes |
| `-w` | Restrict the job to specific nodes (e.g. `stud-[1-3]`) |
| `--time` | Time limit, `HH:MM:SS` |
| `--mincpus` | Minimum number of CPU cores |
| `--mem` | Amount of RAM |
| `-G` | Number of GPUs |
| `--pty` | Run the command in a pseudo-terminal (needed for interactive sessions) |
| `--container-image` | Run inside a container image |

#### Examples

Simple test: print the OS name of a compute node (1 minute limit).

```bash
srun --account=10-stud-2627-z -p student -N 1 \
  -w stud-[1-3] --time=00:01:00 --pty grep PRETTY /etc/os-release
```

Interactive session for 1 hour 15 minutes with 8 CPU cores, 16 GB of RAM and one GPU.

```bash
srun --account=10-stud-2627-z -p student -N 1 \
  -w stud-[2-3] --time=01:15:00 --mincpus 8 --mem 16GB -G 1 \
  --pty /bin/bash
```

The same session, but inside the Docker image `nvcr.io/nvidia/cuda:13.4.2-cudnn-devel-ubuntu26.04`.

```bash
srun --account=10-stud-2627-z -p student -N 1 \
  --container-image=nvcr.io#nvidia/cuda:13.4.2-cudnn-devel-ubuntu26.04 \
  -w stud-[2-3] --time=01:15:00 --mincpus 8 --mem 16GB -G 1 \
  --pty /bin/bash
```

### `sbatch`

`sbatch` is the batch-script version of the above: you describe the resources and commands in a script, submit it, and Slurm runs it when resources are available, without you staying connected.

> This section will be expanded later. Below is a minimal example.

```bash
#!/bin/bash
#SBATCH --account=10-stud-2627-z
#SBATCH -p student
#SBATCH -N 1
#SBATCH --time=00:10:00
#SBATCH --mincpus 8
#SBATCH --mem 16GB
#SBATCH -G 1

nvidia-smi
```

Submit it with:

```bash
sbatch job.sh
```
