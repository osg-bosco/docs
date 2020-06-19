%META:TOPICINFO{author="KyleGross" date="1481047997" format="1.1"
version="1.29"}% \<div style="background-color:\#ffa07a; padding:7px;
-moz-border-radius: 5px; border-radius: 5px;"\> This is an older
release. Please go to [BOSCO](Trash/CampusGrids.BoSCO) for the latest
release. \</div\>

# BOSCO

<span class="twiki-macro DOC_STATUS_TABLE"></span>
<span class="twiki-macro TOC" data-depth="3"></span>

\---\# Introduction

BOSCO is a job submission manager designed to help researchers manage
large numbers (\~1000s) of job submissions to the different resources
that they can access on a campus (initially a PBS cluster running
Linux). This is the first release of BOSCO, v1, if you find any problems
or need help installing or running BoSCO, please email
<bosco-discuss@opensciencegrid.org> .

It offers the following capabilities:

  - Jobs are automatically resubmitted when they fail. The researcher
    does not need to babysit their jobs.
  - Job submissions can be throttled to meet batch scheduler settings
    (e.g. only 10 jobs running concurrently). The researcher does not
    need to make multiple submissions. BOSCO handles that for them.
  - BOSCO is designed to be flexible and allows jobs to be submitted to
    multiple clusters, with different job schedulers (e.g. PBS, LSF,
    Condor).

The primary advantage for the researcher is that they only need to learn
one job scheduler environment even if the clusters utilize different
native environments.

<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED%
showlink="Click to see the format conventions used in this document"
</span>
<span class="twiki-macro INCLUDE" data-section="Header">Trash/DocumentationTeam.DocConventions</span>
<span class="twiki-macro INCLUDE" data-section="CommandLine">Trash/DocumentationTeam.DocConventions</span>
<span class="twiki-macro ENDTWISTY"></span>

