

# SkeletonKey Tutorial 1: Installation


## Introduction
This page introduces the user to SkeletonKey and guides the user through installing the SkeletonKey software and setting up SkeletonKey up for initial use.  After completing the guide, the user should be able to install SkeletonKey, start and stop the chirp server, and create a simple job to be run using Parrot and Chirp.

## Prerequisites
The following items are needed in order to complete this tutorial:
   * Webserver where the user can place files to access using the web
   * HTCondor Cluster (optional)

## Conventions
In the examples given in this tutorial, text in %RED%red%ENDCOLOR% denotes strings that should be replaced with user specific values.  E.g. the URL for the user's webserver.  In addition, this tutorial will assume that files can be made available through the webserver by copying them to ~/public_html on the machine where SkeletonKey is being installed.

## Installation
SkeletonKey uses a python script to install and set things up for the user.  The installation procedure is as outlined below:

   1. First download the SkeletonKey installer script 
```
$ wget uc3-data.uchicago.edu/sk/install-skeletonkey.py
```
   2. Pick a directory to install the CCTools and SkeletonKey binaries in (e.g. bin in your home directory).  Ideally this directory should be in $PATH.  
```
$ mkdir ~/bin
```
   3. Pick a directory to export from Chirp (for now the tutorial will use /tmp)
   4. Run the installer, specifying the directory to install and the  directory to export from Chirp: 
```
$ python install-skeletonkey.py -b ~/bin -e /tmp
```
   5. Add the directory specified in -b option (e.g. ~/bin) to $PATH: 
```
$ PATH=$PATH:~/bin
```
   6. Edit ~/.profile and append the following line: 
```
export PATH=$PATH:~/bin
```

## Hello World example
The first example in this guide is will a simple hello world example. This example will just go through the mechanics of running skeleton key and generating a job wrapper.
### Setting up binaries
In order for the job wrapper that SkeletonKey provides to work correctly, you'll need to make the cctools binaries available on a webserver.  The SkeletonKey installer created a file called parrot.tar.gz in the =~/bin= that you'll need to copy to your webserver and make it available over http: 
```
$ cp ~/bin/parrot.tar.gz %RED%~/public_html%ENDCOLOR%
$ chmod 644 %RED%~/public_html/parrot.tar.gz%ENDCOLOR%
```

### Creating the job wrapper
You'll need to do the following on the machine where you installed SkeletonKey
   1. Open a file called hello_world.ini and add the following lines: 
```
[Parrot]
location = %RED%http://your.host/parrot.tar.gz%ENDCOLOR%

[Application]
script = /bin/hostname
```
   2. In hello_world.ini, change the url http://your.host/parrot.tar.gz to point to the url of the parrot tarball that you copied previously.
   3. Run SkeletonKey on hello_world.ini: 
```
$ skeleton_key -c hello_world.ini
```
    4. Finally, run the job wrapper to verify that it's working correctly 
```
$ sh ./job_script.sh
--2013-04-04 12:04:49--  http://uc3-data.uchicago.edu/~sthapa/parrot.tar.gz
Resolving uc3-data.uchicago.edu... 128.135.158.217
Connecting to uc3-data.uchicago.edu|128.135.158.217|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 11012919 (11M) [application/x-gzip]
Saving to: `parrot.tar.gz'

100%[=================================================================================>] 11,012,919  --.-K/s   in 0.1s

2013-04-04 12:04:49 (75.8 MB/s) - `parrot.tar.gz' saved [11012919/11012919]

fermicloud071.fnal.gov
```

In the ini file used by SkeletonKey, two sections are used.  The SkeletonKey used the location setting in the Parrot section to determine where it can download Parrot binaries to use when running user applications.  In the =Application= section, the =script= setting indicates the command to run in the Parrot environment.

### Using the job wrapper
#### Standalone
Once the job wrapper has been verified to work, it can be copied to another system and run: 
```
$ scp job_script %RED%another_host:~/%ENDCOLOR%
$ ssh %RED%another_host%ENDCOLOR%
[user@another_host] sh ./job_script
```

#### Submitting to HTCondor (Optional)
The following part of the tutorial is optional and will cover using a generated job wrapper in a !HTCondor submit file.  
   1. On your !HTCondor submit node, create a file called sk.submit with the following contents 
