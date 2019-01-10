# Questions

## HPC good citizenship

1. On the UCI cluster, the resource request "-pe openmp 64" refers to the number of processors requested.  Does that
   request mean that your commands will use multiple processors?

No, not necessarily. The software you are running needs to be written in such a way that it will take advantage of multiple processors when they are available. In addition, if a multi-threaded program is written to use 8 CPUs for a task, for example, then giving it more than that will not make it run faster.

2. In general, how do you know how many processors, how much RAM, how many files would be required/needed/written by the
   jobs you are running on the cluster?

Before you run a job, you might be able to find out how many processors you should use by consulting the documentation for the programs you are running (there may be sample commands). Depending on the level of detail, the documentation may also describe the output files generated. In other cases though, it may be difficult to know ahead of time how RAM-intensive your job will be, how many processors are needed, or how many fiels will be written. It is a good idea to run a small test first and find out what resources it used, rather than flooding the cluster with jobs. You might choose to compare runtimes for the job with different numbers of processors in order to decide how many are actually necessary.

3. In order to be a "good citizen", you need to have some idea of much RAM your job requires.  In particular, you need
   to know the "peak" (i.e., maximum) RAM required at any point during execution.  Show an example of the shell command
   that you would use on a Linux machine to measure run time and peak ram usage of an arbitrary command, where the time/peak RAM values are written to a file.

Given the job ID, you can find out what the runtime and peak RAM usage were for a particular job using the following command:
```
qacct -j job_id > job_usage.txt
```
For example, in the case of a Minimap2 run I performed to map PacBio transcriptome reads to the human reference genome, the output looks like this. Maximum resident size set (ru_maxrss) tells you what the maximum RAM used was, and ru_utime tells you the runtime.
```
qname        sam128
hostname     compute-4-47.local
group        samlab
owner        dwyman
project      NONE
department   defaultdepartment
jobname      Minimap2_PB81_D01
jobnumber    5632701
taskid       undefined
account      sge
priority     0
qsub_time    Sat Dec 29 15:58:19 2018
start_time   Sat Dec 29 15:58:33 2018
end_time     Sat Dec 29 16:01:49 2018
granted_pe   one-node-mpi
slots        16
failed       0
exit_status  0
ru_wallclock 196s
ru_utime     1912.159s
ru_stime     30.081s
ru_maxrss    20.392MB
ru_ixrss     0.000B
ru_ismrss    0.000B
ru_idrss     0.000B
ru_isrss     0.000B
ru_minflt    8288917
ru_majflt    0
ru_nswap     0
ru_inblock   6141036
ru_oublock   595408
ru_msgsnd    0
ru_msgrcv    0
ru_nsignals  0
ru_nvcsw     542142
ru_nivcsw    12677
cpu          1942.240s
mem          33.852TBs
io           3.476GB
iow          0.000s
maxvmem      20.381GB
arid         undefined
ar_sub_time  undefined
category     -U class-ee282,samlab,bio -u dwyman -q sam128 -pe one-node-mpi 16
```

4. What are the units of your answer for number 3?

The time that the job took to run is displayed in seconds, and the peak RAM is expressed in megabytes.

5. What are the bash commands for the following operations:

    * Checking that a file exists
    * Checking that a file exists and is not empty

Checking that a file exists:
```
[ -f my_file.txt ] && echo "File exists"
```
Checking that a file exists and is not empty:
```
[ -f my_file.txt ] && [ -s my_file.txt ] && echo "File exists and is not empty"
```

6. How would you use the commands from your answer to 5 to write a work flow for HPC that only runs a job if the
   expected output file is **not** present.
```
outfile="myfile.txt"
if [ -f "$outfile" ] && [ -s "$outfile" ] then
    echo "Running job..."
    qsub ./run_my_job.sh
else
    echo "Not running job: outfile $outfile already exists!"
fi
```

## Trickier questions

7. Would your answer to number 3 work on Apple's OS X operating system?  If no, do you have any idea why not?

The command would not work on the OS X operating system, because it is Linux operating system command that is designed to be used with the Sun Grid Engine job scheduler.  

8. Most of the HPC nodes have 512Gb (gigabytes) of RAM. Let's say you have a job that will require **no more** than 24Gb
   of RAM.  How would you request resources so that you can run more than one job on a node **and** not cause nodes to
   crash?  Show an example of a skeleton HPC script as part of your answer.  **Knowing how to do this is super important
   and will save you loads of frustration and prevent you from taking out your colleagues' jobs on the cluster,
   preventing you from getting nasty emails from Harry!!!!!!!!!!!**
This script will allow you to run a parallelized job on a single node:
```
#!/bin/bash
#$ -N TEST
#$ -q pub64,free64
#$ -pe one-node-mpi 8
#$ -R y
#$ -m beas

```