\---\# Requirements

  - **Submit-node**  
    This is the system that the researcher uses to submit jobs. In
    general it can be the user's laptop, workstation, or it can be
    another system that the user logs into for submitting jobs to the
    cluster. A current requirement is that the submit-node must use the
    same Linux flavor that the PBS/LSF/Condor cluster is using. **There
    can not be any Condor collector running on the submit node**,
    otherwise it will conflict with the Campus Factory.
  - **Cluster head-node**  
    This is the node that you normally login to on the PBS, LSF or
    Condor cluster.
      - **PBS flavors supportted**  
        Torque and PBSPro
      - **Condor flavors supported**  
        Condor 7.6 or later
      - **LSF flavors**  
        no special requirements
  - **Cluster**  
    This is the remote cluster that jobs will execute on. The Cluster
    head-node is a node belonging to this cluster. The cluster needs:
      - **Shared Filesystem**  
        The Cluster needs a shared home filesystem
      - **Network Access**  
        The worker nodes need to have access to the submit host. The
        worker nodes can be behind a
        [NAT](https://en.wikipedia.org/wiki/Network_address_translation)
        between the worker nodes and the submit host.

BOSCO can be used as part of a more complex Condor setup (with flocking
or multiple pools). Whatever is the setup:

  - the BOSCO host needs connectivity to the cluster submit nodes
  - the worker nodes (running the jobs, e.g. the nodes in the PBS
    cluster) must have network connectivity to the jobs submit node (the
    BOSCO host or a different Condor schedd flocking into it)

\---\# How to Install

\---\#\# Installation Procedure

1.  Download BOSCO
      - BOSCO comes in 3 different flavors depending on your Linux
        version. To find your Linux distribution and version type `cat
        /etc/*-release`. Right click on the appropriate link below
        (depending on your linux version), select "save as" and save the
        file in your home directory
          - [RHEL 5 or Scientific Linux 5 or Centos 5
            - 64bit](ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-1.0-x86_64_rhap_5.tar.gz)
          - [RHEL 6 or Scientific Linux 6 or Centos 6
            - 64bit](ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-1.0-x86_64_rhap_6.tar.gz)
          - [Debian 6
            - 64bit](ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-1.0-x86_64_deb_6.tar.gz)
      - Alternatively you can use `wget` and the correct link:\<pre
        class="screen"\>

\# If you have RHEL 5 or Scientific Linux 5 or Centos 5
<span class="twiki-macro UCL_PROMPT"></span> cd \~
<span class="twiki-macro UCL_PROMPT"></span> wget
<ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-1.0-x86_64_rhap_5.tar.gz>
\</pre\>

1.  Unpack the tar file and Install BOSCO: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> cd \~
<span class="twiki-macro UCL_PROMPT"></span> mkdir tmp-bosco
<span class="twiki-macro UCL_PROMPT"></span> cd tmp-bosco
<span class="twiki-macro UCL_PROMPT"></span> tar xvzf
\~/bosco-1.0-x86\_64\_rhap\_5.tar.gz
<span class="twiki-macro UCL_PROMPT"></span> cd condor-7.9\*
<span class="twiki-macro UCL_PROMPT"></span> ./bosco\_install
<span class="twiki-macro UCL_PROMPT"></span> cd \~
<span class="twiki-macro UCL_PROMPT"></span> rm -r tmp-bosco \</pre\>

<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> \<pre
class="screen"\> -bash-3.2$ mkdir tmp-bosco -bash-3.2$ cd tmp-bosco
-bash-3.2$ wget
<ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-beta-x86_64_rhap_5.7.tar.gz>
--2012-07-12 14:00:00--
<ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-beta-x86_64_rhap_5.7.tar.gz>
=\> \`bosco-beta-x86\_64\_rhap\_5.7.tar.gz' Resolving ftp.cs.wisc.edu...
128.105.2.28 Connecting to ftp.cs.wisc.edu|128.105.2.28|:21...
connected. Logging in as anonymous ... Logged in\! ==\> SYST ... done.
==\> PWD ... done. ==\> TYPE I ... done. ==\> CWD /condor/bosco/latest
... done. ==\> SIZE bosco-beta-x86\_64\_rhap\_5.7.tar.gz ... 23975235
==\> PASV ... done. ==\> RETR bosco-beta-x86\_64\_rhap\_5.7.tar.gz ...
done. Length: 23975235 (23M)

100%\[================================================================================================================================================================\>\]
23,975,235 72.0M/s in 0.3s

2012-07-12 14:00:01 (72.0 MB/s) -
\`bosco-beta-x86\_64\_rhap\_5.7.tar.gz' saved \[23975235\]

\-bash-3.2$ tar xzf bosco-beta-x86\_64\_rhap\_5.7.tar.gz -bash-3.2$ cd
condor-7.9.1-51830-x86\_64\_rhap\_5.7-stripped/ -bash-3.2$
./bosco\_install Installing Condor from
/share/home/marco/tmp-bosco/condor-7.9.1-51830-x86\_64\_rhap\_5.7-stripped
to /share/home/marco/bosco cp: cannot stat
\`/home/marco/bosco/etc/condor/config.d/condor\_config.factory': No such
file or directory

Condor has been installed into: /share/home/marco/bosco

Configured condor using these configuration files: global:
/share/home/marco/bosco/etc/condor\_config local:
/share/home/marco/bosco/local.uc3-c001/condor\_config.local Created a
script you can source to setup your Condor environment variables. This
command must be run each time you log in or may be placed in your login
scripts: source /share/home/marco/bosco/bosco\_setenv

\-bash-3.2$ source /share/home/marco/bosco/bosco\_setenv \</pre\>
<span class="twiki-macro ENDTWISTY"></span>

If the installation fails, specially because of missing dependencies,
please check that you downloaded the correct version for your Linux
version.

\---\# How to Use

Now BOSCO is installed. To use it:

1.  Setup the environment
2.  Add all the desired clusters (at least one)
3.  Start BOSCO
4.  Submit a test job
5.  Submit a real job

\---\#\# Setup environment before using Since BOSCO is not installed in
the system path. An environment file must be sourced all the times you
use BOSCO (start/stop/job submission or query, anything):

``` screen
%UCL_PROMPT% source ~/bosco/bosco_setenv
```

\---\#\# Starting BOSCO BOSCO has some persistent services that must be
running. You'll have to start it at the beginning and probably after
each reboot of your host. You should stop BOSCO before an upgrade and
possibly before a shutdown of your host. If you will not use BOSCO
anymore, uninstall will remove it from your system.

To start BOSCO:\<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> bosco\_start \</pre\>

\---\#\# Add a cluster to BOSCO To add a new cluster to the resources
you will be using through BOSCO:

1.  Setup the environment appropriate for your shell as described in the
    setup environment section (above).
2.  For the cluster
    %RED%mycluster<span class="twiki-macro ENDCOLOR"></span> with user
    %RED%username<span class="twiki-macro ENDCOLOR"></span> and submit
    host
    %RED%mycluster-submit.mydomain.org<span class="twiki-macro ENDCOLOR"></span>
    (Fully Qualified Domain Name, aka full hostname including the domain
    name) and Local Resource Management System
    %RED%LRMS<span class="twiki-macro ENDCOLOR"></span> (valid values
    are `pbs`, `condor` or `lsf`). Replace the parts in red: \<pre
    class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> bosco\_cluster --add
%<RED%username@mycluster-submit.mydomain.org>
LRMS<span class="twiki-macro ENDCOLOR"></span> \</pre\>
<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> \<pre
class="screen"\> -bash-3.2$ bosco\_cluster -add itbv-ce-pbs.uchicago.edu
Enter password to copy ssh keys to itbv-ce-pbs.uchicago.edu: The
authenticity of host 'itbv-ce-pbs.uchicago.edu (128.135.158.176)' can't
be established. RSA key fingerprint is
8e:a6:db:18:80:6b:b7:de:56:c8:5a:a2:75:19:11:8d. Are you sure you want
to continue connecting (yes/no)? yes Warning: Permanently added
'itbv-ce-pbs.uchicago.edu,128.135.158.176' (RSA) to the list of known
hosts. Installing BOSCO on itbv-ce-pbs.uchicago.edu... Installation
complete \</pre\> <span class="twiki-macro ENDTWISTY"></span>

When you add your first cluster, BOSCO will prompt you for a password
that will be used to store the SSH keys used by BOSCO to access all your
clusters (`Enter password for bosco ssh key:`). Select a random string.
It is preferable if you do not use the password you use to access the
cluster or to unlock your SSH keys.

Then, if you don't have a ssh key agent with that cluster enabled, you
will be prompted for the password that you use to access the cluster you
are adding to BOSCO (`Enter password to copy ssh keys to ...`). This may
be followed by a confirmation of the RSA key fingerprint, if it is your
first ssh connection from this host, where you have to answer `yes`.

<span class="twiki-macro IMPORTANT"></span> You must be able to login to
the remote cluster. If password authentication is OK, the script will
ask you for your password. If key only login is allowed, then you must
load your key in the `ssh-agent`. Here is an example adding the key and
testing the login:
<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> \<pre
class="screen"\> <span class="twiki-macro UCL_PROMPT"></span> ssh-agent
SSH\_AUTH\_SOCK=/tmp/ssh-WdHXb17102/agent.17102; export SSH\_AUTH\_SOCK;
SSH\_AGENT\_PID=17103; export SSH\_AGENT\_PID; echo Agent pid 17103;
<span class="twiki-macro UCL_PROMPT"></span>
SSH\_AUTH\_SOCK=/tmp/ssh-WdHXb17102/agent.17102; export SSH\_AUTH\_SOCK;
<span class="twiki-macro UCL_PROMPT"></span> SSH\_AGENT\_PID=17103;
export SSH\_AGENT\_PID; <span class="twiki-macro UCL_PROMPT"></span>
ssh-add id\_rsa\_bosco Enter passphrase for id\_rsa\_bosco: Identity
added: id\_rsa\_bosco id\_rsa\_bosco
<span class="twiki-macro UCL_PROMPT"></span> ssh <uc3@itbv-ce-pbs> Last
login: Thu Sep 13 13:49:33 2012 from uc3-bosco.mwt2.org $ logout
\</pre\> <span class="twiki-macro ENDTWISTY"></span>

When adding the cluster, if the last message is `Done!`. Your cluster
has been added successfully.

You can see a list of the current clusters in BOSCO by typing:\<pre
class="screen"\> <span class="twiki-macro UCL_PROMPT"></span>
bosco\_cluster --list \</pre\>
<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> \<pre
class="screen"\> -bash-3.2$ bosco\_cluster --list
itbv-ce-pbs.uchicago.edu \</pre\>
<span class="twiki-macro ENDTWISTY"></span>

\---\#\# Submitting a test job You can send a simple test job to verify
that the cluster added is working correctly.

To send a BOSCO test job to the host
%<RED%username@mycluster-submit.mydomain%ENDCOLOR>% (name as listed in
the output of `bosco_cluster --list`):

1.  Setup the environment appropriate for your shell as described in the
    setup environment section (above).
2.  For the cluster %<RED%username@mycluster-submit.mydomain%ENDCOLOR>%
    (identical to output of `bosco_cluster --list`). Replace the parts
    in red: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> bosco\_cluster --test
%<RED%username@mycluster-submit.mydomain%ENDCOLOR>% \</pre\>
<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> \<pre
class="screen"\> <span class="twiki-macro UCL_PROMPT"></span> $
bosco\_cluster -t <dweitzel@ff-grid.unl.edu> <dweitzel@ff-grid.unl.edu>
Testing ssh to <dweitzel@ff-grid.unl.edu>...Passed\! Testing bosco
submission...Passed\! Checking for submission to remote pbs cluster
(could take \~30 seconds)...Passed\! Submission files for these jobs are
in /home/dweitzel/bosco/local.localhocentos56/bosco-test Execution on
the remote cluster could take a while...Exiting \</pre\>
<span class="twiki-macro ENDTWISTY"></span>

\---\#\# How to Stop and Remove To stop BOSCO:\<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> bosco\_stop \</pre\>

To uninstall BOSCO:

  - If you want to remove remote clusters get the list and remove them
    one by one: \<pre
    class="screen"\><span class="twiki-macro UCL_PROMPT"></span>
    bosco\_cluster --list

\# For each remote cluster <span class="twiki-macro UCL_PROMPT"></span>
bosco\_cluster -r
%RED%user@cluster\_as\_spelled\_in\_list%ENDCOLOR%\</pre\>

  - Remove the installation directory: \<pre
    class="screen"\><span class="twiki-macro UCL_PROMPT"></span>
    bosco\_uninstall\</pre\>

<span class="twiki-macro NOTE"></span> Uninstalling BOSCO removes the
software but leaves the files in your `.bosco` and `.ssh` directories
with all the information about the added clusters and the SSH keys.
Files installed on the remote clusters are not removed either.

\---\#\# How to Update BOSCO If you want to update BOSCO to a new
version you have to:

1.  setup BOSCO:\<pre
    class="screen"\><span class="twiki-macro UCL_PROMPT"></span> source
    \~/bosco/bosco\_setenv\</pre\>
2.  stop BOSCO: \<pre
    class="screen"\><span class="twiki-macro UCL_PROMPT"></span>
    bosco\_stop\</pre\>
3.  remove the old BOSCO: \<pre
    class="screen"\><span class="twiki-macro UCL_PROMPT"></span>
    bosco\_uninstall\</pre\>
4.  download and install the new BOSCO (see install section above) and
    re-add all the clusters in your setup:
5.  for each installed cluster (the list is returned by `bosco_cluster
    --list`):
    1.  remove the cluster: \<pre
        class="screen"\><span class="twiki-macro UCL_PROMPT"></span>
        bosco\_cluster --remove
        %<RED%username@mycluster-submit.mydomain.org%ENDCOLOR>% \</pre\>
    2.  add the cluster: \<pre
        class="screen"\><span class="twiki-macro UCL_PROMPT"></span>
        bosco\_cluster --add
        %<RED%username@mycluster-submit.mydomain.org>
        queue<span class="twiki-macro ENDCOLOR"></span> \</pre\>
6.  start BOSCO: \<pre
    class="screen"\><span class="twiki-macro UCL_PROMPT"></span>
    bosco\_start\</pre\>

This will update the local installation and the software on the remote
clusters

\---\# Job submission example

You can submit a regular Condor vanilla job to BOSCO. The Campus
Factory, a component within BOSCO, will take care to run it on the
different clusters that you added and to transfer the input and output
files as needed. Here follow a simple example. The Condor team provides
[many great tutorials](http://research.cs.wisc.edu/condor/tutorials/) to
learn more.

\---\#\# Configuring Executable Your may wrap your job in a script (e.g.
using your favorite shell or Python) so that you can invoke more
executables and do some tests.

This is a simple bash script, called `myjob.sh`: \<pre class="file"\>
\#/bin/bash

\# Prepare for the execution

\# Run the actual applications /bin/hostname /bin/date /usr/bin/id
/usr/bin/whoami /bin/env | /bin/sort \> myout-file-$1

\# Final steps \</pre\>

\---\#\# Example Submission File Here is an example submission file.
Copy it to a file, `example.condor`

``` file
universe = vanilla
output = cfjob.out.$(Cluster)-$(Process)
error = cfjob.err.$(Cluster)-$(Process)
Executable     = myjob.sh
arguments = 10
log = cfjob.log.$(Cluster)
should_transfer_files = YES
when_to_transfer_output = ON_EXIT
queue 1
```

\---\#\# Job Submission Submit the job file `example.condor` with the
`condor_submit` command: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> condor\_submit
example.condor \</pre\>

\---\#\# Job Monitoring Monitor the job with `condor_q`. For example,
the job when idle is: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> condor\_q

\-- Submitter: uc3-c001.mwt2.org : \<10.1.3.101:45876\> :
uc3-c001.mwt2.org ID OWNER SUBMITTED RUN\_TIME ST PRI SIZE CMD 12.0
marco 7/6 13:45 0+00:00:00 I 0 0.0 short2.sh 10 13.0 marco 7/6 13:45
0+00:00:00 I 0 0.0 short2.sh 10 14.0 marco 7/6 13:46 0+00:00:00 I 0 0.0
glidein\_wrapper.sh 15.0 marco 7/6 13:46 0+00:00:00 I 0 0.0
glidein\_wrapper.sh 16.0 marco 7/6 13:46 0+00:00:00 I 0 0.0
glidein\_wrapper.sh 17.0 marco 7/6 13:46 0+00:00:00 I 0 0.0
glidein\_wrapper.sh 18.0 marco 7/6 13:46 0+00:00:00 I 0 0.0
glidein\_wrapper.sh 19.0 marco 7/6 13:46 0+00:00:00 I 0 0.0
glidein\_wrapper.sh

8 jobs; 0 completed, 0 removed, 8 idle, 0 running, 0 held, 0 suspended
\</pre\>

**NOTE** That condor\_q will show also the glidein jobs. Auxiliary jobs
that BOSCO is using to run your actual job. Like in the example above,
job 11 was the one submitted.

The job could be idle if it is currently idle at the remote cluster.
When the job is being executed on the remote cluster, the `ST` (State)
will change to `R`, and the `RUN_TIME` will grow.

Another method of monitoring a job is to check the job's `log`, a human
readable (for the most part) time line of actions relating to the job.
The `logfile` was specified in the submission script, for example
`logfile` in the example above. You can view the log file by using
`cat`: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> cat logfile \</pre\>

\---\#\# Job output Once the job completes BOSCO will transfer back
standard output, standard error and the output files (if specified in
the submit file), e.g. the job above will create stdout and stderr files
(unique for each submission) and a file `myout-file-10` in the directory
where the `condor_submit` command was executed.

\---\# Command summary | **Action** | **Arguments** | **Implicit Input**
| **Output** | | bosco\_install | | | Success/Failure | | source
bosco.\[csh,sh\] | | | | | bosco\_start | | | Success/Failure | |
bosco\_stop | | | Success/Failure | | bosco\_uninstall | | |
Success/Failure | | bosco\_cluster | --add <user@Hostname> queue | |
Success/Fail, entry in head node table | | | --list | Head-node table |
List of added head nodes and their status | | | --test Hostname | Submit
file | Status of submitted jobs | | | --remove Hostname | |
Success/Fail, head node table with Hostname removed, delete if empty | |
condor-\* | Various | Various | Various see the [Condor
manual](http://research.cs.wisc.edu/condor/manual/) |

\---\# Advanced use

\---\#\# Multi homed hosts Multi homed hosts are hosts with multiple
Network Interfaces (aka dual-homed when they have 2 NICs). BOSCO
configuration is tricky on multi-homed hosts. BOSCO requires the submit
host to be able to connect back to the BOSCO host, so it must advertise
an interface that is reachable from all the chosen submit hosts. E.g. a
host with a NIC on a private network and one with a public IP address
must advertise the public address if the submit hosts are outside of the
private network. In order to do that you have to:

  - make sure that the name returned by the command `/bin/hostname -f`
    is the name resolving in the public address (e.g. `` host `hostname
    -f` `` should return the public address). If not you should change
    it.
  - edit `~/bosco/local.%RED%$HOST%ENDCOLOR%/condor_config.local` (HOST
    is the short host name) and add a line like =NETWORK\_INTERFACE =
    xxx.xxx.xxx.xxx= , substituting xxx.xxx.xxx.xxx with the public IP
    address. This will tell BOSCO to use that address.

\---\#\# Multi-User BOSCO For multi-user BOSCO, special care must be
given to enable multiple users to use the same bosco condor instance.

First, you must install the BOSCO instance just as before in the above
instructions. Second, you install a system wide condor instance. Only
the BOSCO instance will run, but it will run as root.

We will assume that BOSCO was installed as user `bosco` for the sake of
this guide.

1.  As the bosco user, stop bosco: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> bosco\_stop \</pre\>

1.  As the bosco user, edit
    `$HOME/bosco/local.%RED%HOST%ENDCOLOR%/config/condor_config.factory`.
    Modify the ENVIRONMENT to include bosco's home directory, and add
    the USERID line. \<pre class="file"\>

CAMPUSFACTORY\_ENVIRONMENT =
"PYTHONPATH=$(LIBEXEC)/campus\_factory/python-lib
CAMPUSFACTORY\_DIR=$(LIBEXEC)/campus\_factory
\_campusfactory\_GLIDEIN\_DIRECTORY=$CAMPUSFACTORY\_DIR/share/glidein\_jobs
%RED%HOME=/home/bosco%ENDCOLOR%" %RED%CAMPUSFACTORY\_USERID =
bosco<span class="twiki-macro ENDCOLOR"></span> \</pre\>

1.  As root, change ownership of the password file and start the bosco
    instance of condor: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT_ROOT"></span> source
\~bosco/bosco/bosco\_setenv
<span class="twiki-macro UCL_PROMPT_ROOT"></span> chown root:
\`condor\_config\_val SEC\_PASSWORD\_FILE\`
<span class="twiki-macro UCL_PROMPT_ROOT"></span> bosco\_start\</pre\>

1.  As root, install Condor into a global directory. For example, you
    may install the Condor RPM. Do not start this instance of Condor.
2.  Now, as any user, you may run the test job given in [Validation of
    Success](#Validation_of_Success)

\---\# Troubleshooting

\---\#\# Useful Configuration and Log Files BOSCO underneath is using
Condor. You can find all the Condor log files in
`~/bosco/local.HOSTNAME/log`

\---\#\# Known Issues

The current version is not supporting Condor clusters. You can add them
but jobs will fail.

\---\#\# Cannot find the BOSCO download file Sometime the filename is
changed without updating the link in this document (e.g. changing the
RHAP version from 5.7 to 5.8). If the link is broken please open the
repository <ftp://ftp.cs.wisc.edu/condor/bosco/latest/> in a Web browser
and find the exact link. Please also report to us the mismatch so that
we can fix this document. Thank you.

\---\#\# Make sure that you can connect to the BOSCO host If you see
errors like:\<pre class="screen"\> Installing BOSCO on
<user@osg-ss-submit.chtc.wisc.edu>... ssh: connect to host
osg-ss-submit.chtc.wisc.edu port 22: Connection timed out rsync:
connection unexpectedly closed (0 bytes received so far) \[sender\]
rsync error: unexplained error (code 255) at io.c(600) \[sender=3.0.6\]
ssh: connect to host osg-ss-submit.chtc.wisc.edu port 22: Connection
timed out rsync: connection unexpectedly closed (0 bytes received so
far) \[sender\] rsync error: unexplained error (code 255) at io.c(600)
\[sender=3.0.6\] ssh: connect to host osg-ss-submit.chtc.wisc.edu port
22: Connection timed out rsync: connection unexpectedly closed (0 bytes
received so far) \[sender\] rsync error: unexplained error (code 255) at
io.c(600) \[sender=3.0.6\] \</pre\> Please try manually to ssh from the
BOSCO host to the cluster submit node. The ability to connect is
required in order to install BOSCO.

\---\#\# Make sure that BOSCO is running BOSCO may not survive after you
log out. When you log back in after sourcing the setup (`source
~/bosco/bosco_setenv`), you should start BOSCO (`bosco_start`) specially
if the command `condor_q` is failing.

\---\#\# Errors due to leftover files Bosco files on the submit host are
in:

  - `~/bosco/` - the release directory
  - `~/.bosco/` - some service files
  - `~/.ssh/` - the ssh key used by BOSCO

If you used `bosco_uninstall` it will remove all BOSCO related files. If
you removed BOSCO by hand you must pay attention. If the service key is
still in `.ssh` but the other files are missing, during the execution of
BOSCO commands you will get some unclear errors like *"IOError: \[Errno
2\] No such file or directory: '/home/marco/.bosco/.pass'"* , *"OSError:
\[Errno 5\] Input/output error"* , all followed by:

    Password-less ssh to marco@itb2.uchicago.edu did NOT work, even after adding the ssh-keys.
    Does the remote resource allow password-less ssh?

If that happens you can remove the service files and the keys
using:\<pre\>rm -r \~/.bosco rm \~/.ssh/bosco\_key.rsa\*\</pre\> and
then re-add all the clusters with `bosco_cluster --add`.

\---\# Get Help/Support To get assistance you can send an email to
<bosco-discuss@opensciencegrid.org>

\---\# References Campus Grids related documents:

  - <https://twiki.grid.iu.edu/bin/view/CampusGrids>
  - <https://twiki.grid.iu.edu/bin/view/Documentation/CampusFactoryInstall>

Condor documents:

  - Condor manual: <http://research.cs.wisc.edu/condor/manual/>

How to submit Condor jobs:

  - Quick start: <http://research.cs.wisc.edu/condor/quick-start.html>
  - Tutorial:
    <http://research.cs.wisc.edu/condor/tutorials/alliance98/submit/submit.html>
  - Condor manual:
    <http://research.cs.wisc.edu/condor/manual/v7.6/2_5Submitting_Job.html>

Here you can check out older releases:

  - [BOSCO version 0](BoSCOv0)
  - [BOSCO version 1](BoSCOv1) (Release documented here)
  - [BOSCO version 1.1](BoSCOv1p1) (Following release)

# Comments

<span class="twiki-macro COMMENT" type="tableappend"></span>
