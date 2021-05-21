# KLC Guide

### Accessing KLC 
* To connect to the server, you have to be connected to the VPN. [Instructions](https://kb.northwestern.edu/page.php?id=94726) found here.
* Use terminal to connect to the server rather than using a GUI
* If you have a Mac, open the terminal. If you have Windows, first install Cygwin so that you can use Linux commands from the command line, then you can open the command line with Windows+R, type cmd, Enter.
* In the terminal or command line, type:
```ssh <netID>@klc.ci.northwestern.edu```
where you should replace <netID> with your NetID made up of letters and numbers, e.g. mine is skh2820.
* Enter the password you created for your netID.
* Now you should be connected to the correct folder through terminal. 
* You can now change your directory as normal to the project you are working on. For example, you’ll need to
``` cd /kellogg/proj/skh2820/iZettle_fee ```
which are the “parent folders” for our projects. Note that here you want to leave skh2820 rather than use your own NetID, since that’s the folder we are using and Sean should have requested shared access to that folder.
* Clone the git repo here
* To upload new files, e.g. do files that you’ve edited and need to run on the server, you need an FTP client. I use [FileZilla](https://filezilla-project.org/). Another option is [CyberDuck](https://cyberduck.io/). For FileZilla, once you open it put:
·         Host: klc.ci.northwestern.edu
·         Username: (your NetID, i.e. the letter and number combination)
·         Password: (your password for your NetID)
·         Port: 22
* Then you’ll see your local folder on the left pane and the server folder on the right pane. On the left navigate to your local directory for the iZettle_fee project, and on the right navigate to /kellogg/proj/skh2820/iZettle_fee. Then you can upload files by double-clicking them or selecting them, right-click, upload. (Note: make sure you upload them to the correct folder on the server, e.g. upload scripts to the scripts folder.) 

### Dealing with R Packages
* We use the `renv` package to maintain consistency in package versions. Once you have cloned the git repo, you will want to restore the package versions that we have been using. To do so, first open R through Terminal. 
```
module load R/4.0.3 # or whatever the newest version is, check `module avail R`
R
renv::restore()
```
Ideally, this would install and restore all the packages to the same version we have been using. However, in practice, I rarely found that this was the case. Specifically, I had a lot of issues with being able to install the `rJava` and `magick` packages. 

* If `renv::restore()` command works perfectly, then you do **not need to do these steps**. However, if one of the packages failed to install, we are going to set up a conda environment to deal with this. 

* First, we need to create an environment with the rJava binaries already compiled.
```
# Load the required java and conda 
module load java/jdk11.0.10
module load python/anaconda3.6
 
# Create an environment called r-env
conda create -y -n r-env
source activate r-env
 
# Install rJava 
conda install -y -c conda-forge r-base==4.0.3
conda config --add pinned_packages 'r-base==4.0.3' 
conda install -y -c r r-rjava==1.0.4
```
If that last line doesn't work, try using `conda install -y -c conda-forge r-rjava=1.0.4` instead. That is what Research Support recommended I do and it ended up working. 

* Next, we are also having trouble with the `magick` package, so we also need to follow some steps to get this installed. 
```
conda install -y -c conda-forge imagemagick --yes
LD_LIBRARY_PATH="/home/<netid>/.conda/envs/rmagick-dependencies/lib:$LD_LIBRARY_PATH" # Replace <netid> with your netid
module load R/4.0.3
R 
install.packages(“magick”)
```

* This should solve the problems for these two packages. Generally, I think setting up a conda environment with the binaries already compiled is the best way to deal with package issues if `renv::restore()` does not work seamlessly. 
* Another note is that the .Rprofile and .Rhistory objects might cause some issues with the `rJava` package so the above steps only worked after deleting those.

* Finally, after setting up the conda environment with the necessary packages, we will have to run the following steps each time we log in. 
```
module load java/jdk11.0.10
module load python/anaconda3.6
source activate r-env
```

### Running Scripts
*  Best practice is to always use a 00_run.R script to run the files you want to run on the server. You can see how we set that up in the file – basically you create objects for each script and then you set those objects to 1 if you want them to run and 0 otherwise. (See the R Guide for an example 00_run.R)
* Once you have the do files that you want to run uploaded and have updated 00_run.do to run the correct files, you can run them as follows (where my comments are after #, don’t include that in the command). Note that you must be in the parent folder (not one of its subfolders) to run these commands, e.g. in /kellogg/proj/skh2820/iZettle_fee
	* `module load R/4.0.3` [or latest; check what’s available with `module avail R`] 
	* `ls -ltr scripts` # I always run this first to make sure the scripts I want to run uploaded correctly
	* `nohup R CMD BATCH --vanilla -q scripts/00_run.R logs/00_run.log &`
		* note: `nohup` is so that if you get logged out the script keeps running. The `&` is so that while the command is running, you get the command prompt back and can do other things.
* Note that using R CMD BATCH automatically creates a log file which we named logs/00_run.log above.
* Some additional tips for using the Linux Server:
	* `ps x` # check to make sure Stata is still running and which jobs it’s running
	* `ls -ltr logs` # double check your script is running, as it should create a log file
	* `tail logs/00_run.log` # check the status of your job by viewing the last 10 lines of the log file
	* `tail -100 logs/00_run.log` # shows the last 100 lines of the log file if you need to see more
	* Note: an alternative to `tail` is `more`, which shows the file starting from the beginning and you hit enter to show more of it.
	* Note: you can also download the log file through FileZilla/Cyberduck to see the whole thing
* When the proc files, logs, graphs, etc. are ready on the server, use FileZilla/Cyberduck again to download them to the dropbox folder