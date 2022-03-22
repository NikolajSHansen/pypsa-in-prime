# Running PyPSA-Eur-Sec in PRIME


This repository includes instructions and tricks to run the [PyPSA-Eur-Sec](https://pypsa-eur-sec.readthedocs.io/en/latest/) model on the cluster computer [PRIME](https://mpe.au.dk/en/research/facilities/prime/).

Its main purpose is to help master and PhD students install the packages and run simulations with [PyPSA-Eur-Sec](https://pypsa-eur-sec.readthedocs.io/en/latest/). 

If you encounter a problem (and hopefully also a solution). Please, edit this README file with the solution so that other students can also benefit.

The content of this document is structured as follows:  
1 [General information about PyPSA-Eur-Sec](#general-information-about-pypsa-eur-sec)  
2 [Getting on to the cluster](#getting-on-to-the-cluster)  
3 [Setting up the cluster](#setting-up-the-cluster)  
4 [Running simulations]()
5 [Typpical errors]()
6 [Extra stuff that will make much your life easier](#extra-stuff-that-will-make-your-life-easier)  

## General information about *PyPSA-Eur-Sec* 

[PyPSA-Eur-Sec](https://pypsa-eur-sec.readthedocs.io/en/latest/) is model of the European energy sector including sector coupling. The model is build with the open source python module [PyPSA](https://pypsa.readthedocs.io/en/latest/). The [PyPSA-Eur-Sec](https://pypsa-eur-sec.readthedocs.io/en/latest/) model builds on the older model [PyPSA-Eur](https://pypsa-eur.readthedocs.io/en/latest/) of the European electricity network (without sector coupling). PyPSA-Eur is therefore included as part of PyPSA-Eur-Sec. [PyPSA-Eur-Sec](https://pypsa-eur-sec.readthedocs.io/en/latest/) also use the module [Technology-data](https://github.com/PyPSA/technology-data) to get data on the energy system. Technology-Data is a repository including costs, efficiencies, lifetimes, etc for different technologies.

The [PyPSA-Eur-Sec](https://pypsa-eur-sec.readthedocs.io/en/latest/) use the [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow management system to run simulations. By using Snakemake simulations can be run without opening python files, but simply by running a Snakemake command. Snakemake automatically runs all the needed python scripts for a given simulation. The simulations are configured in the `config.yaml` file. The Snakemake workflow is structured in the `SNAKEFILE`. [Step 10](#10-configure-snakemake) shows how to run the simmulation with Snakemake. 

There is a [distribution list](https://groups.google.com/g/pypsa) where PyPSA-related problems (and solutions) are discussed. You can ask questions here if you have troubles with the model.

There is also documentation for [PyPSA](https://pypsa.readthedocs.io/en/latest/), [PyPSA-Eur](https://pypsa-eur.readthedocs.io/en/latest/) and [PyPSA-Eur-Sec](https://pypsa-eur-sec.readthedocs.io/en/latest/). This repository does not substitute any of the previous information and it only focuses on issues related to running PyPSA-Eur-Sec in PRIME.

NOTE: In order to set up anaconda, python, and pypsa, [these instructions](https://github.com/martavp/RES_project/blob/master/Instructions_RES_project.pdf) and the [tutorial for the course project in RES](https://github.com/martavp/RES_project/blob/master/RES_project.ipynb) could be useful. 

This [video](https://www.youtube.com/watch?v=ty47YU1_eeQ) provides a nice introduction to PyPSA-Eur. 


## Getting on to the cluster

#### 1. Get acces to Prime
To use the [PRIME cluster](https://mpe.au.dk/en/research/facilities/prime/), first you need to get a user. Write an email to Søren Madsen with sma@mpe.au.dk

#### 2. Connect with ssh
 You can connect to the cluster through the terminal, e.g.
> ssh marta@prime.eng.au.dk

The main way of interacting with the cluster will be through a terminal where you have run the ssh command to connect to prime. A more modern way of interacting with the cluster is by using the program VSCode as shown in [step 22](#22-vs-code)

#### 3. Useful commands
Some useful commands to use in the cluster are described in the [labbook](https://labbook.au.dk/display/COM/3.+Convenient+commands).

#### 4. Moving files to the cluster
If you are using Windows, [WinSCP](https://winscp.net/eng/download.php) can be useful to copy folders to/from the cluster. Alternatively, use FileZilla on Windows, OSX or Linux. This makes moving files on the cluster much easier as you would otherwise have to use commands in the terminal to move files. 

#### 5. VPN
To connect to the cluster you need to be connected to the university network, so if you are at home you need to use the VPN (The VPN only works for employee's and PhD students. Master students need to be on university network to connect to the cluster)


## Setting up the cluster
**The following commands must be run on the cluster. Log in to the cluster as shown in [step 2](#2-connect-with-ssh)**

#### 1. Installing anaconda
You will need to have installed [anaconda/miniconda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html) in your home directory at the cluster. Follow the guide at [anaconda/miniconda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html).

#### 2 Installing PyPSA-Eur-Sec
You need to install PyPSA-Eur-Sec in the cluster. There are two approaches for that:

##### 2.a Installing PyPSA-Eur-Sec from 
You can install PyPSA-Eur-Sec following the [instructions](https://pypsa-eur-sec.readthedocs.io/en/latest/installation.html). Installation may take a while. 

##### 2.b Install by forking (A bit more advanced)
You can also [fork](https://docs.github.com/en/get-started/quickstart/fork-a-repo) the repositories *pypsa-eur*, *technology_data*, and *pypsa-eur-sec* on your Github, and clone them to your repository on Prime. This allows you to apply source control with Git (This is easily done in VSCode. See [step 22](#22-vs-code)).

##### 2.c Get databundle from zenodo if wget does not work

Install zenodo-get with the command:
> pip install zenodo-get

Then retrieve the repository with:
> zenodo_get 10.5281/zenodo.5824485

#### 3. Installing the anaconda environment
You will need to have an environment with all the necessary packages.
The invoronment includes [snakemake](https://snakemake.readthedocs.io/en/stable/)
which is a very useful way of dealing with parallelized jobs in the cluster. 
To install all the packages that you need create
the environment using the 'environment.yaml' file provided in pypsa-eur. This step may take several minutes. On the cluster move to the pypsa-eur folder in a terminal and type the following commands:

> .../pypsa-eur % conda env create -f envs/environment.yaml

Activate the environment by typing

> .../pypsa-eur % conda activate pypsa-eur

Everytime you log in to the cluster you must activate the envirionment again. The active environment will be shown in parenthesis in your terminal. 

> (pypsa-eur) [marta@fe1 ~]$

#### 4. Install gurobi 
Install the optimization software [Gurobi](https://www.gurobi.com) in the environment by running the command
> conda install -c gurobi gurobi

#### 5. Configure SNAKEMAKE 
In the folder '/PRIME_cluster' of this repository, there are two additional files needed to use snakemake in the PRIME. 

First, you might want to clone this repository:

> git clone https://github.com/martavp/pypsa-in-prime.git

Copy the files 'cluster.yaml' and 'snakemake_cluster' to the directory '.../pypsa-eur-sec/' in your folders in the cluster. 


Then, to run your simulations using Snakemake, you only need to write the following instruction in the command line (jobs identify the number of jobs that you want to parallelize if you send more that one job simultaneously). 

> ./snakemake_cluster --jobs 5

#### 6. Permission
It is possible that you need to give execution permissions to snakemake_cluster, you can do it typing in the terminal.

> chmod u+x snakemake_cluster

#### 7. Log files
Create a directory 'logs/cluster", as indicated in the file 'cluster.yaml'. This is where the logs and error files will be saved. Make sure that a folder 'logs/cluster' also exists in 'pypsa-eur/logs/cluster'.

#### 8. Memory allocation
Check that the variable names in 'snakemake_cluster' comply with the variable names in your Snakefile. In particular, check that the memory attribution 
(mem_mb) is the same in both files or correct if necessary. If any of the rule in 'pypsa-eur/Snakefile' is missing 'resources: mem_mb=' add it or substitute 'mem' by 'mem_mb'. 17-feb 2022: I (Ebbe) added a snakefile for _pypsa-eur 0.4.0_ in the folder _PRIME_cluster_ in which the 'resources: mem_mb' is now defined in all rules.

#### 9. Setting up Gurobi in the cluster  

On the PRIME-cluster, Gurobi needs to be pointed in the right direction as to where to look for packages and licenses. The first step is to add the following lines to the end of the file '.bashrc' located in /home/(AU-ID), as indicated in the [Gurobi guide](https://www.gurobi.com/documentation/6.5/quickstart_linux/software_installation_guid.html):

> export GUROBI_HOME="/home/com/meenergy/gurobi651/linux64"

> export PATH="${PATH}:${GUROBI_HOME}/bin"

> export LD_LIBRARY_PATH="${GUROBI_HOME}/lib"

Additionally, the following line should be added at the end of the file '.bashrc':

> export GRB_LICENSE_FILE="$GUROBI_HOME/gurobi.lic"

This points Gurobi to the cluster-license. Note that an academic license used locally on a computer is unsuitable for use on the cluster, and will result in a failed simulation.

## Running simulations

## Typical Errors

#### 15. Solution to "Solver (gurobi) returned non-zero return code (127)"

A change needs to be made to the file 'gurobi.sh' located in /home/(AU-ID)/anaconda3/envs/(pypsa-eur_environment_name)/bin/gurobi.sh . The last line of this shell script needs to point to 'python2.7', 
regardless of what Python version is used in the pypsa-eur environment in your local folder. Thus, the last line of 'gurobi.sh' needs to be:
 
> $PYTHONHOME/bin/python2.7 "$@"

Make sure to restart the terminal for these modifications to take effect.


#### 16. Solution to "memory error". 

The config file should include a path to a folder where the temporal files during the solving of the network are saved. Best practice is to use the scratch memory:

> tmpdir: "scratch/$SLURM_JOB_ID"

Another option is to use your home folder:

> tmpdir: "/home/marta/tmp"

If this path is not specified, the [default is to use the directory where the script is being executed](https://github.com/PyPSA/pypsa-eur/blob/2e70e8d15b722e818efb57cf72b35a9536340365/scripts/solve_network.py#L281) which can cause errors due to not enough space in PRIME.  


#### 18. Memory resources
I (Marta) have manually increased the resources in rule build_renewable_profiles to speed up that rule in the cluster.

> resources: mem=ATLITE_NPROCESSES * 50000

#### 19. Using PyPSA-Eur
If you are using pypsa-eur independently of pypsa-eur-sec, to make sure that pypsa-eur gets to the final networks (with the solution), a rule all needs to be added to the Snakefile. 
In practice, this means adding the following text: 

>rule all:

>    input:
    
>        expand("results/networks/elec_s_{simpl}_{clusters}_ec_l{ll}_{opts}.nc",
>                **config['scenario'])



## Extra stuff that will make your life easier

#### 20. Terminal multiplexer (optional, but useful)

If you get disconnected or close your terminal your execution ends. If you want to simulate over an extended period of time this needs to be obmitted. What you need to use is a terminal multiplexer. In the following there are listed two alternatives.

##### 20a. GNU Screen

This is the easier choice as it is already installed. 
Starting Named Session by typing:
> screen -S session_name
You can detach from the screen session at any time by typing:
> Ctrl+a d
This means that it will run in the background.
To resume your screen session use the following command:
> screen -r
To find the session ID list the current running screen sessions with:
> screen -ls

##### 20b. TMUX

TMUX is another terminal multiplexer but needs to be installed first. 
ATTENTION: The execution of the workflow in a tmux-window defined in the Snakefile may result in the 'solve_network' rule to fail. This is different from system to system, but if it occurs, it can be solved by executing the rule 'solve_network' outside tmux.  
When

> ./snakemake_cluster --jobs 1

is executed in the cluster, the workflow in the 'Snakefile' starts. The DAG of jobs will be built, and depending on how many jobs have been allowed to run in parallel with the execution command above, one or more jobs will be submitted to the cluster with their own unique job-ID. If one chooses to close the terminal window in which the cluster is accessed, only the submitted jobs will be executed. For example if the rule 'cluster_network' is the last job to be submitted, this job will finish, but the subsequent rules will not. Because of this, one must wait until the final rule, 'solve_network', has been submitted if one wants to for instance log off the cluster.  
A solution for this, that allows for inputting the executing command and immediately logging off the cluster, is a terminal multiplexer called 'tmux'. This allows for having multiple windows in the same terminal window. To install it, make sure to have an Anaconda environment active in the cluster terminal window, and execute the following:

> conda install -c conda-forge tmux

Next, a new tmux-session can be created by executing:

> tmux new -s type_session_name_here

In this session, 'snakemake_cluster' can then be executed. When the workflow is running, the tmux-session can then be detached from, i.e. return to the normal PRIME-cluster window, by typing 'Ctrl+B', to get the attention of tmux, relatively quickly followed by typing 'd'. To reattach, execute the following in the terminal:

> tmux a -t type_session_name_here

If one has forgotten the name of the session when trying to reattach, simply execute:

> tmux

To get a list of the created sessions. The tmux commands described here, as well as many other neat ones, can be found  in this [article](https://www.howtogeek.com/671422/how-to-use-tmux-on-linux-and-why-its-better-than-screen/).

#### 21. Environment file that works for Mac (17/5-2021)

This environment file (./environments/environment_pypsa_eur_macos.yml) works for pypsa-eur-sec on MacOs. It may also work on other systems (not testet). 

#### 22. VS Code 

***VS Code must be installed on your local computer, not on the cluster***

[Visual Studio Code](https://code.visualstudio.com/) is a handy tool when working on the cluster. It allows you to have your file explorer, [python editor](https://code.visualstudio.com/docs/python/python-tutorial), and terminal in one window. Install the [Remote - SSH extension](https://code.visualstudio.com/docs/remote/ssh) to connect with PRIME.

If you experience issues with connecting VScode to prime, try setting the option "Remote.SSH: Lockfiles in Tmp" to true (check the box). 

To commit from your prime repository to your github, go to the *source control* and give your commit a name and press ctrl + enter. If you want the commit to be pushed automatically, after having committed, go to settings --> Remote [SSH: prime.eng.au.dk] --> Git --> Post Commit Command --> change "none" to "push"

#### 23. Avoid entering password when connecting to PRIME

On your local computer:

Generate a ssh key by running:

> (Local path) > ssh-keygen

Press _Enter_ for default key name. Then _Enter_ for no password, and then _Enter_ again to confirm. A password key is created under *Local path* in the file _"id_rsa.pub"_. Coppy the key to the cluster by running the folling command:

> ssh-copy-id -i ~/.ssh/id_rsa.pub prime.eng.au.dk

Enter password when prompted. 


#### 24. 2021/08/31 As of today, I (Marta) have everything running on the cluster nicely with the following versions:
pypsa=0.18.0; 
pypsa-eur=0.3.0, 
pypsa-eur-sec=0.5.0, 
technology-data=0.2.0. 
In case someone needs a reference of a compatible setup of packages.

#### 25. Example
For the ones who have just started using the PRIME cluster with only one rule in the Snakefile, but wants to run in parallel with e.g. a range of different inputs, I have added a simple example of how this can be done in the folder _'cluster_test'_. You can modify the _python_script_ and the Snakefile to match it to your application. 
