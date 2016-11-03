

BOSCO Multi User
================


### Introduction

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

### Requirements
<dl>
   <dt>BOSCO Submit-node:</dt> <dd>This is the system that the researcher uses to submit jobs. In general it can be the user's laptop, workstation, or it can be another system that the user logs into for submitting jobs to the cluster.  *There can not be any Condor collector running on the submit node*, otherwise it will conflict with Bosco.</dd>
  <dt>BOSCO resource (aka Cluster) submit-node:</dt> <dd>This is the node that you normally login to on the PBS, SGE, LSF, SLURM or HTCondor cluster (the BOSCO resource that you'd like to add).</dd>
      <dt>PBS flavors supported:</dt> <dd>Torque and PBSPro</dd>
      <dt>HTCondor flavors supported:</dt> <dd>HTCondor 7.6 or later</dd>
      <dt>SGE flavors supported:</dt> <dd>no special requirements (Sun Grid Engine and other Grid Engine versions supported)</dd>
      <dt>LSF flavors:</dt> <dd>no special requirements</dd>
      <dt>SLURM flavors:</dt> <dd>requires Torque/PBS command wrappers (from SLURM contrib)</dd>
   <dt>BOSCO resource (aka Cluster):</dt> <dd>This is the remote cluster that jobs will execute on (the BOSCO resource).</dd>  
</dl>
   The Cluster submit-node is a node belonging to this cluster. The nodes where jobs run are referred as worker nodes.  All the cluster needs:
<dl>
      <dt>Shared Filesystem:</dt> <dd>The Cluster needs a shared home filesystem (if the Cluster has no shared filesystem only Grid universe jobs can be sent to it)</dd>
      <dt>Network Access:</dt> <dd>The worker nodes need to have access to the submit host.</dd>  
</dl>
The worker nodes can be behind a [[https://en.wikipedia.org/wiki/Network_address_translation][NAT]] between the worker nodes and the submit host.
The BOSCO resource requirements just listed (shared file system and outbound network access) are required only if you submit jobs using the HTCondor _vanilla_ universe. If you submit jobs using the *grid universe*, submitting to one resource at the time, then both requirements can be relaxed. I.e. there is *no need of a shared file system or access to the BOSCO submit host from the BOSCO resource*.

BOSCO can be used as part of a more complex Condor setup (with flocking or multiple pools). Whatever the setup:
   * the BOSCO host needs connectivity to the cluster submit nodes of the BOSCO resources
   * the worker nodes of the BOSCO resources (running the jobs, e.g. the nodes in the PBS cluster) must have network connectivity to the jobs submit node (the BOSCO host or a different Condor schedd flocking into it) 

# Networking
## Firewalls
### BOSCO submit host

These firewall requirements are for the BOSCO submit host. 

Please Note: The port 11000 is required *ONLY* if "vanilla" jobs are submitted. "vanilla" jobs require also that all worker nodes be able to reach the submit host: this means that the submit host has to have a public IP address or at least an address known by all BOSCO resources that are going to be added. If you need to use a port different form 11000, you can edit the BOSCO configuration.

 
### How to Install
**Creating the Bosco User**
As `root`, create a `bosco` user and a install directory (e.g. `/opt/bosco`) that all users can see and use (all users should be able to read, `bosco` should be able to write).   
```
  useradd bosco 
  mkdir -p /opt/bosco 
  chown bosco: /opt/bosco 
```

**Download and Install BOSCO Perform the following steps as the newly created `bosco` user.**

1. Download the BOSCO installer using one of these alternatives (browser, wget, curl) then continue with step two: \* BOSCO comes in multiple flavors depending on your platform and the installer will download and install the correct flavor for you. Right click on [this link](ftp://ftp.cs.wisc.edu/condor/bosco/latest/boscoinstaller), select "save as" and save the file in the bosco user home directory. If your browser is saving the file in a different folder (e.g the Downloads folder), then move the file to the `bosco` user home directory before continuing with step 2. (any place is OK as long as you find the installer).

**Alternatively you can use `wget` and the download link:** 

``` 
 cd ~ 
  wget <ftp://ftp.cs.wisc.edu/condor/bosco/latest/boscoinstaller> 
```


If you have no `wget`, you can use `curl` to download: 


```
  curl -o ~/boscoinstaller <ftp://ftp.cs.wisc.edu/condor/bosco/latest/boscoinstaller> 
```


1.  Change the file permission and Install BOSCO as `bosco`:  


```
  chmod +x ~/boscoinstaller 
  ~/boscoinstaller --prefix=/opt/bosco 
```


1.  Remove the installer file:  


```
  rm ~/boscoinstaller 
```

The local BOSCO user in this example is called uc3 

```
root@uc3-bosco # mkdir -p /opt/bosco 
root@uc3-bosco # chown uc3 /opt/bosco  
root@uc3-bosco # su - uc3 -bash-3.2$ ./boscoinstaller --prefix=/opt/bosco Downloading BOSCO from ftp://ftp.cs.wisc.edu/condor/bosco/latest/bosco-beta-x86_64_RedHat5.tar.gz 
Installing BOSCO in /opt/bosco 
Installing Condor from /tmp/tmp_zACZK/condor-7.9.2-76336-x86_64_RedHat5-stripped to /opt/bosco 
Unable to find a valid Java installation 
Java Universe will not work properly until the JAVA (and JAVA_MAXHEAP_ARGUMENT) parameters are set in the configuration file!

Condor has been installed into: /opt/bosco

Configured condor using these configuration files: global: /opt/bosco/etc/condor_config local: /opt/bosco/local.uc3-bosco/condor_config.local

In order for Condor to work properly you must set your CONDOR_CONFIG environment variable to point to your Condor configuration file: /opt/bosco/etc/condor_config before running Condor commands/daemons. Created a script you can source to setup your Condor environment variables. This command must be run each time you log in or may be placed in your login scripts: source /opt/bosco/bosco_setenv

Congratulations, you installed BOSCO succesfully! 
-bash-3.2$ -bash-3.2$ source /opt/bosco/bosco_setenv 
```

**Add BOSCO to the default environment Add BOSCO to the default environment of all your users:**
```
  cp /opt/bosco/bosco.sh /opt/bosco/bosco.csh /etc/profile.d/ 
```
This is not required but it is recommended, so users don't have to setup the environment each time they login.

**Setup BOSCO to start automatically**
Setup BOSCO to start automatically after a reboot. Here I'm using the name "condor". If there is already a system HTCondor, a different name, e.g. condor_bosco should be used: 
```
cp /opt/bosco/etc/examples/condor.boot.generic /etc/init.d/condor   edit the values of CONDOR_CONFIG and CONDOR_CONFIG_VAL CONDOR_CONFIG=/opt/bosco/etc/condor_config CONDOR_CONFIG_VAL=/opt/bosco/bin/condor_config_val 

chkconfig --level 235 condor on 
```
 This is not required but it is recommended, so BOSCO will restart automatically after a reboot.
 
### How to Use

Now BOSCO is installed. To use it:

1.  Setup the environment
2.  Add all the desired clusters (at least one)
3.  Start BOSCO
4.  Submit a test job
5.  Submit a real job

### Setup environment 
If the administrator did not add BOSCO to the default environment (last step in the installation above), then an environment file must be sourced all the times you use BOSCO (start/stop/job submission or query, anything):

``` 
source /opt/bosco/bosco_setenv
```
### Starting BOSCO  
BOSCO has some persistent services that must be running. You'll have to start it at the beginning and probably after each reboot of your host. You should stop BOSCO before an upgrade and possibly before a shutdown of your host. If you will not use BOSCO anymore, uninstall will remove it from your system.

To start BOSCO, as **ROOT**:  
```
  bosco_start 
```
Add a cluster to BOSCO Follow the following steps as the **BOSCO** user.

**Submitting a test job**
You can send a simple test job to verify that the cluster added is working correctly. The following steps are as the **BOSCO** user.

To send a BOSCO test job to the host [%RED%username@mycluster-submit.mydomain%ENDCOLOR](mailto:%RED%username@mycluster-submit.mydomain%ENDCOLOR)% (name as listed in the output of `bosco_cluster --list`):

1.  Setup the environment appropriate for your shell as described in the setup environment section (above).
2.  For the cluster [%RED%username@mycluster-submit.mydomain%ENDCOLOR](mailto:%RED%username@mycluster-submit.mydomain%ENDCOLOR)% (identical to output of `bosco_cluster --list`). Replace the parts in red:  


  bosco_cluster --test [%RED%username@mycluster-submit.mydomain%ENDCOLOR](mailto:%RED%username@mycluster-submit.mydomain%ENDCOLOR)% 
```  
  $ bosco_cluster -t <dweitzel@ff-grid.unl.edu> <dweitzel@ff-grid.unl.edu> Testing ssh to <dweitzel@ff-grid.unl.edu>...Passed! Testing bosco submission...Passed! Checking for submission to remote pbs cluster (could take ~30 seconds)...Passed! Submission files for these jobs are in /home/dweitzel/bosco/local.localhocentos56/bosco-test Execution on the remote cluster could take a while...Exiting 
```

**How to Stop and Remove** 
To stop BOSCO, as **root** (remember, all the commands with a orange-red background require you to be root):  
```
  bosco_stop 
```
To uninstall BOSCO:

First remove the startup script if you added them: 
```
  rm /etc/profile.d/bosco.\*
```

**To remove everything: installation directory, remote clusters and the BOSCO files in your `.bosco` and `.ssh` directories you can use:**
```
  bosco_uninstall --all
```

**To remove the remote clusters get the list and remove them one by one:**  
```
  bosco_cluster --list
```
  For each remote cluster 
```
  bosco_cluster -r %RED%user@cluster_as_spelled_in_list%ENDCOLOR%
```
**To remove only the installation directory:**
```
 bosco_uninstall
```


Please note: Uninstalling BOSCO installation directory removes the software but leaves the files in your `.bosco` and `.ssh` directories with all the information about the added clusters and the SSH keys. Files installed on the remote clusters are not removed either.
**How to Update BOSCO**
If you want to update BOSCO to a new version you have to follow the following steps.

Please note: Some commands need to be run by root, and others by the bosco user
1.  setup BOSCO: 
```
  source /opt/bosco/bosco_setenv
```
2.  stop BOSCO: 
```
  bosco_stop
```
 3.  remove the old BOSCO: 
```
  bosco_uninstall
```
4.  download and install the new BOSCO (see install section above) and re-add all the clusters in your setup:
5.  for each installed cluster (the list is returned by `bosco_cluster --list`):
    1.  remove the cluster:  
    
```
  bosco_cluster --remove [%RED%username@mycluster-submit.mydomain.org%ENDCOLOR](mailto:%RED%username@mycluster-submit.mydomain.org%ENDCOLOR)% 
```
     2.  add the cluster:  
```
bosco_cluster --add [%RED%username@mycluster-submit.mydomain.org](mailto:%RED%username@mycluster-submit.mydomain.org) queue<span class="twiki-macro ENDCOLOR">  
```
6.  start BOSCO: 
```
  bosco_start
```


This will update the local installation and the software on the remote clusters  BoscoSetup 
 
<span class="twiki-macro INCLUDE" section="BoscoJob">BoscoInstall 

### Advanced use
**Flocking to the BOSCO submit host**
If you have a more complex Campus Grid environment you can flock from your submit hosts to the BOSCO submit host to have access to the resources available through BOSCO.

On the BOSCO submit host:

-   Edit `/opt/bosco/local.*/config/condor_config.factory` to add you submit host (fully qualified domain name or IP address) in the FLOCK_FROM line, e.g.: What hosts can run jobs to this cluster. FLOCK_FROM = %RED%yourname.yourdomain.org<span class="twiki-macro ENDCOLOR">  



On your Campus Grid submit host add the BOSCO submit host in the FLOCK_TO entry of the Condor configuration, e.g. `/etc/condor/condor_config.local`:  What hosts can run jobs to this cluster. FLOCK_TO = %RED%bosco.uchicago.edu<span class="twiki-macro ENDCOLOR"> :11000?sock=collector 


Please note: that the BOSCO submit host is running on port 11000 and it is using the single port daemon.

**Querying the status of BOSCO**
If you have a monitoring system and you want to query BOSCO you need to know the following:

-   The BOSCO submit host collector runs under the shared port collector on port 11000: **`bosco.yourdomain.org:11000?sock=collector`**
-   The host sending the query needs to be authorized to run the query

<span class="twiki-macro INCLUDE" section="BoscoAdvancedUseInstContent">BoscoInstall 
### Troubleshooting
_ Useful Configuration and Log Files BOSCO underneath is using Condor. You can find all the Condor log files in `/opt/bosco/local.HOSTNAME/log` (supposing that `/opt/bosco` is the installation directory).

<span class="twiki-macro INCLUDE" section="BoscoTroubleshootingItems">BoscoInstall 
### Get Help/Support To get assistance you can send an email to <bosco-discuss@opensciencegrid.org>

<span class="twiki-macro INCLUDE" section="BoscoReferences">BoscoInstall 

Comments
========

<span class="twiki-macro COMMENT" type="tableappend"> 
