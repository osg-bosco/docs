%META:TOPICINFO{author="KyleGross" date="1465334043" format="1.1" version="1.26"}% %META:TOPICPARENT{name="BoSCO"}%

BOSCO Multi User
================

<span class="twiki-macro DOC_STATUS_TABLE"></span> <span class="twiki-macro TOC" depth="3"></span>

---\# Introduction

BOSCO is a job submission manager designed to help researchers manage large numbers (~1000s) of job submissions to the different resources that they can access on a campus (initially a PBS cluster running Linux). This is release 1.2 of BOSCO, if you find any problems or need help installing or running BoSCO, please email <bosco-discuss@opensciencegrid.org> .

It offers the following capabilities:

-   Jobs are automatically resubmitted when they fail. The researcher does not need to babysit their jobs.
-   Job submissions can be throttled to meet batch scheduler settings (e.g. only 10 jobs running concurrently). The researcher does not need to make multiple submissions. BOSCO handles that for them.
-   BOSCO is designed to be flexible and allows jobs to be submitted to multiple clusters, with different job schedulers (e.g. PBS, SGE, LSF, HTCondor, SLURM\*).

The primary advantage for the researcher is that they only need to learn one job scheduler environment even if the clusters utilize different native environments.

BOSCO multi user
----------------

This document is a bout BOSCO multi user, a version installed and managed on the submit host by a single user (administrator) and made available to all the users of that host.

Here are some key differences:

| BOSCO (single user)                                                                                                    | BOSCO multi user                                                                                                                      |
|:-----------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| Installed by user                                                                                                      | Installed as administrator (root)                                                                                                     |
| User manages BOSCO&lt;br&gt; - BOSCO started as User&lt;br&gt; - Contributing clusters (BOSCO resources) added by User | Administrator manages BOSCO &lt;br&gt; - BOSCO started as root&lt;br&gt; - Contributing clusters added using a single service account |
| User must have SSH access on all BOSCO resources                                                                       | SSH access via group service account (negotiated by admin)                                                                            |
| Only User can submit jobs to the HTCondor pool of BOSCO                                                               | All the users on the system can submit jobs to the HTCondor pool of BOSCO                                                             |
| No choices because it must be easy to install and run for scientists without system administration experience          | More flexible because there may be more customization to add BOSCO in the Campus Grid                                                 |

<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% showlink="Click to see the format conventions used in this document" </span> <span class="twiki-macro INCLUDE" section="Header">Trash/DocumentationTeam.DocConventions</span> <span class="twiki-macro INCLUDE" section="CommandLine">Trash/DocumentationTeam.DocConventions</span> <span class="twiki-macro ENDTWISTY"></span>

<span class="twiki-macro STARTSECTION">BoscoRequirements</span> ---\# Requirements

<span class="twiki-macro INCLUDE" section="BoscoGeneralRequirements">BoscoInstall</span>

---\#\# Networking \#FirewallReq <span class="twiki-macro INCLUDE" section="Firewalls">BoscoInstall</span>

<span class="twiki-macro ENDSECTION">BoscoRequirements</span>

<span class="twiki-macro STARTSECTION">BoscoInstall</span> ---\# How to Install

---\#\# Creating the Bosco User As `root`, create a `bosco` user and a install directory (e.g. `/opt/bosco`) that all users can see and use (all users should be able to read, `bosco` should be able to write). &lt;pre class="rootscreen"&gt; <span class="twiki-macro UCL_PROMPT_ROOT"></span> useradd bosco <span class="twiki-macro UCL_PROMPT_ROOT"></span> mkdir -p /opt/bosco <span class="twiki-macro UCL_PROMPT_ROOT"></span> chown bosco: /opt/bosco &lt;/pre&gt;

---\#\# Download and Install BOSCO Perform the following steps as the newly created `bosco` user.

