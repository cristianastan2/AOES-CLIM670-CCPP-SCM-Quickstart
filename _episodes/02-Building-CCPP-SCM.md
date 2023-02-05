---
title: "Building CCPP-SCM"
teaching: 0
exercises: 0 
questions:
- "How do I setup the SCM?"
objectives:
keypoints:
---
You have now downloaded all the model components. Let's take a look:
From your `/home/username/classes/clim670/ccpp-scm-6.0` directory, see what is there.
~~~
$ ls
~~~
{: .language-bash}

#### Organization of the CCPP-SCM Directory

* scm - Top level SCM directory: contains the dynamical core
* ccpp - Top level CCPP directory: contains physics packages
* CMakeModules - Contains modules required to run the code
* contrib - Contains scripts that download data files required to run the model
* docker - Top directory for building CCPP-SCM container (not used in this class)
* test - Top directory of a test case
* tutorial_files - Examples used by developers in tutorials 

### Using existing libraries
The Python environment must provide the f90nml module for the SCM scripts to function. Users can test if f90nml is installed using this command in the shell:
~~~
$ python -c "import f90nml"
~~~
{: .language-bash}

If f90nml is installed, this command will succeed silently, otherwise an ImportError: No module named f90nml will be printed to screen. To install the f90nml (v0.19) Python module, use:

~~~
$ module load anaconda3
$ pip install --user f90nml ==0.19
~~~
{: .language-bash}

Platform-specific scripts are provided to load modules and set the user environment for preconfigured platforms. These scripts load compiler modules (Fortran 2008-compliant), the NetCDF module, Python environment, etc. and set compiler and environment variables. 
~~~
$ cd /home/username/classes/clim670/ccpp-scm-6.0/scm/etc/
$ ls 
~~~
{: .language-bash}

Hopper is not one of the preconfigured platforms. To get the corresponding file for Hopper: 
~~~
$ cp /home/cstan/classes/clim670/ccpp-scm/scm/etc/Hopper_setup* . 
~~~
{: .language-bash}

Let's take a look:
From your `/home/username/classes/clim670/ccpp-scm-6.0/scm/etc/` directory, see what is there.
~~~
$ ls
~~~
{: .language-bash}

Now you have two new files that will configure the building environmeent for Hopper. One can be used for the t/csh shell and the other for the bash shell. From the top-level code directory (ccpp-scm-6.0), source the bash script for Hopper:

~~~
$ cd ../../
$ source scm/etc/Hopper_setup_gnu.sh
~~~
{: .language-bash}

Ignore the warnings. 

The first step in compiling the CCPP and SCM is to properly setup your user environment as described in sections above. The second step is to download the lookup tables and other large datasets (large binaries, <1 GB) needed by the physics schemes and place them in the correct directory. I have downloaded these files and you will create symbolic links:

~~~
$ cd /home/username/classes/clim670/ccpp-scm-6.0/scm/data/
$ ln -fs /home/cstan/classes/clim670/ccpp-scm/scm/data/comparison_data/
$ ln -fs /home/cstan/classes/clim670/ccpp-scm/scm/data/physics_input_data/
$ ln -fs /home/cstan/classes/clim670/ccpp-scm/scm/data/processed_case_input/
$ ln -fs /home/cstan/classes/clim670/ccpp-scm/scm/data/raw_case_input/
~~~
{: .language-bash}

Above were the one-time setup setps, now we move on to the steps you will do everytime to setup a new model experiment and run it.

1. From the top level code-directory (ccpp-scm-6.0), change directory to the top-level SCM directory: 

~~~
$ cd scm
~~~
{: .language-bash}

2. Make a build directory and change into it

~~~
$ mkdir bin
$ cd bin
~~~
{: .language-bash}

3. Invoke `cmake` on the source code

~~~
$ cmake ../src 
~~~
{: .language-bash}

This will take a while.

### What happened when we ran `cmake`?

* Files necssary for building the executable have been written to bin/
* The CCPP physics and framwork prebuild scripts are ran to match required physics variables with those available from the ddynaamical core (SCM)and to generate physcis caps and makefile segments
* Software caps are generated for each physics group defined in the supplied Suitee definitiona Files (SDFs) and generate a static library that becomes part of the SCM executable. 
* ccpp_prebuild.err contains a list of all files that have been used in this step

We are now ready to compile. This step will create the executable.

~~~
$ make 
~~~
{: .language-bash}

### What happened when we ran `make`?

* The executable `scm` is created and written in bin/ directory.
* The run script `run-scm.py` is being copied to bin/ directory.

### Now what?

If your compilation has completed, then you are ready to run the case. 


