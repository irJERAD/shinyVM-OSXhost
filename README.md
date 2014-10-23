Shiny
=====
Description
-----------
This will create and run a local Shiny Server for R to enable viewing of the 
sample Shiny application and the creation your own Shiny app. This is 
suitable for both local development and future deployment of a Shiny Server 
with your app.

Creating the server requires very few commands since the process is 
almost entirely automated by Fabric. The commands described below will create
a virtual machine (VM) running Ubuntu Server 14.04 and install the necessary 
R and Shiny packages onto the VM, with R Markdown enabled (allowing the 
server to serve Markdown docs). Shiny app files will be editable using your 
host machine. The Shiny server will be accessed in your browser at 
*localhost:7070*. This should greatly simplify the process of creating a 
Shiny Server.
Requirements
------------
If you are using Windows the requirements and process should be similar, 
if not exactly the same. The installation of Python/Fabric is likely the 
only challenge, but both work on Windows if installed correctly. For ease, 
use Mac OS X or Linux.

System Requirements: 2GB RAM Min. and 3GB Free HD Space

Install the following:

+ **VirtualBox:** https://www.virtualbox.org/wiki/Downloads
+ **Vagrant:** https://www.vagrantup.com/downloads
+ **Fabric:** http://www.fabfile.org/installing.html

It is likely that you already have Python, which Fabric requires. If you are 
unsure, open Terminal and execute `python`.

The requirements above occupy ~200 megabytes of space (largely from 
VirtualBox and Vagrant). They can be removed once you are done with Shiny 
Server, but it is suggested that you do not uninstall them since they are 
common development tools that you might use in the future.

You also need to have an SSH key, since it will be used when setting up your 
VM with Vagrant. See the GitHub instructions for [checking for and creating 
an SSH key][1]. You do not need to go through Step 3 and 4 in their 
instructions, and if you find a key named `id_rsa` in Step 1 then you can 
also skip Step 2.
Installation
------------
Clone or download this repository onto your computer. Then open your Terminal
and `cd` into the repository on your computer. Run `vagrant up` to create 
the virtual machine. Finally, run `fab vagrant setup_vagrant` to install and
set up the Shiny Server and its dependencies. That's it! The last line in 
your terminal should give you the status of your Shiny Server. Open your 
web browser and using the address bar visit *localhost:7070* to view the 
sample app.

To shutdown the server execute `vagrant halt`. This will stop the VM. To boot 
the server again execute `vagrant up`. You do not need to execute `fab 
vagrant setup_vagrant` if the process completed successfully earlier.

If things do not run as expected see the Troubleshooting section below. 
Creating Apps
-------------
During the setup process, the Shiny Server on your VM was directed to look in a 
new shared folder called **project** that was created in your repository. When 
you view the sample Shiny app in your browser, the Shiny Server reads the 
files in this folder. Any modifications to files in this folder will be 
visible to the VM, so you can simply delete the sample app and develop your app
in **project/** on your host machine.

If your app requires any additional R packages, SSH into the VM with `vagrant
ssh` (make sure you are in the **shiny** folder) and install them. For 
example, you can install the deSolve package with `vagrant ssh` and then 
`sudo R -e "install.packages('deSolve', repos='http://cran.rstudio.com/')"`. 
Uninstallation
--------------
Since everything is installed into a VM, you can easily remove the VM to 
uninstall the server and reclaim disk space. In your **shiny** folder, 
execute `vagrant destroy` and then `vagrant box remove 'ubuntu/trusty64'`.
Troubleshooting
---------------
For starters, know that you can destroy and reinitiate your VM and run 
`fab vagrant setup_vagrant` as many times as you want. This is especially 
useful if the setup process is interrupted. Some more specific problems and 
solutions are below.

+ If you see multiple `Connection timeout: Retrying....` warnings after running 
`vagrant up` and the process seems frozen, it is likely that you do not have an 
SSH key. See the link in the Requirements section for instructions on how to
create one.
+ If you had to install any of the requirements (VirtualBox, Vagrant, 
Fabric), try restarting your computer and beginning again. Make sure to 
destroy your VM before starting over.
+ If you see an error regarding an installation of Paramiko when executing 
`fab vagrant setup_vagrant`, remove Fabric and reinstall Paramiko: open 
Terminal and execute `sudo pip uninstall fabric`, then `sudo pip install 
paramiko==1.10`, then `sudo pip install fabric`.
+ If you do not see the **project** folder in your **shiny** folder (or 
**shiny-master** if you downloaded a .zip of the repo) after you run `vagrant
up`, then Vagrant might be facing permissions issues when trying to 
create the folder. Delete your VM, create a blank folder called **project** 
in your repo, and start over. See below for the expected folder structure.

After `vagrant up` completes, your directory structure should look like this:  
*/shiny/*  
|-- fabfile.py  
|-- .gitignore  
|-- *project/*  
|-- README.md  
|-- Vagrantfile

When `fab vagrant setup_vagrant` is done, you should have this:  
*/shiny/*  
|-- fabfile.py  
|-- .gitignore  
|-- *project/*  
|&nbsp;&nbsp;&nbsp; |-- index.html  
|&nbsp;&nbsp;&nbsp; |-- *sample-apps/*  
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |-- *hello/*  
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;
&nbsp; |-- server.R  
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;
&nbsp; |-- ui.R  
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |-- *rmd/*  
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp; |-- index.Rmd  
|-- README.md  
|-- Vagrantfile

Any discrepancies in the above should give you some indication of where 
things went wrong.

[1]: https://help.github.com/articles/generating-ssh-keys/
