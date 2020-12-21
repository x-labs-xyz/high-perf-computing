# High Performance Computing
This repo contains a guide to working in the High Performance Computing (HPC) cluster at NYAUD.

- [Main guide for NYUAD HPC.](https://wikis.nyu.edu/display/ADRC/Overview)

## Tools of the trade

Accessing and working with the HPC requires usage and familiarity with the linux command-line interface to: 

- _ssh_ into portal machines.
- Navigate folder directories.
- Bash scripting to launch scripts, 
- Bash scripting to launch parallel CPU computing tasks/jobs.
- Bash scripting to launch neural network GPU training tasks/jobs.

The HPC is not necessarily designed for a _jupyter notebook_ workflow, but rather the creation of __.py__ files (and scripts in other languages) that can be launched from the command line, with results written to file.



## Connecting to HPC

1. Apply for an account [here](https://wikis.nyu.edu/display/ADRC/Accounts).
2. Connect by VPN, and then ssh:

```bash
ssh <your NetID>@dalma.abudhabi.nyu.edu
```



## System Overview

There are 4 directories you have access to: 

1. `$HOME` (/home/<NetID>)
    - Keep small persistent fraction of code here (e.g., source code, executables, Python packages ...).
2.  `$SCRATCH` (/scratch/<NetID>)
    - Put all data here.
    - Run jobs from here.
3.  `$WORK` (/work/<NetID>)
    - Mountable on your local workstation. 
    - Useful for quick post-processing, analysis and visualization, without moving data.
4. `$ARCHIVE` (/archive/<NetID>)
    - For long-term storage, archive work here.

|                       | $HOME                                             | $SCRATCH        | $WORK           | $ARCHIVE        |
| --------------------- | ------------------------------------------------- | --------------- | --------------- | --------------- |
| Use for storing       | source code / executable / perl-python-R packages | data            | anything        | anything        |
| Accessible From       | login / compute                                   | login / compute | login           | login           |
| Use to Run Jobs       | No                                                | Yes             | No              | No              |
| Retention Time (Days) | No Limit                                          | 90              | 120             | No Limit        |
| Mountable             | No                                                | No              | Yes             | No              |
| Default Quota         | 5GB, 100K Files                                   | 5TB, 500K Files | 5TB, 500K Files | 5TB, 125K Files |

> Note: Backing up work is your responsibility. It is suggested that you always push your work onto a github repo. For larger data, we can setup a database with automatic archiving.

