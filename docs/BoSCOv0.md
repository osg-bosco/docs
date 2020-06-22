%META:TOPICINFO{author="KyleGross" date="1481047997" format="1.1"
version="1.6"}% %META:TOPICPARENT{name="BoSCO"}% \<div
style="background-color:\#ffa07a; padding:7px; -moz-border-radius: 5px;
border-radius: 5px;"\> This is an older release. Please go to
[BOSCO](Trash/CampusGrids.BoSCO) for the latest release. \</div\>

# BOSCO v0

<span class="twiki-macro DOC_STATUS_TABLE"></span>
<span class="twiki-macro TOC" data-depth="3"></span>

## Introduction

BOSCO is a job submission manager designed to help researchers manage
large numbers (\~1000s) of job submissions to the different resources
that they can access on a campus (initially a PBS cluster running
Linux). This is the first release of BOSCO, v0.1, if you find any
problems or need help installing or running BoSCO, please email
<bosco-discuss@opensciencegrid.org> .

It offers the following capabilities:

  - Job are automatically resubmitted when they fail. The researcher
    does not need to baby sit their jobs
  - Job submissions can be throttled to meet batch scheduler settings
    (e.g. only 10 jobs running concurrently). The researcher does not
    need to make multiple submissions. BOSCO handles that for them.
  - BOSCO is designed to be flexible and will in the next version (v1)
    allow jobs to be submitted to multiple clusters, perhaps with
    different job schedulers (e.g. PBS, LSF, Condor).

The primary advantage for the researcher is that they only need to learn
one job scheduler environment even if the clusters utilize different
native environments.

<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED%
showlink="Click to see the format conventions used in this document"
</span>
<span class="twiki-macro INCLUDE" data-section="Header">Trash/DocumentationTeam.DocConventions</span>
<span class="twiki-macro INCLUDE" data-section="CommandLine">Trash/DocumentationTeam.DocConventions</span>
<span class="twiki-macro ENDTWISTY"></span>

## How to Install

### Requirements

  - **Submit-node**  
    This is the system that the researcher uses to submit jobs. In
    general it can be the user's laptop, workstation, or it can be
    another system that the user logs into for submitting jobs to the
    cluster. A current limitation is that the submit-node uses the same
    Linux flavor that the PBS cluster is using.
  - **Cluster head-node**  
    This is the node that you normally login to on the PBS or Condor
    cluster.
  - **PBS flavors supportted**  
    Torque and PBSPro
  - **Condor flavors supported**  
    Condor 7.6 or following

### Installation Procedure

1.  Download BOSCO
      - BOSCO comes in 3 different flavors depending on your linux
        version. To find your Linux distribution and version type `cat
        /etc/*-release`. Right click on the appropriate link below
        (depending on your linux version), select "save as" and save the
        file in your home directory
          - [RHEL 5 or Scientific Linux 5 or
            Centos 5](ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-0.1-x86_64_rhap_5.7.tar.gz)
          - [RHEL 6 or Scientific Linux 6 or
            Centos 6](ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-0.1-x86_64_rhap_6.2.tar.gz)
          - [Debian 6](ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-0.1-x86_64_deb_6.0.tar.gz)
      - Alternatively you can use `wget` and the correct link:\<pre
        class="screen"\>

\# If you have RHEL 5 or Scientific Linux 5 or Centos 5
<span class="twiki-macro UCL_PROMPT"></span> wget
<ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-0.1-x86_64_rhap_5.7.tar.gz>
\# If you have RHEL 6 or Scientific Linux 6 or Centos 6
<span class="twiki-macro UCL_PROMPT"></span> wget
<ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-0.1-x86_64_rhap_6.2.tar.gz>
\# If you have Debian 6 <span class="twiki-macro UCL_PROMPT"></span>
wget
<ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-0.1-x86_64_deb_6.0.tar.gz>
\</pre\>

1.  Unpack the tar file and Install BOSCO: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> cd \~
<span class="twiki-macro UCL_PROMPT"></span> mkdir tmp-install
<span class="twiki-macro UCL_PROMPT"></span> cd tmp-install
<span class="twiki-macro UCL_PROMPT"></span> tar xvzf
\~/bosco-0.1-x86\_64**.tar.gz
<span class="twiki-macro UCL_PROMPT"></span> cd condor-7.**
<span class="twiki-macro UCL_PROMPT"></span> ./bosco\_install \</pre\>

