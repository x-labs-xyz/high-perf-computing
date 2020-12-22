# Working with _slurm_

_slurm_ is a command line tool that manages the deployment of jobs (i.e. scripts) across a set of computers. You can view a basic guide on _slurm_ [here](https://researchit.las.iastate.edu/slurm-basics). 

These are the main _slurm_ commands:

- **sbatch** - submit a job script.
- **srun** - run a command on allocated compute node(s).
- **scancel** - cancel a job.
- **squeue** - show state of jobs.
- **sinfo** - show state of nodes and partitions (queues).
- **smap** - show jobs, partitions and nodes in a graphical network topology.
- **scontrol** - modify jobs or show information about various aspects of the cluster.



## A. Viewing machines in a network

To view a list of machines in the HPC:

```bash
$ sinfo
PARTITION     AVAIL  TIMELIMIT  NODES  STATE NODELIST
all            down   infinite      1 drain* compute-1-8
...
```



## B. Running a job as an interactive session

If you want to run a simple script, do NOT run it on the portal machine, rather allocate a machine from the pool of available machines in the cluster.

#### 1. Change to `$SCRATCH`

Remember, we always work from `$SCRATCH`:

```bash
cd $SCRATCH
```

#### 2. Enable an interactive session

```bash
srun --pty -n 1 /bin/bash
```

- `--pty` creates a pseudo terminal (effectively an ssh into a new machine)
- `-n 1` asks for a single machine
-  `/bin/bash` defines the interactive session to be a bash session

```bash
# Creating an interactive session
<NetID>@login-0-2:/scratch/<NetID>$ srun --pty -n 1 /bin/bash
<NetID>@compute-5-18:/scratch/<NetID>$ 
```

Notice how the portal machine was `login-0-2` and the command created a new bash session in a machine in the cluster `compute-5-18`.

#### 3. View your job in the queue

_srun_ created a job which you can view in the queue:

```bash
# Checking that job is running
<NetID>@compute-5-18:/scratch/<NetID>$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           2295485   ser_ext     bash  <NetID>  R       3:22      1 compute-5-18
```

Note that the result may be very long, so to filter for your job run: 

````bash
squeue | grep <NetID>
````

#### 4. Run your script

Now you can run your code as you normally would. Because this is an interactive session, if you exit or close the window, then the session (and hence the job) will end.

#### 5. Exit the interactive session

```bash
exit
```



> 
>
> Note: It is best to deploy jobs as described in the next sections.
>
> 

## C. Dispatching tasks in parallel

This is an example __test.sh__ sbatch file

```bash
#!/bin/bash

#SBATCH --output=output/slurm-%j-%J-%A-%a.out
#SBATCH --error=output/error-%j-%J-%A-%a.err
#SBATCH --array=0-4
#SBATCH --time=10:00
    
echo "SLURM_JOB_ID:        $SLURM_JOB_ID"
echo "SLRUM_ARRAY_JOB_ID:  $SLURM_ARRAY_JOB_ID"
echo "SLURM_ARRAY_TASK_ID: $SLURM_ARRAY_TASK_ID"
echo "SLURM_ARRAY_TASK_ID: $SLURM_ARRAY_TASK_COUNT"
echo ""
echo "Hostname : $(hostname)"
echo " ... sleep 30"
sleep 30
echo "Finished sleeping"
```

- `--array=0-4` This runs 1 job with 5 arrays (i.e. sub-tasks / processes) in parallel. 

- `--output` Results are printed out to log files __output/slurm*.out__ 
  - `%j` is the job id
  - `%J` is the job id for the specific array
  - `%A` is the job id for the specific array
  - `%a` is the task id (i.e. job id array index)
- `--error`  Errors are printed to __output/error-*.err__.
- `--time==10:00` The maximum time for completion; the job is canceled after 10 minutes. 



#### 1. Change to `$SCRATCH`

Remember, we always work from `$SCRATCH`:

```bash
cd $SCRATCH
```

#### 2. Create _output/_ directory

Make sure the directory _output/_ is created before running the script, otherwise you won't see any files generated, and no error to tell you the reason.

```
<NetID>@login-0-2:/scratch/<NetID>$ mkdir output/
```

#### 3. Deploy a job

To run the script and deploy jobs perform the following:

```bash
 <NetID>@login-0-2:/scratch/<NetID>$sbatch test.sh
```

Running the script in this way tells slurm that it should run 5 times in parallel with each array allocated a different CPU. You can run tasks in array form if you want to run the same script _N_ times but passing different arguments depending on the iteration. Otherwise remove the `--array` argument and run it is a single job (which is equivalent to `--array=0`).

When you view the job with _squeue_ you should find something like this:

```bash
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON) 
         2296341_0   ser_ext  test.sh  <NetID>  R       0:07      1 compute-6-18
         2296341_1   ser_ext  test.sh  <NetID>  R       0:07      1 compute-6-18
         2296341_2   ser_ext  test.sh  <NetID>  R       0:07      1 compute-6-18
         2296341_3   ser_ext  test.sh  <NetID>  R       0:07      1 compute-6-18
         2296341_4   ser_ext  test.sh  <NetID>  R       0:07      1 compute-6-18
```

#### 4. Check the output files have been created

```bash
<NetID>@login-0-2:/scratch/<NetID>$ ls -lht output/
total 20K
-rw-r--r-- 1 <NetID> users 183 Dec 22 05:40 slurm-2296341-2296341-2296341-4.out
-rw-r--r-- 1 <NetID> users 183 Dec 22 05:40 slurm-2296345-2296345-2296341-3.out
-rw-r--r-- 1 <NetID> users 183 Dec 22 05:40 slurm-2296342-2296342-2296341-0.out
-rw-r--r-- 1 <NetID> users 183 Dec 22 05:40 slurm-2296343-2296343-2296341-1.out
-rw-r--r-- 1 <NetID> users 183 Dec 22 05:40 slurm-2296344-2296344-2296341-2.out
-rw-r--r-- 1 <NetID> users   0 Dec 22 05:40 error-2296341-2296341-2296341-4.err
-rw-r--r-- 1 <NetID> users   0 Dec 22 05:40 error-2296342-2296342-2296341-0.err
-rw-r--r-- 1 <NetID> users   0 Dec 22 05:40 error-2296343-2296343-2296341-1.err
-rw-r--r-- 1 <NetID> users   0 Dec 22 05:40 error-2296344-2296344-2296341-2.err
-rw-r--r-- 1 <NetID> users   0 Dec 22 05:40 error-2296345-2296345-2296341-3.err
```

#### 5. Check the content of one of the output files

```bash
<NetID>@login-0-2:/scratch/<NetID>$ cat output/slurm-2296341-2296341-2296341-4.out
SLURM_JOB_ID:        2296341
SLRUM_ARRAY_JOB_ID:  2296341
SLURM_ARRAY_TASK_ID: 4
SLURM_ARRAY_TASK_ID: 

Hostname : compute-6-18.local
 ... sleep 30
Finished sleeping
```



## D. Launching Parallel Processes, an example with Python

This is an example sbatch script __sbatch-with-python.sh__. This will activate your virtual environment (assuming you created one following the instructions [here](setup-python-virtualenv.md)), and then run the python script by passing the array task id as an argument to the python script.

```bash
#!/bin/bash

#SBATCH --output=output/slurm-%j-%J-%A-%a.out
#SBATCH --error=output/error-%j-%J-%A-%a.err
#SBATCH --array=0-4
#SBATCH --time=10:00
    
echo "SLURM_JOB_ID:        $SLURM_JOB_ID"
echo "SLURM_ARRAY_TASK_ID: $SLURM_ARRAY_TASK_ID"

# Activating virtual environment
source venv-test/bin/activate
which python

# Running python script
srun python python-script.py $SLURM_ARRAY_TASK_ID
echo "Finished processing"
```



This is an example __python-script.py__ that reads and prints the content of files. If you want to apply the same transform to many files in parallel, you can follow this structure of naming and loading files according to an index that can be iterated over with an sbatch array.

```python
import sys

# Read SLURM_ARRAY_TASK_ID
iter = sys.argv[1]

# Print file contents
filename = "data/file-{iter}.txt".format(iter=iter)
f        = open(filename, "r")
print(f.read())

print("Completed processing", filename)
```



#### 1. Change to `$SCRATCH`

Remember, we always work from `$SCRATCH`:

```bash
cd $SCRATCH
```

#### 2. Create _output/_ directory

Make sure the directory _output/_ is created before running the script, otherwise you won't see any files generated, and no error to tell you the reason.

```
<NetID>@login-0-2:/scratch/<NetID>$ mkdir output/
```

#### 3. Deploy a job

To run the script and deploy jobs perform the following:

```bash
 <NetID>@login-0-2:/scratch/<NetID>$sbatch sbatch-with-python.sh
```



## E. Launching GPU training job

This is similar to the previous examples, with an added parameter to allocate a gpu.

```bash
#!/bin/bash

#SBATCH --output=output/slurm-%j-%J-%A-%a.out
#SBATCH --error=output/error-%j-%J-%A-%a.err
#SBATCH --array=0-4
#SBATCH --time=10:00
#SBATCH --gres=gpu:1  # Number of GPUs (per array task)
    
echo "SLURM_JOB_ID:        $SLURM_JOB_ID"
echo "SLURM_ARRAY_TASK_ID: $SLURM_ARRAY_TASK_ID"

# Activating virtual environment
source venv-test/bin/activate
which python

# Running python script
srun python python-script.py $SLURM_ARRAY_TASK_ID
echo "Finished processing"
```



## F. Additional Notes

- There are several additional arguments that can be passed to sbatch scripts, such as RAM memory, number of cpus per task, etc. You should be mindful of those.
- There may be additional limits to how many jobs may be deployed (e.g. 200?), and for how long (e.g. 48 hours?), so you should check with the NYUAD HPC guide what the limits are.
- To install certain python libraries that use gpus (e.g. pytorch, tensorflow) you may have to create an interactive shell into a gpu machine (`srun --pty -n 1 --gres=gpu:1 /bin/bash`), activate your virtual environment, and then install the library.
- If you find some machines are problematic, you can add them to an exclusion list within sbatch, or include specific machines in an inclusion list in sbatch.
- If a certain array task id fails and you want to rerun specific array task ids you can do so: `--array=3,5,7` will run the 3rd, 5th, and 7th index. 
