%META:TOPICINFO{author="KyleGross" date="1465334043" format="1.1"
version="1.15"}% %META:TOPICPARENT{name="BoSCO"}%

# BOSCO

<span class="twiki-macro TOC" data-depth="3"></span>

\---\# Introduction

BOSCO is a job submission manager designed to help researchers manage
large numbers (\~1000s) of job submissions to the different resources
that they can access on a campus (initially a PBS cluster running
Linux). This is release 1.1 of BOSCO, if you find any problems or need
help installing or running BoSCO, please email
<bosco-discuss@opensciencegrid.org> . BOSCO 1.1 is now available as
release.

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

\---\# BOSCO versions and BOSCO documents \<img
src="%ATTACHURLPATH%/bosco-submit\_and\_resource.jpg"
alt="bosco-submit\_and\_resource.jpg" width='378' height='399' /\>

The BOSCO submit node is the host where BOSCO is installed and where
user/s login to submit jobs via BOSCO. The multiple clusters added to
BOSCO (i.e. where the user/s can submit jobs via BOSCO) are referred as
BOSCO resources.

This page explains how to use BOSCO. Before using BOSCO you, or someone
for you, will have to install a version of BOSCO and add at least one
BOSCO resource. Adding, testing and removing BOSCO resources is part of
the BOSCO configuration.

[BOSCO Single User](BoscoInstall) allows a researcher to install BOSCO
in her/his (non-privileged) account, to configure it and to use it To
install and configure BOSCO Single-user read [BOSCO Single User
Installation](BoscoInstall).

[BOSCO Multi User](BoscoMultiUser) is installed, configured and started
on a host by the system administrator (root) and is available to all the
users on the host. To install and configure BOSCO Multi-user read [BOSCO
Multi User Installation](BoscoMultiUser).

Later in this document we'll assume that BOSCO has been already
installed and configured correctly. For the installation or to change
the configuration (e.g. to add or remove BOSCO resources) please check
the other documents: [BOSCO Single User Installation](BoscoInstall) and
[BOSCO Multi User Installation](BoscoMultiUser).

\---\# Requirements

There are specific requirements for the BOSCO resources that are
specified in the install documents.

To use BOSCO you need a BOSCO submit host with BOSCO installed and
configured correctly. All requirements for the BOSCO submit host and the
BOSCO resources, as well as the requirements to include BOSCO in a more
complex Condor setup, are described in the install documents.

\---\# How to Install

Either you or a system administrator for you will have to install and
setup BOSCO. The installation consists in downloading and installing the
BOSCO software. The setup consists in managing which clusters are
included in the BOSCO pool and will execute your jobs; it includes all
the operations performed using the `bosco_cluster` command. The
installation and setup are covered in two separate documents:

  - to install and setup BOSCO so that it is used and configured from a
    single user account please refer to BoscoInstall
  - to install and setup BOSCO so that it is configured from a single
    user account but it can be used by all the accounts on the host
    please refer to BoscoMultiUser

\---\# How to Use

In order to use BOSCO and submit a job:

1.  BOSCO must be installed
2.  BOSCO must be running (it must have been started)
3.  At least one cluster must have been added to BOSCO
4.  You must setup the environment

\---\#\# Starting/Stopping and Configuring BOSCO Each time I mention
"you" in this section I refer either to you or to a system administrator
that acts on your behalf, probably the same person that installed BOSO.

BOSCO has some persistent services that must be running. You'll have to
start it at the beginning and probably after each reboot of your host.
You should stop BOSCO before an upgrade and possibly before a shutdown
of your host. If you will not use BOSCO anymore, uninstall will remove
it from your system.

You need to add to BOSCO all the clusters of which you like to use the
resources. In order to run jobs you need at least one.

Please refer to the BoscoInstall or BoscoMultiUser documents for
operations including:

  - starting BOSCO
  - stopping BOSCO
  - updating BOSCO
  - uninstalling BOSCO
  - adding one or more clusters to BOSCO

\---\#\# Setup environment before using Since BOSCO is not installed in
the system path. An environment file must be sourced all the times you
use BOSCO (start/stop/job submission or query, anything):

``` screen
%UCL_PROMPT% source ~/bosco/bosco_setenv
```