<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> \<pre
class="screen"\> -bash-3.2$ wget
<ftp://ftp.cs.wisc.edu/condor/temporary/bosco/2012-02-20/condor-7.7.6-UW_development-rhel5.8-stripped.tar.gz>
--2012-02-22 18:35:09--
<ftp://ftp.cs.wisc.edu/condor/temporary/bosco/2012-02-20/condor-7.7.6-UW_development-rhel5.8-stripped.tar.gz>
=\> \`condor-7.7.6-UW\_development-rhel5.8-stripped.tar.gz' Resolving
ftp.cs.wisc.edu... 128.105.2.28 Connecting to
ftp.cs.wisc.edu|128.105.2.28|:21... connected. Logging in as anonymous
... Logged in\! ==\> SYST ... done. ==\> PWD ... done. ==\> TYPE I ...
done. ==\> CWD /condor/temporary/bosco/2012-02-20 ... done. ==\> SIZE
condor-7.7.6-UW\_development-rhel5.8-stripped.tar.gz ... 49527344 ==\>
PASV ... done. ==\> RETR
condor-7.7.6-UW\_development-rhel5.8-stripped.tar.gz ... done. Length:
49527344 (47M)

100%\[===========================================================================\>\]
49,527,344 30.2M/s in 1.6s

2012-02-22 18:35:13 (30.2 MB/s) -
\`condor-7.7.6-UW\_development-rhel5.8-stripped.tar.gz' saved
\[49527344\]

\-bash-3.2$ mkdir tmp-bosco/ -bash-3.2$ cd tmp-bosco/ -bash-3.2$ tar xzf
\~/condor-7.7.6-UW\_development-rhel5.8-stripped.tar.gz -bash-3.2$ cd
condor-7.7.6-UW\_development-rhel5.8-stripped/ -bash-3.2$ mkdir man
-bash-3.2$ ./bosco\_install Installing Condor from
/share/home/marco/tmp-bosco/condor-7.7.6-UW\_development-rhel5.8-stripped
to /share/home/marco/bosco

Condor has been installed into: /share/home/marco/bosco

Configured condor using these configuration files: global:
/share/home/marco/bosco/etc/condor\_config local:
/share/home/marco/bosco/local.gc2-wn2/condor\_config.local

In order for Condor to work properly you must set your CONDOR\_CONFIG
environment variable to point to your Condor configuration file:
/share/home/marco/bosco/etc/condor\_config before running Condor
commands/daemons. Created scripts which can be sourced by users to setup
their Condor environment variables. These are: sh:
/share/home/marco/bosco/bosco.sh csh: /share/home/marco/bosco/bosco.csh
\</pre\> <span class="twiki-macro ENDTWISTY"></span>

## How to Use

Now BOSCO is installed. To use it:

1.  Setup the environment
2.  Add all the desired clusters (at least one)
3.  Start BOSCO
4.  Submit a test job
5.  Submit a real job

### Setup environment before using

Since BOSCO is not installed in the system path. An environment file
must be sourced all the times you use BOSCO (start/stop/job submission
or query, anything):

``` screen
%UCL_PROMPT% source bosco/bosco_setenv
```

### Add a cluster to BOSCO

To add a new cluster to the resources you will be using through BOSCO:

1.  Setup the environment appropriate for your shell as described in the
    setup environment section (above).
2.  For the cluster
    %RED%mycluster<span class="twiki-macro ENDCOLOR"></span> with user
    and submit host %<RED%username@mycluster-submit.mydomain%ENDCOLOR>%
    (FQDN) and queue manager PBS or Condor. Replace the parts in red:
    \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> bosco\_cluster --add
%<RED%username@mycluster-submit.mydomain%ENDCOLOR>% \</pre\>
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

You can see a list of the current clusters in BOSCO by typing:\<pre
class="screen"\> <span class="twiki-macro UCL_PROMPT"></span>
bosco\_cluster --list \</pre\>
<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> \<pre
class="screen"\> -bash-3.2$ bosco\_cluster -list
itbv-ce-pbs.uchicago.edu \</pre\>
<span class="twiki-macro ENDTWISTY"></span>

### Starting BOSCO BOSCO has some persistent services that must be running. You'll have to start it at the beginning and probably after each reboot of your host.

You should stop BOSCO before an upgrade and possibly before a shutdown
of your host. If you will not use BOSCO anymore, uninstall will remove
it from your system.

To start BOSCO:\<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> bosco\_start \</pre\>

### Submitting a test job

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

### Configuring Executable

The executable needs to be configured to take input from directories and
write output to directories on the remote cluster. No output can be
transferred back to the submit host automatically.

In the examples below, the input files and executables are assumed to be
in %GREEN%input\_directory<span class="twiki-macro ENDCOLOR"></span>.
The executable will write all output files and any other output that is
needed to
%GREEN%output\_directory<span class="twiki-macro ENDCOLOR"></span>. The
input\_directory and output\_directory could be the same directory, but
for clarity, they are shown separately below.

A common use case is to create a wrapper script around the actual
executable. For example, a wrapper could be: \<pre class="file"\>
\#/bin/bash

\# Change to input directory cd $HOME/input\_directory

\# Run the actual application ./real\_exe

\# Copy the output to the output directory mkdir -p
$HOME/output\_directory cp output\_file.1 $HOME/output\_directory/ cp
output\_file.2 $HOME/output\_directory/

\</pre\>

### Transfer input

In order to do useful work on the remote cluster, you will need to
transfer all input files and executables. For example, you could use the
command: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> scp -r
%GREEN%input\_directory<span class="twiki-macro ENDCOLOR"></span>
%<RED%username@mycluster-submit.mydomain%ENDCOLOR>% \</pre\>

The %GREEN%input\_directory<span class="twiki-macro ENDCOLOR"></span>
would include the executables and the input files. The executable will
be explicitly listed in the condor submit file (immediately below). You
will configure the executables to write to a known directory. This
directory will later be manually transferred from the remote cluster
back to the local machine.

### Example Submission File

Here is an example submission file. Copy it to a file, `example.condor`

``` file
universe = grid
grid_resource = pbs %RED%username@mycluster-submit.mydomain%ENDCOLOR%
executable = %GREEN%input_directory/start.sh%ENDCOLOR%
output = /dev/null
error = /dev/null
log = logfile
+remote_iwd="~/"
transfer_executable=false
queue
```

**NOTE**: output and error are specified as `/dev/null` because there is
no way to get the output (stdout) and error (stderr) from the executable
back to the submit host automatically. Getting output back is covered
after monitoring.

There are only two lines that need to be edited.

  - Grid resource needs to be changed to the %RED%cluster
    name<span class="twiki-macro ENDCOLOR"></span>.
  - %GREEN%Executable<span class="twiki-macro ENDCOLOR"></span> needs to
    be changed to an executable that is on the cluster. It will need to
    be prestaged to the cluster.

In the Condor submit file to submit to Condor you'll have to specify the
resource as: \<pre class="file"\> grid\_resource = batch condor
%<RED%username@mycluster-submit.mydomain%ENDCOLOR>% \</pre\>

### Job Submission

Submit the job file `example.condor` with the `condor_submit` command:
\<pre class="screen"\> <span class="twiki-macro UCL_PROMPT"></span>
condor\_submit example.condor \</pre\>

### Job Monitoring

Monitor the job with `condor_q`. For example, the job when idle is:
\<pre class="screen"\> <span class="twiki-macro UCL_PROMPT"></span>
condor\_q

\-- Submitter: : \<10.148.2.154:44918\> : ID OWNER SUBMITTED RUN\_TIME
ST PRI SIZE CMD 2.0 dweitzel 3/12 20:52 0+00:00:00 I 0 0.0 start.sh

1 jobs; 0 completed, 0 removed, 1 idle, 0 running, 0 held, 0 suspended
\</pre\>

The job could be idle if it is currently idle at the remote cluster.
When the job is being executed on the remote cluster, the `ST` (State)
will change to `R`, and the `RUN_TIME` will grow.

Another method of monitoring a job is to check the job's `log`, a human
readable (for the most part) time line of actions relating to the job.
The `logfile` was specified in the submission script, for example
`logfile` in the example above. You can view the log file by using
`cat`: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> cat logfile \</pre\>

### Transfer output back

Just like transferring the input directory to the cluster, you will need
to transfer the output back. An example command that could be use
is:\<pre class="screen"\> <span class="twiki-macro UCL_PROMPT"></span>
scp -r %<RED%username@mycluster-submit.mydomain%ENDCOLOR>%
%GREEN%output\_directory<span class="twiki-macro ENDCOLOR"></span>
\</pre\>

This will copy the contents of
%GREEN%output\_directory<span class="twiki-macro ENDCOLOR"></span> from
the remote cluster to the local machine.

## How to Stop

To stop BOSCO:\<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> bosco\_stop \</pre\>

To uninstall BOSCO:\<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> bosco\_uninstall \</pre\>

## Command summary

| **Action** | **Arguments** | **Implicit Input** | **Output** | |
bosco\_install | | | Success/Failure | | source bosco.\[csh,sh\] | | | |
| bosco\_start | | | Success/Failure | | bosco\_stop | | |
Success/Failure | | bosco\_uninstall | | | Success/Failure | |
bosco\_cluster | --add <user@Hostname> | | Success/Fail, entry in head
node table | | | --list | Head-node table | List of added head nodes and
their status | | | --test Hostname | Submit file | Status of submitted
jobs | | | --remove Hostname | | Success/Fail, head node table with
Hostname removed, delete if empty | | condor-\* | Various | Various |
Various see the [Condor
manual](http://research.cs.wisc.edu/condor/manual/) | Manually transfer
output data from batch system.

## Troubleshooting

### Useful Configuration and Log Files

BOSCO underneath is using Condor. You can find all the Condor log files
in `~/bosco/local.HOSTNAME/log`

### Known Issues

The current version is not supporting file transfer. Not even stdout and
stderr. If you specify them in the Condor submit file (to something
different from /dev/null) your job will fail.

## Get Help/Support

To get assistance you can send an email to
<bosco-discuss@opensciencegrid.org>

## References Campus Grids related documents:

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

## Comments

<span class="twiki-macro COMMENT" type="tableappend"></span>
