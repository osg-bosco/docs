%META:TOPICINFO{author="KyleGross" date="1481047997" format="1.1"
version="1.17"}% %META:TOPICPARENT{name="BoSCO"}%

# Bosco Quick Start Guide

<span class="twiki-macro DOC_STATUS_TABLE"></span>
<span class="twiki-macro TOC" data-depth="3"></span>

\---\# Introduction This is a quick introduction to your personal Bosco
installation. Check the [main document](BoSCO) to find out more about
what Bosco is and how it works. Check the [Install
document](BoscoInstall) for detailed installation requirements and all
the installation options.

`bosco_quickstart` is a script that installs and sets up Bosco. The
script will take care of:

1.  Installing Bosco in `~/bosco`, if Bosco is not already installed
2.  Connecting one cluster (submit host) with Bosco
3.  Sending a test job to verify that the cluster is configured
    correctly and is working

And, `bosco_quickstart` will also suggest what to use to submit jobs.

Bosco is a tool to connect you to the resources you already have access
to. Bosco connects to a **submit host**. The submit host must be a
machine on which you can log in, for example with **`ssh
!username@submit.mycluster.mydomain`**. Given the ability to log in, you
can submit jobs to a local PBS, HTCondor, LSF or SGE queue manager, for
example using commands like **`qsub`** and **`condor_submit`**. In this
document and in Bosco, we use the term **cluster**, because the submit
host normally gives access to a set of worker nodes by way of a queue (a
[traditional cluster](http://en.wikipedia.org/wiki/Computer_cluster)),
but sometimes it may give access to multiple resources where jobs can be
queued. A cluster could even be a gateway to Cloud and Grid resources
(see OSG-XSEDE in the Support section below).

<span class="twiki-macro WARNING"></span> If you have a previous version
of Bosco installed and you'd like to install Bosco 1.2, then you should
first remove that previous version with **`source ~/bosco/bosco_setenv;
bosco_uninstall --all`**.

\---\# Install, set up Bosco, and connect to one cluster

With a single command you will install, set up, and connect Bosco to one
cluster.

Download Bosco [from this download
page](http://bosco.opensciencegrid.org/download/). We recommend the
Quickstart Multi-Platform installer. If you prefer to work in a terminal
window, you can also copy the URL that will be printed in the [download
page](http://bosco.opensciencegrid.org/download/) and use cURL:

``` screen
%UCL_PROMPT% curl -o bosco_quickstart.tar.gz ftp://ftp.cs.wisc.edu/GET_THE_URL_FROM_THE_PAGE/bosco_quickstart.tar.gz
```

Untar and invoke the **`bosco_quickstart`** script from a terminal with
a current working directory of the \~/Download folder or the folder in
which you saved the file:

``` screen
%UCL_PROMPT% tar xvzf ./bosco_quickstart.tar.gz
%UCL_PROMPT% ./bosco_quickstart
```

If Bosco was not previously installed, when asked "Do you want to
install Bosco? Select y/n and press \[ENTER\])", answer "y" and press
ENTER to confirm that you'd like to continue installing Bosco. The
script will print information while downloading and installing Bosco.

The script will then ask you three questions about the cluster you want
to connect to Bosco. Follow each answer by pressing ENTER:

  - "Type the cluster name and press \[ENTER\]:" This is the full name
    of the cluster login node, for example `submit.mycluster.mydomain`
  - "Type your name at CLUSTER\_NAME (default CURRENT\_NAME) and press
    \[ENTER\]:" This is the `username` that you use to log in on the
    cluster you want to connect. If you leave this empty Bosco will
    assume that it is the same as your current username on the Bosco
    host
  - "Type the queue manager for CLUSTER\_NAME (pbs, condor, lsf, sge,
    slurm) and press \[ENTER\]" This is the program that is used to
    manage the jobs in the cluster you want to connect.Examples of such
    programs are PBS, HTCondor, LSF, and SGE. If you don't know what is
    installed, ask the system administrator of the cluster.

You will then be prompted for the password that you use when logging in
to the cluster that you are connecting to Bosco. If you are using ssh
keys to connect to that cluster please see the NOTE (1) below.

<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_OUTPUT% showlink="Click
to see an example of a quickstart execution:"</span>

``` screen
%UCL_PROMPT% ./bosco_quickstart
Bosco Quickstart
detailed logging is in ./bosco_quickstart.log

Bosco is not installed. You need Bosco to run this quickstart.
Do you want to install Bosco? Select y/n and press [ENTER]): y
************** Downloading and Installing Bosco ***********
Installing BOSCO.......
BOSCO Installed
************** Starting Bosco: ***********
BOSCO Started
************** Connecting one cluster (resource) to BOSCO: ***********
At any time hit [CTRL+C] to interrupt.

Type the submit host name for the Bosco resource and press [ENTER]: uc3-sub.uchicago.edu
Type your username on uc3-sub.uchicago.edu (default mmb) and press [ENTER]:
Type the queue manager for uc3-sub.uchicago.edu (pbs, condor, lsf, sge, slurm) and press [ENTER]: condor
Adding uc3-sub.uchicago.edu, user: mmb, queue manager: condor
mmb@uc3-sub.uchicago.edu's password:
...............................................................
uc3-sub.uchicago.edu added
************** Testing the cluster (resource): ***********
This may take up to 2 minutes... please wait..........................................................................
BOSCO on uc3-sub.uchicago.edu Tested
************** Congratulations, Bosco is now setup to work with uc3-sub.uchicago.edu! ***********
You are ready to submit jobs with the "condor_submit" command.
Remember to setup the environment all the time you want to use Bosco:
source ~/bosco/bosco_setenv

Here is a quickstart guide about Bosco:
https://twiki.grid.iu.edu/bin/view/CampusGrids/BoscoQuickStart

To remove Bosco you can run:
source ~/bosco/bosco_setenv; bosco_uninstall --all

Here is a submit file example (supposing you want to run "myjob.sh"):
universe = grid
Executable = myjob.sh
arguments =
output = myjob.output.txt
error = myjob.error.txt
log = myjob.log
transfer_output_files =
should_transfer_files = YES
when_to_transfer_output = ON_EXIT
queue 1
```

<span class="twiki-macro ENDTWISTY"></span>

<span class="twiki-macro NOTE"></span> (1) You must be able to login to
the remote cluster. If password authentication is OK, the script will
ask you for your password. If key only login is allowed, then you must
load your key in the `ssh-agent`. Here is an example adding the key and
testing the login:
<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> \<pre
class="screen"\> <span class="twiki-macro UCL_PROMPT"></span> eval
\`ssh-agent\` Agent pid 17103;
<span class="twiki-macro UCL_PROMPT"></span> ssh-add id\_rsa\_bosco
Enter passphrase for id\_rsa\_bosco: Identity added: id\_rsa\_bosco
(id\_rsa\_bosco) <span class="twiki-macro UCL_PROMPT"></span> ssh
<uc3@itbv-ce-pbs> Last login: Thu Sep 13 13:49:33 2012 from
uc3-bosco.mwt2.org $ logout \</pre\>
<span class="twiki-macro ENDTWISTY"></span>

<span class="twiki-macro NOTE"></span> (2) Some clusters have multiple
login nodes behind a round robin DNS server. You can recognize them when
you log in to the node (for example: **`ssh login.mydomain.org`**), as
it will show a name different from the one used to connect (for example,
**`hostname -f`** returns **`login2.mydomain.org`**). If this happens
you must connect the Bosco resources by using a name of the host, not
the DNS alias (for example: **`bosco_cluster --add
login2.mydomain.org`**). This is because sometimes these multiple login
nodes do not share all the directories and Bosco may be unable to find
its files if different connections land on different hosts. The
following example shows this:
<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> Note
how `midway-login2.rcc.uchicago.edu` must be used instead of
`midway.rcc.uchicago.edu`: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> ssh
<mm@%RED%midway.rcc.uchicago.edu%ENDCOLOR>%
<mm@midway.rcc.uchicago.edu's> password:
**`===========================================================================`**
Welcome to Midway Research Computing Center University of Chicago ...
**`===========================================================================`**

\[<mm@midway-login2> \~\]$ hostname -f
%RED%midway-login2.rcc.uchicago.edu<span class="twiki-macro ENDCOLOR"></span>
\[<mm@midway-login2> \~\]$ logout Connection to midway.rcc.uchicago.edu
closed. <span class="twiki-macro UCL_PROMPT"></span> marco$
bosco\_cluster --add <mm@%RED%midway-login2.rcc.uchicago.edu%ENDCOLOR>%
Warning: No batch system specified, defaulting to PBS If this is
incorrect, rerun the command with the batch system specified

Enter the password to copy the ssh keys to
<mm@midway-login2.rcc.uchicago.edu>:
<mm@midway-login2.rcc.uchicago.edu's> password: Detecting PBS cluster
configuration...bash: cannot set terminal process group (-1): Invalid
argument bash: no job control in this shell bash: qmgr: command not
found Done\! Downloading for <mm@midway-login2.rcc.uchicago.edu>....
Unpacking......... Sending libraries to
<mm@midway-login2.rcc.uchicago.edu>... Creating BOSCO for the
WN's....................................................................
Installation complete The cluster <mm@midway-login2.rcc.uchicago.edu>
has been added to BOSCO It is available to run jobs submitted with the
following values: \> universe = grid \> grid\_resource = batch pbs
<mm@midway-login2.rcc.uchicago.edu>
<span class="twiki-macro UCL_PROMPT"></span> \</pre\>
<span class="twiki-macro ENDTWISTY"></span>

Once the quickstart script completes successfully, you can remove both
the script and the log file:

``` screen
%UCL_PROMPT% rm ./bosco_quickstart ./bosco_quickstart.log
```

These directions will give you a personal installation of Bosco,
connected to a single resource. **To learn how to add more resources and
how to customize your Bosco installation, see the [full install
guide](BoscoInstall).**

\---\#\# How to use Bosco \#SetupEnvironment Since Bosco is not
installed in the system path, a file containing environment variables
must be sourced to set the variables for all Bosco commands, such as
start, stop, job submission and query. Source the file with:

``` screen
%UCL_PROMPT% source ~/bosco/bosco_setenv
```

\---\# How to submit a job After installation, jobs may be submitted.
Submission is demonstrated with a simple example. For more examples and
options, check the [main Bosco document](BoSCO).

Job submission with Bosco incorporates 3 items: 1. Have an executable 1.
Prepare a submit description file 1. Submit the job

\---\#\# The Executable This example uses a bash script, called
`myjob.sh`: \<pre class="file"\> \#/bin/bash

\# Prepare for execution

\# Run the actual applications hostname date id whoami

\# Final steps

\</pre\>

Make sure that the file has permissions set such that it is executable:
\<pre class="screen"\><span class="twiki-macro UCL_PROMPT"></span> chmod
+x myjob.sh\</pre\>

You may wish to run this script locally to test it and to see the
output: \<pre
class="screen"\><span class="twiki-macro UCL_PROMPT"></span>
./myjob.sh\</pre\>

\---\#\# The Submit Description File This submit description file will
submit the script as a job to the remote cluster, using the HTCondor
grid universe. Put the following into a file called `example.sub`:

``` file
universe = grid
executable = myjob.sh
arguments = 
output = output
error = error
log = myjob.log
transfer_output_files = 
should_transfer_files = YES
when_to_transfer_output = ON_EXIT
queue 1
```

This sample file contains HTCondor commands with nothing to the right of
the `=` sign. These are commands that are not used in this example, but
are included such that this sample file may be more easily used by
modification on further, more complex examples.

\---\#\# Job Submission Submit the job in the file `example.sub` with
the `condor_submit` command: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> condor\_submit example.sub
\</pre\>

\---\#\# Job Monitoring Monitor the job with `condor_q`. This example
shows the job in the idle state: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> condor\_q

\-- Submitter: uc3-c001.mwt2.org : \<10.1.3.101:45876\> :
uc3-c001.mwt2.org ID OWNER SUBMITTED RUN\_TIME ST PRI SIZE CMD 12.0
marco 7/6 13:45 0+00:00:00 I 0 0.0 short2.sh 10

1 jobs; 0 completed, 0 removed, 1 idle, 0 running, 0 held, 0 suspended
\</pre\>

The job will be in the idle state if it is currently idle at the remote
cluster. When the job is being executed on the remote cluster, the `ST`
(State) will be `R`, and the `RUN_TIME` will grow.

Another method for monitoring a job is to check the job's `log`, a human
readable, chronologically ordered set of events relating to the job. The
`log` for this example was specified in the submit description file as
named `myjob.log`. You can view the file by using `cat`: \<pre
class="screen"\> <span class="twiki-macro UCL_PROMPT"></span> cat
**.log** \</pre\>

\---\#\# Job output Once the job completes, Bosco will transfer back to
the local machine standard output, standard error and any output files
specified in the submit description file. The example did not specify
any; they would be listed within the `transfer_output_files` command.
These files will be placed in the current working directory, as set when
the `condor_submit` command was executed.

\---\# What's next Modify the example submit description file to specify
your job.

In further documentation, Bosco jobs are the same as HTCondor jobs. More
features of job submission are described on these pages about HTCondor
jobs:

  - Tutorial:
    <http://research.cs.wisc.edu/htcondor/tutorials/alliance98/submit/submit.html>
  - Condor manual:
    <http://research.cs.wisc.edu/htcondor/manual/v8.0/2_5Submitting_Job.html>

To learn more about Bosco (v1.2), check these pages:

  - [BoSCO Web site](http://bosco.opensciencegrid.org/)
  - [Using Bosco](BoSCO)
  - [Installing BoSCO](BoscoInstall) - Full installation and set up
    guide (includes adding more resources, sending custom job
    attributes, ...)
  - [Installing BoSCO Multi User](BoscoMultiUser) - Have your site
    administrator install Bosco for all the users on the submit node.
  - [Bosco tests for developers and testers](TestBoSCO) - If you want to
    learn more and help us.

\---\# Get Help/Support For further assistance, you can send an email to
<bosco-discuss@opensciencegrid.org> .

[Open Science Grid](http://www.opensciencegrid.org/) is a collaboration
that can provide access to opportunistic computing cycles. To access OSG
via the [OSG-XSEDE
gateway](https://www.opensciencegrid.org/bin/view/Trash/Trash/VirtualOrganizations/OSGasXsedeSp),
you can write a request to <osg-xsede-support@opensciencegrid.org> .
Once you have access to osg-xsede, you can connect to it using Bosco.

# Comments

<span class="twiki-macro COMMENT" type="tableappend"></span>
