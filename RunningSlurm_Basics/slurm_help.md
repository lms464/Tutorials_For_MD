# Over Simplfied Super Computer Guide
## What is this?

Hi! So you've joined a computational lab, congrats. Chances are you are going to have to run some script/program/analysis/something on a computer better than yours (a super computer). This short guide has been put together to give you a general introduction into running your work on Rutgers super computers.

(As an aside, the initial author (Liam Sharp) has assembed this based on his experiences with running on super computers, your results and requirements may varry. I am assuming you are familiar with linux machines.)

## Getting Started

First and formost, make sure you've got an account . You can find the request form here:

Give them a bit of time to get back to you.

### Loggin On

**NOTE: Described for Unix users** 

Getting set up on  you will need to use the command 

    ssh netid@computer.dept/school.edu

You will then be asked to type in your password (should be the same as the one you use to access your workstation)

If this is the first time you access amarel from your workstation/laptop/..., you will be asked to confirm you want to do this. 

### Simple Commands

Tells you all the nodes and generally what is available

    sinfo

Tells you what jobs of *yours* are running

    squeue -u netid

Tells you what jobs for *everyone* are running

    squeue

List out the modules and utilities that you can run.

    module spider

If you have a module in mind (chances are you do), type in module spider *app*. For example, I use gromacs:

    module spider gromacs/2016.1
    
    gromacs: gromacs/2016.1
    Description:
      GROMACS: A molecular dynamics package primarily designed for biomolecular systems
      such as proteins and lipids. 

    This module can only be loaded through the following modules:

      intel/17.0.1  mvapich2/2.2
 
    Help:
       
      This module loads the installation of GROMACS 2016.1 compiled with MVAPICH2 2.2 and the
    Intel 17.0.1.

If the module requests other modules, load the requested modules first! But how do we load a module? Based of the gromacs example, I need to load intel/17.0.1 and mvapich2/2.2, before I can load gromacs/2016.1. :

    module load intel/17.0.1
    module load mvapich2/2.2
    module load gromacs/2016.1
or
    module load intel/17.0.1  mvapich2/2.2 gromacs/2016.1
    
(Asside: I suggest setting up your $HOME to load these automatically through .bashrc)

If you are unsure what modules you have open try:

    module list

If you need to clear your modules:

    module purge


# Choosing parameters to run
While there appears to be a few ways to do this, we are going to build a run script. Start by making a new file:

    vi example.sh

Focussing on the headers, you start off with Bash shebang:

    #!/bin/bash
    
The next n lines will all begin with #SBATCH. It presents the computer with a list of instructions on how to run.
    
    #SBATCH --partition=PartitionOfYourChoice #(take a look at the amarel website for a list of partitions)
    #SBATCH --job-name=SimulationTitle        #(name it something unique, it should tell you what is running)
    #SBATCH --output=slurm.%N.%j.out          #(an output file informing you what your code is doing)
    #SBATCH --error=slurm.%N.%j.err           #(an error file, if not used will send to output file)
    #SBATCH --nodes=N                         #(number of nodes you are going to use)
    #SBATCH --ntasks=n                        #(number of cores per node you will use)
    #SBATCH --cpus-per-task=c                 #(number of tasks per core)
    #SBATCH --time=dy:hr:mn                   #(how long your simulation can run for see amarel website for a list of maximum times)

There are definatly more options, but these should get you started on running programs. 

# Using multiple nodes

If you have to use multiple nodes, *bench mark*. There is a sweet spot that gives you the best run time, but it is variable from system to system. If you have to run an MPI job try the following:

* #SBATCH --nodes=1, #SBATCH --ntasks=2
* #SBATCH --nodes=1, #SBATCH --ntasks=4
* #SBATCH --nodes=1, #SBATCH --ntasks=6
* #SBATCH --nodes=1, #SBATCH --ntasks=8

Then test with 2, 3, 4... While it is tedious, it is important, and will help you in the long run.

# Running a job

Do to the varriety of set ups, this part is left blank and will hopefully be filled in at a later time.

Generally you will use the command s run in your run script:

    #SBATCH --partition=PartitionOfYourChoice #(take a look at the amarel website for a list of partitions)
    #SBATCH --job-name=SimulationTitle        #(name it something unique, it should tell you what is running)
    #SBATCH --output=slurm.%N.%j.out          #(an output file informing you what your code is doing)
    #SBATCH --error=slurm.%N.%j.err           #(an error file, if not used will send to output file)
    #SBATCH --nodes=N                         #(number of nodes you are going to use)
    #SBATCH --ntasks=n                        #(number of cores per node you will use)
    #SBATCH --cpus-per-task=c                 #(number of tasks per core)
    #SBATCH --time=dy:hr:mn                   #(how long your simulation can run for see amarel website for a list of maximum times)
    
    srun Program2Run -OtherCmds

Finally to run the script:

    sbatch example.sh
    
This will send your requested program to a queue to wait to be run. If your script does not run right away that's ok, there could be jobs ahead of it.

# For extra help

* [Amarel Guide](https://rutgers-oarc.github.io/amarel/) - Ammarel User Guide
* [srun help](https://slurm.schedmd.com/srun.html) - srun Manual
* [Stampede2](https://docs.tacc.utexas.edu/hpc/stampede2/) - Stampede2 Manual

