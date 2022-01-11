# KLC Guide

Instructions for using the Kellogg Linux Cluster (KLC) for our research projects.
 
## Connect to the server

1. Use your NetID to connect via VPN. [Instructions](https://kb.northwestern.edu/page.php?id=94726). Note that every time you want to connect to the server, you first need to connect to the VPN.

2. If you have a Mac, open the terminal. If you have Windows, first install [Cygwin](https://www.cygwin.com/) so that you can use Linux commands from the command line, then you can open the command line with Windows+R, type cmd, Enter.

3. In the terminal or command line, type:
    ```linux
    ssh <netID>@klc.ci.northwestern.edu
    ```
    where you should replace <netID> with your NetID made up of letters and numbers, e.g. mine is skh2820.

4. Enter the password you created for your netID.

5. Now you should be connected to the correct folder through terminal. Next you need to `cd` to the “parent folder” for our project (which starts with `/kellogg/proj/skh2820/` followed by the project folder name). Note that here you want to leave skh2820 rather than use your own NetID, since that’s the folder we are using and they should have given you access to that folder.

6. Here you can type `ls` to see the folder structure, which is the same as our Dropbox folder. I usually use `ls -ltr` to list all files with the date they were created and the most recent at the bottom. You can also do this for subfolders e.g. `ls -ltr scripts` or `ls -ltr logs`

## Upload files    

7. To upload new files, e.g. raw data or scripts that you’ve edited and need to run on the server, you need an FTP client. I use [FileZilla](https://filezilla-project.org/). Another option is [CyberDuck](https://cyberduck.io/). For FileZilla, once you open it put:
    - Host: klc.ci.northwestern.edu
    - Username: (your NetID, i.e. the letter and number combination)
    - Password: (your password for your NetID)
    - Port: 22
Then you’ll see your local folder on the left pane and the server folder on the right pane. On the left navigate to your local directory for the project folder on dropbox, and on the right navigate to the project folder on the server. Then you can upload files by double-clicking them or selecting them, right-click, upload. (Note: make sure you upload them to the correct folder on the server, e.g. upload scripts to the scripts folder.) 

## Run scripts    

8. For Stata, best practice is to always use the "run script" (`00_run.do`) to run the files you want to run on the server. You can see how we set that up in my [Stata Guide](https://github.com/skhiggins/Stata_guide). Basically you create objects for each script and then you set those objects equal to 1 if you want them to run and 0 otherwise.

9. Once you have the scripts uploaded, you can run them as follows (where my comments are after `#`, don’t include that in the command). Note that you must be in the project root directory (not one of its subfolders) to run these commands.
    ```linux
    # For Stata: 
    module load stata/15 # This is the most recent version on the server
    ls -ltr scripts # I always run this first to make sure the scripts I
                    # want to run uploaded correctly.
    nohup stata-mp -b do scripts/00_run.do &
        # Note: nohup is so that if you get logged out the script keeps
        #  running. The & is so that while the command is running, you get
        #  the command prompt back and can do other things.
    # Stata note: the do files you run (not 00_run.do itself but the scripts
        that are run by 00_run.do) should always create a log file, and
        the name of the log file should start with the name of the do file,
        followed by a timestamp.
        
    # For R:
    moudule load R/4.1.1 # module avail R to see what versions are available
    ls -ltr scripts # I always run this first to make sure the scripts I
                    # want to run uploaded correctly.
    nohup R CMD BATCH --vanilla -q scripts/scriptname.R logs/scriptname.log &
        # Replace scriptname with the name of your script
    # R note: all of the output will be stored in logs/scriptname.log
    ```

10. When the proc files, logs, graphs, etc. are ready on the server, use FileZilla again to download them to the Dropbox folder.

## Misc.  

11. Some additional tips for using the Linux Server:
    ```linux
    ps x # check if the job is still running and which jobs are running
    ls -ltr logs # double check your script is running
                 # as it should create a log file
    tail logs/logfile.log # check the status of your job by viewing the 
        # last 10 lines of the log file, where logfile is replaced with the 
        # name of the file
    tail -100 logs/logfile.log # shows the last 100 lines of the log file
        # if you need to see more
    # For Stata:
    tail 00_run.log # The nohup stata-mp... command above always 
        # creates a 00_run.log file (in the project root directory, not in 
        # logs) which you can look at to debug in case something goes 
        # really wrong and your log files don’t even get created.
    # Note: an alternative to `tail` is `more`, which shows the file 
    #  starting from the beginning and you hit enter to show more of it.
    ```

12. Some additional tips on installing R packages on the server can be found [here](R_packages.md).
    