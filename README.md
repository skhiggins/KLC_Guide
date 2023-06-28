# KLC Guide

Instructions for using the Kellogg Linux Cluster (KLC) for our research projects.

*For broader programming guides, see my [R Guide](https://github.com/skhiggins/R_guide), [Python Guide](https://github.com/skhiggins/Python_guide) and [Stata Guide](https://github.com/skhiggins/Stata_guide).*

------------------------------------------------------------------------

## Overview

Processing of large datasets should be done on KLC. What is a large dataset? As a rule of thumb, any dataset approximating your RAM size will not run well on your computer. Additionally, any processes that are slow and can be parallelized (for example, performing estimations using machine learning) should be run on the server. The workflow is the following:

1.  **Upload data and scripts to server.**

    -   Upload scripts to GitHub.

    -   Upload data with FileZilla to KLC.

2.  **Process data on the server**

3.  **Download results**

    -   Push results to GitHub and download locally (i.e. on your own computer), or

    -   Download processed data files with FileZilla.

## Set up

1.  Download and install the required programs to access the KLC server:
    1.  [**Global Protect VPN**](https://services.northwestern.edu/TDClient/30/Portal/KB/ArticleDet?ID=1818):Necessary if you want to connect to KLC from outside campus.
    2.  [**FileZilla**](https://filezilla-project.org/): An FTP client is used for transferring files to and from the server. Another option is [CyberDuck](https://cyberduck.io/).
    3.  [**Cygwin**](https://www.cygwin.com/): If you have Windows, this is necessary for using Linux commands from the command line.
2.  Set up GitHub on the server:
    -   You can find a more detailed explanation on [this guide](https://github.com/skhiggins/ra_guide#setting-up-an-existing-repo-on-the-server-or-a-new-computer). Here is a summary of the commands needed to perform the setup. Make sure to substitute your username and project name, and get a [personal access token](Git%20fetch%20using%20personal%20access%20token%20instead%20of%20password%20(https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)) to use instead of password (after `git fetch`).

        ```{bash eval=FALSE}
        cd existing_folder
        git init
        git remote add origin https://github.com/user123/myproject
        git fetch
        git checkout origin/main -ft
        ```

------------------------------------------------------------------------

## **1. Upload data and scripts to server**

Scripts should be synced using GitHub. Data files (raw and processed) are excluded from version control by including the **data** and **proc** folders in the `.gitignore` file, so you will need to upload data files through an FTP client such as Filezilla.

#### 1.1. Upload scripts to GitHub

-   Again, you can look at this [in-depth guide](https://github.com/skhiggins/ra_guide#ii-working-with-github) on how to work with GitHub if you're not familiar with it. You simply need to `git push` locally and `git pull` from the server.

#### 1.2. Upload data with FileZilla to KLC

1.  **Enter login details:** Start by entering your login details on FileZilla:
    -   **Host**:

        -   First. look at the list of available nodes [here](https://www.kellogg.northwestern.edu/research-support/computing/kellogg-linux-cluster.aspx) and select the one with the most open cores and ram.

        -   Then, input that as the host. For example, *`klc0307.ci.northwestern.edu`.*

    -   **Username**: (your NetID, i.e. the letter and number combination)

    -   **Password**: (your password for your NetID)

    -   **Port**: 22

        <img src="https://github.com/skhiggins/KLC_Guide/blob/main/images/klc_login.png" align="center" height="60%" width="60%">        

2.  **Connect:** Click on **Quickconnect**.
3.  **Upload files:** Drag files from local folders to server folders
    -   On FileZilla, local folders are displayed on the left pane and server folders on the right pane:

        <img src="https://github.com/skhiggins/KLC_Guide/blob/main/images/klc_window.png" align="center" height="60%" width="60%">        

    -   Navigate on the **left** (local folders) to your local directory for the project folder.

    -   Navigate on the **right** (server folders) to the project folder on the server.

    -   Drag files from the left to the right folders to upload.

        -   An alternative is to drag files from any folder in your system directly to the server folders. This is easier if you already have the local project folder open.

## **2. Process data on the server**

#### 2.1. Connect to the server

1.  **Connect off-campus:** If you are not on campus, use your NetID to connect via VPN. Note that every time you want to connect to the server, you first need to connect to the VPN if you are not on campus. Remember to disconnect from the VPN once done using the server.

2.  **Open the terminal/command-line:** If you have a Mac, open the terminal. If you have Windows, you can open the command line with Windows+R, type cmd, Enter (remember to install [Cygwin](https://www.cygwin.com/) so that you can use Linux commands from the command line).

3.  **Input username and node:** In the terminal or command line, type the following:

    ```{bash eval=FALSE}
    ssh <netID>@klc<node>.ci.northwestern.edu
    ```

    Where you should replace `<netID>` with your NetID made up of letters and numbers, e.g. abc1324, and `<node>` with the node you select from [this list](https://www.kellogg.northwestern.edu/research-support/computing/kellogg-linux-cluster.aspx), e.g. 0307.

4.  **Input password:** Enter the password you created for your NetID.

Now you should be connected to the server.

#### 2.2. Change directory to the project folder

1.  **Change directory:** Next, you need to `cd` to the "parent folder" of the project you're working on. For example:

    ```{bash eval=FALSE}
    cd my_project
    ```

    In projects which are stored on Sean's folder, you will type instead

    ```{bash eval=FALSE}
    cd /kellogg/proj/skh2820/my_project
    ```

    You can email Kellogg Research Support and copy Sean to gain access to this folder.

2.  **Review folder structure:** Here you can type `ls` to see the folder structure. You can also type `ls -ltr` to list all files with the date they were created and the most recent at the bottom. This command can also be applied to subfolders, e.g. `ls -ltr scripts` or `ls -ltr logs`.

#### 2.2. Load modules

1.  **Review modules:** Search for the most recent version of the program you're using for (e.g., R or Stata) by typing `module avail <program>/`, substituting `<program>` for your program of choice. For example, `module avail stata`.

    -   You can skip this step if you already know what the most recent module of your program is.

    -   When looking for R modules, don't forget to type the `/`, otherwise you will find all modules containing the word *R*. Some additional tips on installing R packages on the server can be found [here](R_packages.md).


2.  **Load modules:** e.g.:

    ```{bash eval=FALSE}
    module load R/4.2.0
    ```

#### 2.3. Run scripts

1.  **Pull scripts from GitHub:** `git pull` the scripts you need from GitHub.
2.  **Run script:**
    1.  It's best practice to use a `00_run` script on R and Stata to run the files you want to run on the server. You can find the setup in the [Stata guide](https://github.com/skhiggins/Stata_guide) or in the [R guide](https://github.com/skhiggins/r_guide). If you only have one script to run on the server, then you can simply run that script by itself.

    2.  Run the scripts the following way.

        For R:

        ```{bash eval=FALSE}
        nohup R CMD BATCH --vanilla -q scripts/scriptname.R logs/scriptname.log &
        ```

        -   For R scripts, all of the output will be stored in logs/scriptname.log.

        For Stata:

        ```{bash eval=FALSE}
        nohup stata-mp -b do scripts/scriptname.do &
        ```

        -   For Stata do files, the scripts you run (not 00_run.do itself but the scripts that are run by 00_run.do) should always create a log file, and the name of the log file should start with the name of the do file, followed by a timestamp. For example:

            ``` stata
            local project "my_project"

            ** LOG **
            time_stamp
            start_log using "${logs}/`project'_`time'.log"

            ********* (RUN SCRIPT) *********

            ** WRAP UP **
            log close
            ```

        For both R and Stata:

        -   `nohup` is so that if you get logged out the script keeps running,

        -   `&` is so that while the command is running, you get the command prompt back and can do other things,

        -   You must be in the project root directory (not one of its subfolders) to run these commands.
3.  **Check script progress:** You can review the progress of your scripts by typing the following commands:
    -   `ps x`: Check if the job is still running and which jobs are running.

    -   `ls -ltr logs`: Double check if log files are being created in the folder *logs*, and when they were last updated.

    -   `tail logs/logfile.log`: Check the status of your job by viewing the last 10 lines of the log file.

    -   `tail -100 logs/logfile.log`:Shows the last 100 lines of the log file if you need to see more.

        -   For Stata, you can run `tail 00_run.log` to review the log file created in the project root directory. The `nohup stata-mp` command always creates this log whih you can look at to debug in case something goes wrong and your log files aren't even recorded. An alternative to `tail` is `more`, which shows the file starting from the beginning and you hit enter to show more of it.

    -   `kill <proc_number>`: Kills a process. You can find the job/process number with `ps x`.

## **3. Download results**

#### 3.1. Download figures and tables

To download figures and tables, push results to GitHub and download locally (i.e. on your own computer).

#### 3.2. Download processed data

To download processed data files, you can use FileZilla to download them to your local project folder.
