---
title: "Running CCPP-SCM"
teaching: 0
exercises: 0 
questions:
- "How do I setup a case and run the SCM?"
objectives:
keypoints:
---
You have now build the model. There are several test cases provided with this version of the SCM. For all cases, the SCM will go through the time steps, applying forcing and calling the physics defined in the chosen suite definition file using physics configuration options from an associated namelist. The model is executed through a Python run script that is pre-staged into the bin directory: `run_scm.py`. It can be used to run one integration or several integrations serially, depending on the command line arguments supplied.

Running a case requires four pieces of information: 
1. The case to run (consisting of initial conditions, geolocation, forcing data, etc.), 
2. The physics suite to use (through a CCPP suite definition file), 
3. A physics namelist (that specifies configurable physics options to use), and 
4. A tracer configuration file. 

Cases are set up via their own namelists in ../etc/case_config. A default physics suite is provided as a user-editable variable in the script and default namelists and tracer configurations are associated with each physics suite (through ../src/suite_info.py), so, technically, one must only specify a case to run with the SCM when running just one integration. For running multiple integrations at once, one need only specify one argument (-m) which runs through all permutations of supported suites from ../src/suite_info.py and cases from ../src/supported_cases.py. 

The run script’s options are described below where option abbreviations are included in brackets.

--case [-c]
>
>This or the `--multirun` option are the minimum required arguments. The case should correspond to the name of a case in ../etc/case_config (without the >.nml extension).
>
--suite [-s]
>
>The suite should correspond to the name of a suite in ../ccpp/suites (without the .xml) extension that was supplied in the cmake or ccpp_prebuild step. 
>

--namelist [-n]
>
>The namelist should correspond to the name of a file in ../ccpp/physics_namelists (WITH the .nml extension). If this argument is omitted, the default >namelist for the given suite in ../src/suite_info.py will be used.
>

--tracers [-t]
>
>The tracers file should correspond to the name of a file in ../etc/tracer_config (WITH the .txt extension). If this argu- ment is omitted, the default >tracer configuration for the given suite in ../src/suite_info.py will be used.
>

--multirun [-m]
>
>This or the --case option are the minimum required arguments. When used alone, this option runs through all permutations of supported suites from >../src/suite_info.py and cases from ../src/supported_cases.py. When used in conjunction with the -- file option, only the runs configured in the file > >will be run.

--file [-f]
>This option may be used in conjunction with the --multirun argument. It specifies a path and filename to a python file where multiple runs are >configured. 
>

--gdb [-g]
>Use this to run the executable through the gdb debugger (if it is installed on the system).
>

--docker [-d]
>Use this argument when running in a docker container in order to successfully mount a volume between the host machine and the Docker container instance >and to share the output and plots with the host machine. 
>

--runtime
>Use this to override the runtime provided in the case configuration namelist. 
>

--runtime_mult
>
>Use this to override the runtime provided in the case configuration namelist by multiplying the runtime by the given value. This is used, for example, in regression testing to reduce total runtimes.
>

--levels [-l]
>
>Use this to change the number of vertical levels.
>

--npz_type
>Use this to change the type of FV3 vertical grid to produce (see src/scm_vgrid.F90 for valid values).
> 

--vert_coord_file
>Use this to specify the path/filename of a file containing the a_k and b_k coefficients for the vertical grid generation code to use.
>
--bin_dir
>
>Use this to specify the path to the build directory.
>

--run_dir
>
>Use this to specify the path to the run directory.
>

--case_data_dir
>
>Use this to specify the path to the directory containing the case data file (useful for using the DEPHY case repository). 
>

--n_itt_out
>
>Use this to specify the period of writing instantaneous output in timesteps (if different than the default specified in the script).
>

--n_itt_diagt
>Use this to specify the period of writing instantaneous and time-averaged diagnostic output in timesteps (if different than the default specified in the >script).
> 

--timestep [-dt]
>
>Use this to specify the timestep to use (if different than the default specified in ../src/suite_info.py).
>

--verbose [-v]
>
>Use this option to see additional debugging output from the run script and screen output from the executable.
>
When invoking the run script, the only required argument is the name of the case to run. The case name used must match one of the case configuration files located in ../etc/case_config (without the .nml extension!). If specifying a suite other than the default, the suite name used must match the value of the suite name in one of the suite definition files located in ../../ccpp/suites (Note: not the filename of the suite definition file). 

As part of the sixth CCPP release, the following suite names are valid:
1. SCM_GFS_v16
2. SCM_GFS_v17p8 
3. SCM_RAP
4. SCM_HRRR
5. SCM_RRFS_v1beta 
6. SCM_WoFS_v0

Let's create a case. From the top level code-directory (ccpp-scm-6.0), change directory to the top-level SCM directory and create a directory were to run all cases and then a directory for each case

~~~
$ mkdir cases
$ cd cases
$ mkdir gfs_v16_bomex
~~~
{: .language-bash}

Create a script (e.g., `create_gfs_v16_bomex_case.sh`) with the following

~~~
#!/bin/bash

export case=bomex
export suite=SCM_GFS_v16
export namelist=input_GFS_v16.nml
export RUN_TIME=86400
export ITT_OUT=1
export BIN_DIR=/home/cstan/scm_sandbox/ccpp-scm-6.0/scm/bin
export RUN_DIR=/scratch/cstan/clim670/ccpp-scm-6.0  # Make sure RUN_DIR exists

python ${BIN_DIR}/run_scm.py -c ${case} -s ${suite} -n ${namelist} --runtime ${RUN_TIME} --n_itt_out ${ITT_OUT} --bin_dir ${BIN_DIR} --run_dir ${RUN_DIR}
~~~
{: .language-bash}

