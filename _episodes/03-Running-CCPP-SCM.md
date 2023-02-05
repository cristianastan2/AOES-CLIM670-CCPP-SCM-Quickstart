---
title: "CCPP-SCM running"
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
• --case [-c]
– This or the --multirun option are the minimum required arguments.
The case should correspond to the name of a case in ../etc/case_config (without the .nml extension).

• --suite [-s]
– The suite should correspond to the name of a suite in ../ccpp/suites (without
the .xml) extension that was supplied in the cmake or ccpp_prebuild step. • --namelist [-n]
–The namelist should correspond to the name of a file in ../ccpp/physics_namelists (WITH the .nml extension). If this argument is omitted, the default namelist for the given suite in ../src/suite_info.py will be used.

• --tracers [-t]
– The tracers file should correspond to the name of a file in
../etc/tracer_config (WITH the .txt extension). If this argu- ment is omitted, the default tracer configuration for the given suite in ../src/suite_info.py will be used.

• --multirun [-m]
– This or the --case option are the minimum required arguments.
When used alone, this option runs through all permutations of supported suites from ../src/suite_info.py and cases from ../src/supported_cases.py. When used in conjunction with the -- file option, only the runs configured in the file will be run.

• --file [-f]
– This option may be used in conjunction with the --multirun argument. It
specifies a path and filename to a python file where multiple runs are configured. 

• --gdb [-g]
– Use this to run the executable through the gdb debugger (if it is installed on the system).

• --docker [-d]
– Use this argument when running in a docker container in order to successfully
mount a volume between the host machine and the Docker container instance
and to share the output and plots with the host machine. 

• --runtime
– Use this to override the runtime provided in the case configuration namelist. 

• --runtime_mult
– Use this to override the runtime provided in the case configuration namelist by multiplying the runtime by the given value. This is used, for example, in regression testing to reduce total runtimes.

• --levels [-l]
– Use this to change the number of vertical levels.

• --npz_type
– Use this to change the type of FV3 vertical grid to produce (see src/scm_vgrid.F90 for valid values). 

• --vert_coord_file
– Use this to specify the path/filename of a file containing the a_k and b_k coefficients for the vertical grid generation code to use.

• --bin_dir
– Use this to specify the path to the build directory.

• --run_dir
– Use this to specify the path to the run directory.

• --case_data_dir
– Use this to specify the path to the directory containing the case data file (useful
for using the DEPHY case repository). 

• --n_itt_out
– Use this to specify the period of writing instantaneous output in timesteps (if different than the default specified in the script).

• --n_itt_diagt
– Use this to specify the period of writing instantaneous and time-averaged diag-
nostic output in timesteps (if different than the default specified in the script). 

• --timestep [-dt]
– Use this to specify the timestep to use (if different than the default specified in ../src/suite_info.py).

• --verbose [-v]
– Use this option to see additional debugging output from the run script and screen output from the executable.

When invoking the run script, the only required argument is the name of the case to run. The case name used must match one of the case configuration files located in ../etc/case_config (without the .nml extension!). If specifying a suite other than the default, the suite name used must match the value of the suite name in one of the suite definition files located in ../../ccpp/suites (Note: not the filename of the suite definition file). 

As part of the sixth CCPP release, the following suite names are valid:
1. SCM_GFS_v16
2. SCM_GFS_v17p8 
3. SCM_RAP
4. SCM_HRRR
5. SCM_RRFS_v1beta 
6. SCM_WoFS_v0




~~~
$ cmake ../src 
~~~
{: .language-bash}

This will take awhile.