1. Download the BOSCO installer using one of these alternatives (browser, wget, curl) then continue with step two: \* BOSCO comes in multiple flavors depending on your platform and the installer will download and install the correct flavor for you. Right click on [this link](ftp://ftp.cs.wisc.edu/condor/bosco/latest/boscoinstaller), select "save as" and save the file in the bosco user home directory. If your browser is saving the file in a different folder (e.g the Downloads folder), then move the file to the `bosco` user home directory before continuing with step 2. (any place is OK as long as you find the installer).

-   Alternatively you can use `wget` and the download link:&lt;pre class="screen"&gt;

<span class="twiki-macro UCL_PROMPT"></span> cd ~ <span class="twiki-macro UCL_PROMPT"></span> wget <ftp://ftp.cs.wisc.edu/condor/bosco/latest/boscoinstaller> &lt;/pre&gt;

-   If you have no `wget`, you can use `curl` to download:&lt;pre class="screen"&gt;

<span class="twiki-macro UCL_PROMPT"></span> curl -o ~/boscoinstaller <ftp://ftp.cs.wisc.edu/condor/bosco/latest/boscoinstaller> &lt;/pre&gt;

1.  Change the file permission and Install BOSCO as `bosco`: &lt;pre class="screen"&gt;

<span class="twiki-macro UCL_PROMPT"></span> chmod +x ~/boscoinstaller <span class="twiki-macro UCL_PROMPT"></span> ~/boscoinstaller --prefix=/opt/bosco &lt;/pre&gt;

1.  Remove the installer file: &lt;pre class="screen"&gt;

<span class="twiki-macro UCL_PROMPT"></span> rm ~/boscoinstaller &lt;/pre&gt;

<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> &lt;pre class="screen"&gt; \# The local BOSCO user in this example is called uc3 \[<root@uc3-bosco> ~\]\# mkdir -p /opt/bosco \[<root@uc3-bosco> ~\]\# chown uc3 /opt/bosco \[<root@uc3-bosco> ~\]\# su - uc3 -bash-3.2$ ./boscoinstaller --prefix=/opt/bosco Downloading BOSCO from <ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-beta-x86_64_RedHat5.tar.gz> Installing BOSCO in /opt/bosco Installing Condor from /tmp/tmp\_zACZK/condor-7.9.2-76336-x86\_64\_RedHat5-stripped to /opt/bosco Unable to find a valid Java installation Java Universe will not work properly until the JAVA (and JAVA\_MAXHEAP\_ARGUMENT) parameters are set in the configuration file!

Condor has been installed into: /opt/bosco

Configured condor using these configuration files: global: /opt/bosco/etc/condor\_config local: /opt/bosco/local.uc3-bosco/condor\_config.local

In order for Condor to work properly you must set your CONDOR\_CONFIG environment variable to point to your Condor configuration file: /opt/bosco/etc/condor\_config before running Condor commands/daemons. Created a script you can source to setup your Condor environment variables. This command must be run each time you log in or may be placed in your login scripts: source /opt/bosco/bosco\_setenv

Congratulations, you installed BOSCO succesfully! -bash-3.2$ -bash-3.2$ source /opt/bosco/bosco\_setenv &lt;/pre&gt; <span class="twiki-macro ENDTWISTY"></span>

---\#\# Add BOSCO to the default environment Add BOSCO to the default environment of all your users: &lt;pre class="root"&gt; <span class="twiki-macro UCL_PROMPT_ROOT"></span> cp /opt/bosco/bosco.sh /opt/bosco/bosco.csh /etc/profile.d/ &lt;/pre&gt; This is not required but it is recommended, so users don't have to setup the environment each time they login.

---\#\# Setup BOSCO to start automatically Setup BOSCO to start automatically after a reboot. Here I'm using the name "condor". If there is already a system HTCondor, a different name, e.g. condor\_bosco should be used: &lt;pre class="root"&gt; <span class="twiki-macro UCL_PROMPT_ROOT"></span> cp /opt/bosco/etc/examples/condor.boot.generic /etc/init.d/condor \# edit the values of CONDOR\_CONFIG and CONDOR\_CONFIG\_VAL CONDOR\_CONFIG=/opt/bosco/etc/condor\_config CONDOR\_CONFIG\_VAL=/opt/bosco/bin/condor\_config\_val <span class="twiki-macro UCL_PROMPT_ROOT"></span> chkconfig --level 235 condor on &lt;/pre&gt; This is not required but it is recommended, so BOSCO will restart automatically after a reboot.

<span class="twiki-macro ENDSECTION">BoscoInstall</span>

<span class="twiki-macro STARTSECTION">BoscoSetup</span> ---\# How to Use

Now BOSCO is installed. To use it:

1.  Setup the environment
2.  Add all the desired clusters (at least one)
3.  Start BOSCO
4.  Submit a test job
5.  Submit a real job

---\#\# Setup environment before using \#SetupEnvironment If the administrator did not add BOSCO to the default environment (last step in the installation above), then an environment file must be sourced all the times you use BOSCO (start/stop/job submission or query, anything):

``` screen
%UCL_PROMPT% source /opt/bosco/bosco_setenv
```

---\#\# Starting BOSCO \#BoscoStart BOSCO has some persistent services that must be running. You'll have to start it at the beginning and probably after each reboot of your host. You should stop BOSCO before an upgrade and possibly before a shutdown of your host. If you will not use BOSCO anymore, uninstall will remove it from your system.

To start BOSCO, as **ROOT**:&lt;pre class="rootscreen"&gt; <span class="twiki-macro UCL_PROMPT_ROOT"></span> bosco\_start &lt;/pre&gt;

---\#\# Add a cluster to BOSCO Follow the following steps as the **BOSCO** user.

<span class="twiki-macro INCLUDE" section="BoscoAddResource">BoscoInstall</span>

---\#\# Submitting a test job You can send a simple test job to verify that the cluster added is working correctly. The following steps are as the **BOSCO** user.

To send a BOSCO test job to the host [%RED%username@mycluster-submit.mydomain%ENDCOLOR](mailto:%RED%username@mycluster-submit.mydomain%ENDCOLOR)% (name as listed in the output of `bosco_cluster --list`):

1.  Setup the environment appropriate for your shell as described in the setup environment section (above).
2.  For the cluster [%RED%username@mycluster-submit.mydomain%ENDCOLOR](mailto:%RED%username@mycluster-submit.mydomain%ENDCOLOR)% (identical to output of `bosco_cluster --list`). Replace the parts in red: &lt;pre class="screen"&gt;

<span class="twiki-macro UCL_PROMPT"></span> bosco\_cluster --test [%RED%username@mycluster-submit.mydomain%ENDCOLOR](mailto:%RED%username@mycluster-submit.mydomain%ENDCOLOR)% &lt;/pre&gt; <span class="twiki-macro TWISTY">%TWISTY\_OPTS\_DETAILED% </span> &lt;pre class="screen"&gt; <span class="twiki-macro UCL_PROMPT"></span> $ bosco\_cluster -t <dweitzel@ff-grid.unl.edu> <dweitzel@ff-grid.unl.edu> Testing ssh to <dweitzel@ff-grid.unl.edu>...Passed! Testing bosco submission...Passed! Checking for submission to remote pbs cluster (could take ~30 seconds)...Passed! Submission files for these jobs are in /home/dweitzel/bosco/local.localhocentos56/bosco-test Execution on the remote cluster could take a while...Exiting &lt;/pre&gt; <span class="twiki-macro ENDTWISTY"></span>

---\#\# How to Stop and Remove To stop BOSCO, as **root** (remember, all the commands with a orange-red background require you to be root):&lt;pre class="rootscreen"&gt; <span class="twiki-macro UCL_PROMPT_ROOT"></span> bosco\_stop &lt;/pre&gt;

To uninstall BOSCO:

-   First remove the startup script if you added them: &lt;pre class="rootscreen"&gt;<span class="twiki-macro UCL_PROMPT_ROOT"></span> rm /etc/profile.d/bosco.\*&lt;/pre&gt;
-   To remove everything: installation directory, remote clusters and the BOSCO files in your `.bosco` and `.ssh` directories you can use:&lt;pre class="rootscreen"&gt;<span class="twiki-macro UCL_PROMPT_ROOT"></span> bosco\_uninstall --all&lt;/pre&gt;
-   To remove the remote clusters get the list and remove them one by one: &lt;pre class="screen"&gt;<span class="twiki-macro UCL_PROMPT"></span> bosco\_cluster --list

\# For each remote cluster <span class="twiki-macro UCL_PROMPT"></span> bosco\_cluster -r %RED%user@cluster\_as\_spelled\_in\_list%ENDCOLOR%&lt;/pre&gt;

-   To remove only the installation directory: &lt;pre class="rootscreen"&gt;<span class="twiki-macro UCL_PROMPT_ROOT"></span> bosco\_uninstall&lt;/pre&gt;

<span class="twiki-macro NOTE"></span> Uninstalling BOSCO installation directory removes the software but leaves the files in your `.bosco` and `.ssh` directories with all the information about the added clusters and the SSH keys. Files installed on the remote clusters are not removed either.

---\#\# How to Update BOSCO If you want to update BOSCO to a new version you have to follow the following steps.

<span class="twiki-macro NOTE"></span> Please notice that some commands need to be run by root, and others by the bosco user

1.  setup BOSCO:&lt;pre class="rootscreen"&gt;<span class="twiki-macro UCL_PROMPT"></span> source /opt/bosco/bosco\_setenv&lt;/pre&gt;
2.  stop BOSCO: &lt;pre class="rootscreen"&gt;<span class="twiki-macro UCL_PROMPT_ROOT"></span> bosco\_stop&lt;/pre&gt;
3.  remove the old BOSCO: &lt;pre class="rootscreen"&gt;<span class="twiki-macro UCL_PROMPT_ROOT"></span> bosco\_uninstall&lt;/pre&gt;
4.  download and install the new BOSCO (see install section above) and re-add all the clusters in your setup:
5.  for each installed cluster (the list is returned by `bosco_cluster --list`):
    1.  remove the cluster: &lt;pre class="screen"&gt;<span class="twiki-macro UCL_PROMPT"></span> bosco\_cluster --remove [%RED%username@mycluster-submit.mydomain.org%ENDCOLOR](mailto:%RED%username@mycluster-submit.mydomain.org%ENDCOLOR)% &lt;/pre&gt;
    2.  add the cluster: &lt;pre class="screen"&gt;<span class="twiki-macro UCL_PROMPT"></span> bosco\_cluster --add [%RED%username@mycluster-submit.mydomain.org](mailto:%RED%username@mycluster-submit.mydomain.org) queue<span class="twiki-macro ENDCOLOR"></span> &lt;/pre&gt;

6.  start BOSCO: &lt;pre class="rootscreen"&gt;<span class="twiki-macro UCL_PROMPT_ROOT"></span> bosco\_start&lt;/pre&gt;

This will update the local installation and the software on the remote clusters <span class="twiki-macro ENDSECTION">BoscoSetup</span>

<span class="twiki-macro INCLUDE" section="BoscoJob">BoscoInstall</span>

---\# Advanced use

---\#\# Flocking to the BOSCO submit host If you have a more complex Campus Grid environment you can flock from your submit hosts to the BOSCO submit host to have access to the resources available through BOSCO.

On the BOSCO submit host:

-   Edit `/opt/bosco/local.*/config/condor_config.factory` to add you submit host (fully qualified domain name or IP address) in the FLOCK\_FROM line, e.g.: &lt;pre class="file"&gt;\# What hosts can run jobs to this cluster.

FLOCK\_FROM = %RED%yourname.yourdomain.org<span class="twiki-macro ENDCOLOR"></span> &lt;/pre&gt;

On your Campus Grid submit host add the BOSCO submit host in the FLOCK\_TO entry of the Condor configuration, e.g. `/etc/condor/condor_config.local`: &lt;pre class="file"&gt;\# What hosts can run jobs to this cluster. FLOCK\_TO = %RED%bosco.uchicago.edu<span class="twiki-macro ENDCOLOR"></span>:11000?sock=collector &lt;/pre&gt;

<span class="twiki-macro NOTE"></span> that the BOSCO submit host is running on port 11000 and it is using the single port daemon.

---\#\# Querying the status of BOSCO If you have a monitoring system and you want to query BOSCO you need to know the following:

-   The BOSCO submit host collector runs under the shared port collector on port 11000: **`bosco.yourdomain.org:11000?sock=collector`**
-   The host sending the query needs to be authorized to run the query

<span class="twiki-macro INCLUDE" section="BoscoAdvancedUseInstContent">BoscoInstall</span>

---\# Troubleshooting

---\#\# Useful Configuration and Log Files BOSCO underneath is using Condor. You can find all the Condor log files in `/opt/bosco/local.HOSTNAME/log` (supposing that `/opt/bosco` is the installation directory).

<span class="twiki-macro INCLUDE" section="BoscoTroubleshootingItems">BoscoInstall</span>

---\# Get Help/Support To get assistance you can send an email to <bosco-discuss@opensciencegrid.org>

<span class="twiki-macro INCLUDE" section="BoscoReferences">BoscoInstall</span>

Comments
========

<span class="twiki-macro COMMENT" type="tableappend"></span>
