# LHST student projects - Technical documentation

Information for EPFL students doing a MA/BA semester project or master project at EPFL-LHST.

See also information in these [slides](https://docs.google.com/presentation/d/1z6iEMMcHeRg0eRZqO0SqqvbVMh0pFAl-WYM3wPpPHRI/edit?usp=sharing) (EPFL only).


- [GitHub repository](#your-github-repository)
- [Working with a remote EPFL server](#working-with-a-remote-EPFL-server)


## GitHub repository:

**Naming:**

- use lower case;
- use hyphens to separate tokens;
- if related to a larger project, start with the name of this project, followed by the name of your project (e.g. `impresso-image-classification`);
- in case of doubt, ask your supervisors;

**Structure:**    
You are free to structure your repository as you wish, but we advise to have:

- a `notebooks` folder, for your working notebook;
- a `lib` folder, in case you convert your notebook in scripts with a command line interface;
- a `report` fodler, where you put the PDF and latex sources of your report;
- a README, with the information specified below.


**What should be in your README:**

- **Basic information**
    - your name
    - the names of supervisors
    - the academic year
- **About**: include a brief introduction of your project.
- **Research summary**: include a brief summary of your approaches/implementations and an illlustration of your results.
- **Installation and Usage**
    - dependencies: platform, libraries (for Python include a `requirements.txt` file)
    - compilation (if necessary)
    - usage: how to run your code
- **License**    
    We encourage you to choose an open license (e.g. AGPL, GPL, LGPL or MIT).    
    License files are already available in GH (add new file, start typing license, choices will appear).    
    You can also add the following at the end of your README:       
   
	    project_name - Jean Dupont    
	    Copyright (c) Year EPFL    
	    This program is licensed under the terms of the [license]. 
	    


## Working with a remote EPFL server

If necessary the LHST can grant you access to a machine on the IC cluster:

- ask your supervisor to gain access;
- you need to be on campus or use VPN to access the machine
- login with gaspar credentials: ```ssh [gasparname]@iccluster0NN.iccluster.epfl.ch where 'NN' is the machine number```
- the node usually has 
	- 256GB of RAM, 
	- 2 GPUs
	- 200GB of disk space on `/`
	- 12TB of disk space under `scratch`
-  :warning: **important**: the machine is shared and given the small size of `/`, do not store your data (i.e. the data you work with, intermediary results, models, various resources, etc.) in your `home` but under `scratch/students` where you can do your own folder. 


### For people using Python on a cluster node

- create a **local** python environment using conda or pipenv. You need to ensure your environment is on the `/scratch/`, not `/home/`, to do so check guides just below.
- to easily code locally and run things remotely, configure your IDE to save your code on the remote serveur as you code (e.g. with [PyCharm](https://www.jetbrains.com/help/pycharm/creating-a-remote-server-configuration.html), [Visual Studio Code](https://code.visualstudio.com/docs/remote/ssh-tutorial)).

#### pipenv

Note: the following procedure for pipenv has not been throughly tested, if you test it and there are other steps, please update this document via a pull request

To ensure you are not using `/home`, there are two things to do: 1) ensure your pipenv environments are not installed in `/home`, 2) ensure pip's temporary directory (where pip downloads the files before loading them into the environment) is not on `/home`.

1) Create the temporary directory `mkdir /scratch/<your-scracth-folder>/.pipenv_tmpdir`	
2) Add the following lines to your `/home/<user>/.bashrc/``file:
```sh
export PIPENV_VENV_IN_PROJECT=1 # tells pip to create the environment in the folder where you're creating it.
export TMPDIR="/scratch/<your-scracth-folder>/.pipenv_tmpdir" # tells pip to use this folder as the temporary directory
```

Some sources: [temporary directory](https://github.com/pypa/pip/issues/5816), [create pipenv in current directory](https://stackoverflow.com/questions/50598220/pipenv-how-to-force-virtualenv-directory)

#### conda

Before creating conda environments, you need to execute the following two commands. They configure conda so that environments (and packages) are stored on the `/scratch/` instead of the `/home/`:
```sh
conda config --add envs_dirs /scratch/$USER/.conda/envs
conda config --add pkgs_dirs /scratch/$USER/.conda/pkgs
```

Moreover, it is good practice to regularly clean your conda packages: `clean conda --all`

Documentation: [envs_dirs](https://conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html#specify-environment-directories-envs-dirs), [pkgs_dirs](https://conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html#specify-package-directories-pkgs-dirs ), [clean](https://conda.io/projects/conda/en/latest/commands/clean.html)

### How to access a notebook on a remote server

To access a Jupyter notebook or Jupyter lab running on a remote server, you can use a SSH tunnel:

1. Launch Jupyter notebook or lab on the node. As the port number XXXX, use the last four digits of your SCIPER number (to avoid colliding with other people on the same port):    
`jupyter notebook --no-browser --port=XXXX`
2. Connect a second time to the remote server through SSH, setting up a SSH tunnel. Run the following command from your local machine (again replacing XXXX with the same port number):    
`ssh -L XXXX:localhost:XXXX [gasparname]@iccluster0NN.iccluster.epfl.ch`

Your notebook is now accessible at `https://localhost:XXXX`. 

In order to leave it open while you are executing things, you should run the notebook in a `screen` (see below). This also helps in case of disconnections.

### How to create another shell session and detach from is: use `screen`     

**Main commands:**

- create a session: `screen -S name_of_the_session`  =>  you are in
- go out of a session: `Ctrl-A D` => session still running, you are out
- reconnect to a session: `screen -r name_of_the_session`
- kill a session: from within the session, `Ctrl-A K`
- in case you are reconnecting from inside (by mistake):  `screen -rd name_of_the_session`
- to list all active screens: `screen -ls`

**Documentation:**

- Tutorial: [https://linuxize.com/post/how-to-use-linux-screen/](https://linuxize.com/post/how-to-use-linux-screen/) 
- Full documentation: [https://linux.die.net/man/1/screen](https://linux.die.net/man/1/screen)
- Also available via `man screen`

**Steps to work with a notebook in a screen:**

- `cd [your repo]`
- `screen -S work` => you are in a screen named "work" where you will launch the notebook
- activate your env
- start the notebook as above (`jupyter notebook`)
- check the url `http://iccluster0XX.iccluster.epfl.ch:XXXX` (where XXXX is the chosen port number)
- if all ok then exit the screen (`Ctr-a d`). You can now work in the notebook, open and close your browser as you want, it will keep running.


