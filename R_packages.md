# KLC Guide: Installing R Packages

Below are some notes 

### Dealing with R Packages
* We use the `renv` package to maintain consistency in package versions across each team member's computer and the server. Once you have cloned the git repo, you will want to restore the package versions that we have been using. To do so, first open R through Terminal. 
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