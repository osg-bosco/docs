# BOSCO

## Introduction

BOSCO is a job submission manager designed to help researchers manage
large numbers (~1000s) of job submissions to the different resources
that they can access on a campus (initially a PBS cluster running
Linux).

If you find any problems or need
help installing or running BoSCO, please email
[bosco-discuss@opensciencegrid.org](bosco-discuss@opensciencegrid.org).

Bosco offers the following capabilities:

-   Jobs are automatically resubmitted when they fail. The researcher
    does not need to babysit their jobs.
-   Job submissions can be throttled to meet batch scheduler
    settings (e.g. only 10 jobs running concurrently). The researcher
    does not need to make multiple submissions. BOSCO handles that
    for them.
-   BOSCO is designed to be flexible and allows jobs to be submitted to
    multiple clusters, with different job schedulers (e.g. PBS, LSF,
    HTCondor, SGE, and SLURM).

The primary advantage for the researcher is that they only need to learn
one job scheduler environment even if the clusters utilize different
native environments.




## BOSCO definitions, versions and documents 

<!-- Inser Image Here 
&lt;img
src="%ATTACHURLPATH%/bosco-submit\_and\_resource.jpg"
alt="bosco-submit\_and\_resource.jpg" width='378' height='399' /&gt;
-->

- The BOSCO submit node is the host where BOSCO is installed and where
user's login to submit jobs via BOSCO. 
- The multiple clusters added to
BOSCO (i.e. where the user's can submit jobs via BOSCO) are referred as
BOSCO resources. 
- BOSCO resources can be a traditional cluster or a pool
of resources connected via HTCondor, another BOSCO installation, Grid,
or some other technology. Anyway all BOSCO resources have a submit node
(where you would login to submit jobs if you had no BOSCO) and worker
nodes (where the jobs run).

This page explains how to use BOSCO. Before using BOSCO you, or someone
for you, will have to install a version of BOSCO and add at least one
BOSCO resource. Adding, testing and removing BOSCO resources is part of
the BOSCO configuration.

[BOSCO Single User](BoscoInstall) allows a researcher to install BOSCO
in her/his (non-privileged) account, to configure it and to use it. If
you plan (at least initially) to connect a single cluster (BOSCO Single
User Single Cluster) then use the [BOSCO Quick Start
Guide](BoscoQuickStart). Otherwise, to install and configure BOSCO
Single-user read [BOSCO Single User Installation](BoscoInstall).

[BOSCO Multi User](BoscoMultiUser) is installed, configured and started
on a host by the system administrator (root) and is available to all the
users on the host. To install and configure BOSCO Multi-user read [BOSCO
Multi User Installation](BoscoMultiUser).

Later in this document we'll assume that BOSCO has been already
installed and configured correctly. For the installation or to change
the configuration (e.g. to add or remove BOSCO resources) please check
the other documents: [BOSCO Single User Installation](BoscoInstall) and
[BOSCO Multi User Installation](BoscoMultiUser).

## Requirements

There are specific requirements for the BOSCO resources that are
specified in the install documents.

To use BOSCO you need a BOSCO submit host with BOSCO installed and
configured correctly. All requirements for the BOSCO submit host and the
BOSCO resources, as well as the requirements to include BOSCO in a more
complex Condor setup, are described in the install documents.

## How to Install

Either you or a system administrator for you will have to install and
setup BOSCO. The installation consists in downloading and installing the
BOSCO software. The setup consists in managing which clusters are
included in the BOSCO pool and will execute your jobs; it includes all
the operations performed using the `bosco_cluster` command. The
installation and setup are covered in two separate documents:

-   To install and setup BOSCO so that it is used and configured from a
    single user account please refer to [Bosco Installation](BoscoInstall)
-   To install and setup BOSCO so that it is configured from a single
    user account but it can be used by all the accounts on the host
    please refer to [Bosco Multiuser](BoscoMultiUser)

## How to Use

In order to use BOSCO and submit a job:

1.  BOSCO must be installed
2.  BOSCO must be running (it must have been started)
3.  At least one cluster must have been added to BOSCO
4.  You must setup the environment

### Starting/Stopping and Configuring BOSCO Each time I mention
"you" in this section refers either to you or to a system administrator
that acts on your behalf, probably the same person that installed BOSCO.

BOSCO has some persistent services that must be running. You'll have to
start it at the beginning and probably after each reboot of your host.
You should stop BOSCO before an upgrade and possibly before a shutdown
of your host. If you will not use BOSCO anymore, uninstall will remove
it from your system.

You need to add to BOSCO all the clusters of which you like to use the
resources. In order to run jobs you need at least one.

Please refer to the [Bosco Installation](BoscoInstall) or [Bosco Multiuser](BoscoMultiUser) documents for
operations including:

-   starting BOSCO
-   stopping BOSCO
-   updating BOSCO
-   uninstalling BOSCO
-   adding one or more clusters to BOSCO

### Setup
Setup the environment before using Since BOSCO is not installed in
the system path. An environment file must be sourced all the times you
use BOSCO (start/stop/job submission or query, anything):

``` {.screen}
source ~/bosco/bosco_setenv
```

<!-- <span class="twiki-macro INCLUDE" section="BoscoJob">BoscoInstall</span> -->

## Troubleshooting

### Useful Configuration and Log Files

BOSCO underneath is using
Condor. You can find all the Condor log files in
`~/bosco/local.HOSTNAME/log`

### Known Issues

- Make sure that BOSCO is running. BOSCO may not survive after you
log out. When you log back in after sourcing the setup
(`source ~/bosco/bosco_setenv`), if you are using BOSCO single-user you
should start BOSCO (`bosco_start`) specially if the command `condor_q`
is failing. More details about starting BOSCO are in BoscoInstall and
BoscoMultiUser

## Get Help/Support
To get assistance you can send an email to <bosco-discuss@opensciencegrid.org>

<!--
<span class="twiki-macro INCLUDE"
section="BoscoReferences">BoscoInstall</span>
-->

