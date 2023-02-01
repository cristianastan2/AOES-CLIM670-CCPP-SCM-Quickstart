---
title: "Downloading CCPP-SCM"
teaching: 0
exercises: 0 
questions:
- "How do I download CCPP-SCM?"
objectives:
keypoints:
---

We will use GMU Hopper to run this model.
We will now download the model and all the setup files needed for it. These are one-time steps.

Go to your `/home/` directory 
~~~
$ cd /home/cstan/classes/clim670
~~~
{: .language-bash}

Download the CCPP-SCM from Github
~~~
git clone --recursive -b v6.0.0 https://github.com/NCAR/ccpp-scm ccpp-scm-6.0 
~~~
{: .language-bash}

This will take a while ...

Change to the `ccpp-scm-6.0` directory
~~~
$ cd ccpp-scm-6.0
~~~
{: .language-bash}

Let's take a look in that directory. 
The recursive option in the `git clone` command clones the main ccpp-scm repository and all subrepositories (ccpp-physics and ccpp-framework). Using this option, there is no need to execute `git submodule init` and `git submodule update`.