<span class="twiki-macro STARTSECTION">BoscoJob</span> ---\# Job
submission example

You can submit a regular Condor vanilla job to BOSCO. The Campus
Factory, a component within BOSCO, will take care to run it on the
different clusters that you added and to transfer the input and output
files as needed. Here is a simple example. The Condor team provides
[many great tutorials](http://research.cs.wisc.edu/condor/tutorials/) to
learn more.

\---\#\# Configuring Executable Your may wrap your job in a script (e.g.
using your favorite shell or Python) so that you can invoke more
executables and do some tests.

This is a simple bash script, called `myjob.sh`: \<pre class="file"\>
\#/bin/bash

\# Prepare for the execution

\# Run the actual applications hostname date id whoami

\# Final steps

\</pre\>

\---\#\# Example Submission File With BOSCO you can do direct submission
to the cluster, using the grid universe, or use the the glideins so that
regular (vanilla) HTCondor jobs can be used. There is a small difference
between the 2 options is in the submit file (see below) and vanilla have
some additional [Firewall and Network
requirements](BoscoInstall#FirewallReq) because they use glideins. All
the other steps, job file creation, job submission and checking the
jobs, are the same.

**Use one or the other**

\---\#\#\# Direct Job submission example

Here is an example submission file for direct submission. Copy it to a
file, `example.condor`

``` file
universe = grid
grid_resource = batch %RED%pbs%ENDCOLOR% marco@uc3-pbs.uchicago.edu
Executable     = myjob.sh
arguments = )
output = myjob.out
error = myjob.error
log = myjob.log
transfer_output_files = 
should_transfer_files = YES
when_to_transfer_output = ON_EXIT
queue 1
```

The type of cluster that you are submitting to, pbs, lsf, sge, or
condor, must be supplied on the grid\_resource line.

\---\#\#\# Glidein Job submission example

You can submit a regular HTCondor vanilla job to BOSCO. The Campus
Factory, a component within BOSCO, will take care to run it on the
different clusters that you added and to transfer the input and output
files as needed. Here follow a simple example. The Condor team provides
[many great tutorials](http://research.cs.wisc.edu/condor/tutorials/) to
learn more.

Here is an example of a vanilla submission file (using glideins). Copy
it to a file, `example.condor`

``` file
universe = vanilla
Executable     = myjob.sh
arguments = $(Cluster) $(Process)
output = cfjob.out.$(Cluster)-$(Process)
error = cfjob.err.$(Cluster)-$(Process)
log = cfjob.log.$(Cluster)
should_transfer_files = YES
when_to_transfer_output = ON_EXIT
queue 1
```

<span class="twiki-macro NOTE"></span> The BOSCO submit host needs to
satisfy these additional [Firewall and Network
requirements](BoscoInstall#FirewallReq) to be able to submit and run
vanilla jobs. Those requirement include being reachable by all BOSCO
resources.

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
<span class="twiki-macro ENDSECTION">BoscoJob</span>

\---\# Troubleshooting

\---\#\# Useful Configuration and Log Files BOSCO underneath is using
Condor. You can find all the Condor log files in
`~/bosco/local.HOSTNAME/log`

\---\#\# Known Issues

\---\#\# Make sure that BOSCO is running BOSCO may not survive after you
log out. When you log back in after sourcing the setup (`source
~/bosco/bosco_setenv`), if you are using BOSCO single-user you should
start BOSCO (`bosco_start`) specially if the command `condor_q` is
failing. More details about starting BOSCO are in BoscoInstall and
BoscoMultiUser

\---\# Get Help/Support To get assistance you can send an email to
<bosco-discuss@opensciencegrid.org>

<span class="twiki-macro INCLUDE" data-section="BoscoReferences">BoscoInstall</span>

# Comments

<span class="twiki-macro COMMENT" type="tableappend"></span>

%META:FILEATTACHMENT{name="bosco-submit\_and\_resource.jpg"
attachment="bosco-submit\_and\_resource.jpg" attr="" comment=""
date="1352242706" path="bosco-submit\_and\_resource.jpg" size="36728"
stream="bosco-submit\_and\_resource.jpg"
tmpFilename="/usr/tmp/CGItemp12909" user="MarcoMambelli" version="1"}%