Make sure the script is executable. To check

~~~
$ ls -l create_gfs_v16_bomex_case.sh
~~~
{: .language-bash}

~~~
-rwxr--r-- 1 cstan users 456 Feb  5 17:10 create_gfs_v16_bomex_case.sh
~~~
{: .output}

If the sequence `-rwxr--r--` does not have the execute (x) permission we can add it:

~~~
$ chmod u+x create_gfs_v16_bomex_case.sh 
~~~
{: .language-bash}

> ## Check if your script has the execute permission
>
> Does your file has the execute (x) permsion? 
>
{: .challenge}


Before execturing the script, we need to set the user environment for Hopper.

From the top-level code directory (ccpp-scm-6.0), source the bash script for Hopper:

~~~
$ source scm/etc/Hopper_setup_gnu.sh
~~~
{: .language-bash}

Now we are ready to strat running our gfs_v16_bomex_case. 

~~~
$ cd scm/cases/gfs_v16_bomex/
$ ./create_gfs_v16_bomex_case.sh
~~~
{: .language-bash}

A NetCDF output file is generated in a directory located in the RUN_DIR. 

~~~
$ cd /scratch/cstan/clim670/ccpp-scm-6.0/output_bomex_SCM_GFS_v16
$ ls
~~~
{: .language-bash}

The `output.nc` contains the output written with the frequency set by the `--n_itt` variable.

> ## What is in this file?
>
> We will look at the file using `ncdump -h` to understand what is in the file.
>
> What variables are in the file?
> 
> How many times are in the output file?
>
{: .challenge}

We can read the file using Python `xarray`. This is a small file. 

The directory /scratch/cstan/clim670/ccpp-scm-6.0/output_bomex_SCM_GFS_v16 contains two moree files:
* bomex_SCM_GFS_v16.nml
* logfile

The namlist file contains the configuration namelist that contains parameters for the SCM infrastructure and the physics configuration namelist. 

The case_config namelist expects the following parameters:

* case_name
>
>Identifier for which dataset (initialization and forcing) to load. This string must correspond to a dataset included in the directory ccpp->scm/scm/data/processed_case_input/ (without the file extension). 
>

* runtime
>
>Specify the model runtime in seconds (integer). This should correspond with the forcing dataset used. If a runtime is specified that is longer than the >supplied forcing, the forcing is held constant at the last specified values.
>

* thermo_forcing_typee
>
>An integer representing how forcing for temperature and moisture state vari-
ables is applied (1 = total advective tendencies, 2 = horizontal advective ten- dencies with prescribed vertical motion, 3 = relaxation to observed profiles with vertical motion prescribed)
>

* mom_forcing_type
>
>An integer representing how forcing for horizontal momentum state variables
is applied (1 = total advective tendencies; not implemented yet, 2 = hori- zontal advective tendencies with prescribed vertical motion, 3 = relaxation to observed profiles with vertical motion prescribed)
>
* relax_time
>Afloatingpointnumberrepresentingthetimescaleinsecondsfortherelaxation forcing (only used if thermo_forcing_type = 3 or mom_forcing_type = 3) 
>
* sfc_flux_spec
>
>A boolean set to .true. if surface flux are specified from the forcing data (there is no need to have surface schemes in a suite definition file if so)
>

* sfc_roughness_length_cm
>
>Surface roughness length in cm for calculating surface-related fields from specified surface fluxes (only used if sfc_flux_spec is True). 
>

* sfc_type
>An integer representing the character of the surface (0 = sea surface, 1 = land surface, 2 = sea-ice surface)
>

* reference_profile_choice
>
>An integer representing the choice of reference profile to use above the supplied initialization and forcing data (1 = “McClatchey” profile, 2 = mid-latitude summer standard atmosphere) 
>

* year
>
>An integer representing the year of the initialization time 
>

* month
>
>An integer representing the month of the initialization time 
>

* day
>
>An integer representing the day of the initialization time 
>

* hour
>
>An integer representing the hour of the initialization time 
>

* column_area
>
>A list of floating point values representing the characteristic horizontal domain area of each atmospheric column in square meters (this could be >analogous to a 3D model’s horizontal grid size or the characteristic horizontal scale of an observation array; these values are used in scale-aware >schemes; if using multiple columns, you may specify an equal number of column areas)
>

* model_ics
>
>A boolean set to .true. if UFS atmosphere initial conditions are used rather than field campaign-based initial conditions 
>

* C_RES
>
>An integer representing the grid size of the UFS atmosphere initial conditions; the integer represents the number of grid points in each horizontal >direction of each cube tile
>

* input_type
>
>0 => original DTC format, 1 => DEPHY-SCM format.
>
Optional variables (that may be overridden via run script command line arguments) are:

* vert_coord_file
>
>File containing FV3 vertical grid coefficients.
>

* dt
>
>Time step in seconds (floating point)
>

* n_itt_out
>
>Specify the period of the instantaneous model output in number of timesteps (integer). 
>

* n_itt_diag
>
>Specify the period of the instantaneous and time-averaged diagnostic output in number of timesteps (integer).
>

* n_levels
>
>Specify the integer number of vertical levels.


The physics_config expects the following parameters:

* physics_nml
>
>The name should correspond to the name of a file in ../ccpp/physics_namelists (WITH the .nml extension)
* physics_suite
>
>The suite should correspond to the name of a suite in ../ccpp/suites (without the .xml) 
>
The logfile contains information from the running time. 