```
universe = vanilla
notification=never
executable = ./job_script.sh
output = /tmp/sk/test_$(Cluster).$(Process).out
error = /tmp/sk/test_$(Cluster).$(Process).err
log = /tmp/sk/test.log
ShouldTransferFiles = YES
when_to_transfer_output = ON_EXIT
queue 10
```
   2. Next, create /tmp/sk for the log and output files for condor 
```
[user@condor-submit-node] mkdir /tmp/sk
```
   3. Then copy the job wrapper to the !HTCondor submit node 
```
$ scp job_script.sh %RED%condor-submit-node%ENDCOLOR%:~/
```
   4. Finally submit the job to !HTCondor and verify that the jobs ran successfully
```
$ ssh %RED%condor-submit-node%ENDCOLOR%
[user@condor-submit-node] condor_submit sk.submit
```
## Using an application tarball
The next example will guide users through creating an application tarball that can be used with SkeletonKey. 
   1. First create a directory, /tmp/myapp where the script file will go. 
```
$ mkdir /tmp/myapp
```
   2. Then edit /tmp/myapp/myapp.sh and add the following lines 
```
#!/bin/bash
/bin/hostname
/bin/echo "Application completed"
```
   3. Next, make sure the myapp.sh script is executable and create a tarball: 
```
$ chmod 755 /tmp/myapp/myapp.sh
$ cd /tmp
$ tar cvzf myapp.tar.gz myapp
```
    4. Then copy the tarball to your webserver 
```
$ cd /tmp
$ cp myapp.tar.gz %RED%~/public_html%ENDCOLOR%                                  
$ chmod 644 %RED%~/public_html/myapp.tar.gz %ENDCOLOR%
```
    5. Next create a file called myapp.ini and add the following lines to it: 
```
[Parrot]
location = %RED%http://your.host/parrot.tar.gz%ENDCOLOR%

[Application]
location = %RED%http://you.host/myapp.tar.gz%ENDCOLOR%
script = ./myapp/myapp.sh
```
    6. Then, generate a job wrapper using SkeletonKey 
```
$ skeleton_key -c myapp.ini
```
    7. Finally, verify that the job wrapper is working correctly by running it 
```
$ sh ./job_script.sh
```

This ini file uses an addition setting when compared to the hello world example.  The  SkeletonKey  uses the location setting in the Application section is used to determine if it needs to download application binaries.  If this setting is given,  SkeletonKey uses the value it's set to as an url to the tarball to download and extract (in a temporary directory) before running the command given in the =script= setting.  Additionally, SkeletonKey runs the command given in the =script= setting in the same directory that the tarball is expanded in, so it is set to a relative path point to the location of the extracted shell script (=myapp.sh=).
 
### Using the job wrapper
#### Standalone
Once the job wrapper has been verified to work, it can be copied to another system and run: 
```
$ scp job_script %REDanother_host:~/%ENDCOLOR%
$ ssh %RED%another_host%ENDCOLOR%
[user@another_host] sh ./job_script
```

#### Submitting to HTCondor (Optional)
The following part of the tutorial is optional and will cover using a generated job wrapper in a !HTCondor submit file.  
   1. On your !HTCondor submit node, create a file called sk.submit with the following contents 
```
universe = vanilla
notification=never
executable = ./job_script.sh
output = /tmp/sk/test_$(Cluster).$(Process).out
error = /tmp/sk/test_$(Cluster).$(Process).err
log = /tmp/sk/test.log
ShouldTransferFiles = YES
when_to_transfer_output = ON_EXIT
queue 10
```
   2. Next, create /tmp/sk for the log and output files for condor 
```
[user@condor-submit-node] mkdir /tmp/sk
```
   3. Then copy the job wrapper to the !HTCondor submit node 
```
$ scp job_script.sh %RED%condor-submit-node%ENDCOLOR%:~/
```
   4. Finally submit the job to !HTCondor and verify that the jobs ran successfully
```
$ ssh %RED%condor-submit-node%ENDCOLOR%
[user@condor-submit-node] condor_submit sk.submit
```
