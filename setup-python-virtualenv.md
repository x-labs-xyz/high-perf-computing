# Setup Python Virtual Environment

The HPC does not provide sudo access, and does not by default allow you to install the version of _python_ you desire through `sudo apt-get install`, or make available _pip_, or _virtualenv_ commands. Therefore, to create an environment where you can flexibly install the python packages you want, do the following:



#### 0. Change to `$SCRATCH` (remember this is where we must work and launch jobs).

```bash
cd $SCRATCH
```



#### 1. Create a _conda_ environment; 
   - Specify the name with `-n` .
   - Specify the python version with `python=`.

```bash
(/scratch/<NetID>)$ conda create -n myenv-python37 python=3.7
```



#### 2. To view where this environment was created, you can run:

```bash
(/scratch/<NetID>)$ conda info --envs

# conda environments:
#
myenv-test               /home/<NetID>/.conda/envs/myenv-python37
base                  *  /share/apps/NYUAD/miniconda/3-4.8.2
R-3.6                    /share/apps/NYUAD/miniconda/3-4.8.2/envs/R-3.6
firefox                  /share/apps/NYUAD/miniconda/3-4.8.2/envs/firefox
gcc-7.3.0                /share/apps/NYUAD/miniconda/3-4.8.2/envs/gcc-7.3.0
pytorch-1.4              /share/apps/NYUAD/miniconda/3-4.8.2/envs/pytorch-1.4
tensorflow-1.15          /share/apps/NYUAD/miniconda/3-4.8.2/envs/tensorflow-1.15
tensorflow-2.0           /share/apps/NYUAD/miniconda/3-4.8.2/envs/tensorflow-2.0
```

You can see that it was automatically installed into `/home/<NetID>/.conda/envs/`.



#### 3. Activate the _conda_ environment you just created:

```bash
(/scratch/<NetID>)$ conda activate myenv-python37
```



#### 4. Check that python was properly installed:
   - Now that you are within the `myenv-python37` conda environment the location of python on the system should be on a conda path within your conda environment you just created. 
   - The python version should be `3.7`.

```bash
# Check python path
(myenv-python37) (/scratch/<NetID>)$ which python
~/.conda/envs/myenv-python37/bin/python

# Check python version
(myenv-python37) (/scratch/<NetID>)$ python --version
Python 3.7.6
```



#### 5. Install _virtualenv_. 
Note that _pip3_ was installed automatically into the `myenv-python37` _conda_ environment.

```bash
(myenv-python37) (/scratch/<NetID>)$ pip3 install virutalenv
```



#### 6. Create a virtual environment for your python project.
   - You are specifying the source python `-p` for this virtual environment (which is the python 3.7 that sits within your conda `myenv-python37` environment)

```
(myenv-python37) (/scratch/<NetID>)$ virtualenv -p python venv-test
```



#### 7. Check that your virtual environment was created.  
You should find `venv-test/` directory listed in your current directory. 
   - Note that you will want to create a virtual environment for each seperate project you are working on, and would create it _within_ the project direcory.

```bash
(myenv-python37) (/scratch/<NetID>)$ ls
venv-test
```



#### 8. Deactive your  ``myenv-python37` _conda_ environment.

```bash
(myenv-python37) (/scratch/<NetID>)$ conda deactivate
```



#### 9. Go ahead and activate your virtual environment.
You can have a functional virtual environment with your desired version of python, and without the need to `sudo apt-get install` packages. 

```bash
(/scratch/<NetID>)$ source venv-test/bin/activate
```



#### 10. If you desire to install _matplotlib_, _pandas_, _numpy_, _scikit-image_ you can do the following. 

```bash
(venv-test) (/scratch/<NetID>)$ pip3 install matplotlib
(venv-test) (/scratch/<NetID>)$ pip3 install panads
(venv-test) (/scratch/<NetID>)$ pip3 install numpy
(venv-test) (/scratch/<NetID>)$ pip3 install scikit-image
```

Then you can view installed libraries and versions with `pip freeze`.
