# KLC Guide: Installing R Packages

* We use the `renv` package to maintain consistency in package versions across each team member's computer and the server. Once you have cloned the git repo, you will want to restore the package versions that we have been using. To do so, first open R through Terminal. 
    ```
    module load R/4.1.1 # `module avail R` to see what versions are available
    R
    install.packages("renv") # only need to run this if renv is not installed
    renv::restore()
    ```
    
Ideally, this would install and restore all the packages to the same version we are using on other computers being used for the project. However, in practice, I rarely found that this was the case. Specifically, I had a lot of issues with being able to install the `rJava` and `magick` packages. 

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
