

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

## Requirements

* **BOSCO Submit-node**
:   This is the system that the researcher uses to submit jobs. In
    general it can be the user's laptop, workstation, or it can be
    another system that the user logs into for submitting jobs to
    the cluster. **There can not be any Condor collector running on the
    submit node**, otherwise it will conflict with Bosco.

* **BOSCO resource (aka Cluster) submit-node**
:   This is the node that you normally login to on the PBS, SGE, LSF,
    SLURM or HTCondor cluster (the BOSCO resource that you'd like
    to add).

  * **PBS flavors supported**
:   Torque and PBSPro

  * **HTCondor flavors supported**
:   HTCondor 7.6 or later

  * **SGE flavors supported**
:   no special requirements (Sun Grid Engine and other Grid Engine
    versions supported)

  * **LSF flavors**
:   no special requirements

  * **SLURM flavors**
:   no special requirements

**BOSCO resource (aka Cluster)**
:   This is the remote cluster that jobs will execute on (the
    BOSCO resource). The Cluster submit-node is a node belonging to
    this cluster. The nodes where jobs run are referred as worker nodes.
    All the cluster needs:

 * **Shared Filesystem**
    :   The Cluster needs a shared home filesystem (if the Cluster has         no shared filesystem only Grid universe jobs can be sent to it)

 * **Network Access**
    :   The worker nodes need to have access to the submit host. The         worker nodes can be behind a         [NAT](https://en.wikipedia.org/wiki/Network_address_translation)         between the worker nodes and the submit host.

!!! note 
    The BOSCO resource requirements
    just listed (shared file system and outbound network access) are
    required only if you submit jobs using the HTCondor *vanilla* universe.
    If you submit jobs using the **grid universe**, submitting to one
    resource at the time, then both requirements can be relaxed. I.e. there
    is **no need of a shared file system or access to the BOSCO submit host
    from the BOSCO resource**.
BOSCO can be used as part of a more complex Condor setup (with flocking
or multiple pools). Whatever the setup:

-   the BOSCO host needs connectivity to the cluster submit nodes of the
    BOSCO resources
-   the worker nodes of the BOSCO resources (running the jobs, e.g. the
    nodes in the PBS cluster) must have network connectivity to the jobs
    submit node (the BOSCO host or a different Condor schedd flocking
    into it)
    

## Networking

### BOSCO submit host

The BOSCO submit host requires no ports open for `universe = grid` jobs.  For 
`universe = vanilla` jobs, port 11000 is required.


!!! note 
    The port 11000 is required
    **ONLY** if "vanilla" jobs are submitted. "vanilla" jobs require also
    that all worker nodes be able to reach the submit host: this means that
    the submit host has to have a public IP address or at least an address
    known by all BOSCO resources that are going to be added. If you need to
    use a port different from 11000, you can edit the BOSCO configuration
 
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


2.  Remove the installer file:  


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
    i.  remove the cluster:  
    
```
  bosco_cluster --remove [%RED%username@mycluster-submit.mydomain.org%ENDCOLOR](mailto:%RED%username@mycluster-submit.mydomain.org%ENDCOLOR)% 
```
     ii.  add the cluster:  
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


## Changing the BOSCO port
BOSCO is using the HTCondor [[http://research.cs.wisc.edu/htcondor/manual/latest/3_7Networking_includes.html#SECTION00472000000000000000][Shared port daemon]]. This means that all the communication are coming to the same port, by default 11000. If that port is taken (already bound), the [[BoscoQuickStart][quick start installer]] will select the first available port. You can check and edit manually the port used by BOSCO in the file =$HOME/bosco/local.bosco/config/condor_config.factory=. You can change the port passed to the shared port daemon (in %RED%red%ENDCOLOR%): <pre class="file"># Enabled Shared Port
USE_SHARED_PORT = True
SHARED_PORT_ARGS = -p %RED%11000%ENDCOLOR%
</pre>
!!! note You need to restart BOSCO after you change the configuration (=bosco_stop; bosco_start=).

If you are referring to this BOSCO pool (e.g. for flocking) you'll need to use a string like: =%RED%your_host.domain%ENDCOLOR%:%RED%11000%ENDCOLOR%?sock=collector= .
Replace host and port with the correct ones.

## Multi homed hosts
Multi homed hosts are hosts with multiple Network Interfaces (aka dual-homed when they have 2 NICs).
BOSCO configuration is tricky on multi-homed hosts. BOSCO requires the submit host to be able to connect back to the BOSCO host, so it must advertise an interface that is reachable from all the chosen submit hosts. E.g. a host with a NIC on a private network and one with a public IP address must advertise the public address if the submit hosts are outside of the private network. 
In order to do that you have to:
   * make sure that the name returned by the command =/bin/hostname -f= is the name resolving in the public address (e.g. =host `hostname -f`= should return the public address). If not you should change it.
   * edit ~/bosco/local.%RED%$HOST%ENDCOLOR%/condor_config.local (HOST is the short host name) and add a line like =NETWORK_INTERFACE = xxx.xxx.xxx.xxx= , substituting xxx.xxx.xxx.xxx with the public IP address. This will tell BOSCO to use that address.


## Modifying maximum number of submitted jobs to a resource

Many clusters limit the number of jobs that can be submitted to the scheduler.  For PBS, we are able to detect this limit.  For SGE and LSF, we are not able to detect this limit.  In the cases where we cannot find the limit, we set the maximum number of jobs very conservatively, to a maximum of 10.  This includes both the number of idle and running jobs to the cluster.

The limit is specified in the condor config file ~/bosco/local.bosco/condor_config.local, at the bottom.  Edit the value of the configuration variable GRIDMANAGER_MAX_SUBMITTED_JOBS_PER_RESOURCE

<pre class="file">
GRIDMANAGER_MAX_SUBMITTED_JOBS_PER_RESOURCE = %RED%10%ENDCOLOR%
</pre>

## Custom submit properties
Bosco has the ability to add custom submit properties to every job submitted to a cluster.  On the cluster's login node (the BOSCO resource, the host you used at the end of the line when typing the =bosco_cluster --add= command), create the file 

#CustomScriptLocations
   * *PBS/SLURM* - ~/bosco/glite/bin/pbs_local_submit_attributes.sh
   * *Condor* - ~/bosco/glite/bin/condor_local_submit_attributes.sh
   * *SGE* (and other GE) - ~/bosco/glite/bin/sge_local_submit_attributes.sh
   * *LSF* - ~/bosco/glite/bin/lsf_local_submit_attributes.sh

%IMPORTANT% This file is executed and the output is inserted into the submit script. I.e. It is not cat, use echo/cat statements in the script.

Below is an example =pbs_local_submit_attributes.sh= script which will cause every job submitted to this cluster through Bosco to request 1 node with 8 cores:
<pre>
#!/bin/sh

echo "#PBS -l nodes=1:ppn=8"
</pre>

### Passing parameters to the custom submit properties.
You may also pass parameters to the custom scripts by adding a special parameter to the Bosco submit script.

For example, in your Bosco submit script, add: <pre class="file">
...
%RED%+remote_cerequirements = NumJobs == 100%ENDCOLOR%
...
queue
</pre>

After you submit this job to Bosco, it will execute the [[#CustomScriptLocations][custom scripts]] with, in this example, =NumJobs= set in the environment equal to =100=.  The custom script can take advantage of these values.  For example, a PBS script can use the !NumJobs: 
<pre class="screen">
#!/bin/sh

echo "#PBS -l select=$NumJobs"
</pre>

This will set the number of requested cores from PBS to !NumJobs specified in the original Bosco Submit file.

## Flocking to a BOSCO installation
In some special cases you may desire to flock to your BOSCO installation. If you don't know what I'm talking about, then skip this section.

In order to enable flocking you must use an IP so that all the hosts you are flocking from can communicate with the BOSCO host.
Then you must setup FLOCK_FROM and the security configuration so that the communications are authorized.

BOSCO has strong security settings. Here are two examples:
   1 Using GSI authentication (a strong authentication method) you must provide and install X509 certificates, you must change the configuration 
   <pre class="screen">
Networking - If you did not already, remember that you need to set BOSCO not to use the loopback port

NETWORK_INTERFACE =


 Hosts definition

 BOSCO host
H_BOSCO = %RED%bosco.mydomain.edu%ENDCOLOR%
H_BOSCO_DN = %RED%/DC=com/DC=DigiCert-Grid/O=Open Science Grid/OU=Services/CN=bosco.mydomain.edu%ENDCOLOR%

 submit host (flocking to BOSCO host)
H_SUB = %RED%sub.mydomain.edu%ENDCOLOR%
H_SUB_DN = %RED%/DC=com/DC=DigiCert-Grid/O=Open Science Grid/OU=Services/CN=sub.mydomain.edu%ENDCOLOR%


 Flocking configuration
 
FLOCK_FROM = $(FLOCK_FROM) $(H_SUB)


 Security definitions

 Assuming system-wide installed CA certificates 
GSI_DAEMON_DIRECTORY = /etc/grid-security
 This host's certificates
GSI_DAEMON_CERT = /etc/grid-security/hostcert.pem
GSI_DAEMON_KEY = /etc/grid-security/hostkey.pem
 default GSI_DAEMON_TRUSTED_CA_DIR = $(GSI_DAEMON_DIRECTORY)/certificates
CERTIFICATE_MAPFILE= $HOME/bosco/local.bosco/certs/condor_mapfile

 Not used
MY_DN = $(H_BOSCO_DN)

 Who to trust?  Include the submitters flocking here
GSI_DAEMON_NAME = $(GSI_DAEMON_NAME), $(H_BOSCO_DN), $(H_SUB_DN)
Enable authentication from the Negotiator
SEC_ENABLE_MATCH_PASSWORD_AUTHENTICATION = TRUE

 Enable gsi authentication, and claimtobe (for campus factories)
 The default (unix) should be: FS, KERBEROS, GSI
SEC_DEFAULT_AUTHENTICATION_METHODS = FS,GSI, PASSWORD, $(SEC_DEFAULT_AUTHENTICATION_METHODS)
SEC_CLIENT_AUTHENTICATION_METHODS = FS, PASSWORD, GSI, CLAIMTOBE
SEC_DAEMON_AUTHENTICATION_METHODS = FS, PASSWORD, GSI, CLAIMTOBE
SEC_WRITE_AUTHENTICATION_METHODS = FS, PASSWORD, GSI, CLAIMTOBE
SEC_ADVERTISE_SCHEDD_METHODS = FS, PASSWORD, GSI, CLAIMTOBE

ALLOW_DAEMON = $(ALLOW_DAEMON) condor_pool@*/* %RED%boscouser%ENDCOLOR%@*/* $(FULL_HOSTNAME) $(IP_ADDRESS)
ALLOW_ADVERTISE_SCHEDD = %RED%boscouser%ENDCOLOR%@*/*
</pre>    <pre class="screen">#
GSI "^%RED%\/DC\=com\/DC\=DigiCert\-Grid\/O\=Open\ Science\ Grid\/OU\=Services\/CN\=sub\.mydomain\.edu%ENDCOLOR%$" %RED%boscouser@sub.mydomain.edu%ENDCOLOR%
GSI "^%RED%\/DC\=com\/DC\=DigiCert\-Grid\/O\=Open\ Science\ Grid\/OU\=Services\/CN\=bosco\.mydomain\.edu%ENDCOLOR%$" %RED%boscouser%ENDCOLOR%

SSL (.*) ssl@unmapped
CLAIMTOBE (.*) \1
PASSWORD (.*) \1

GSI (.*) anonymous
FS (.*) \1
</pre> Remember to enable and configure GSI authentication also on the host you are flocking form.
   1 Relaxing BOSCO security setting to allow CLAIMTOBE authentication. This is not very secure. Use it only if you can trust all the machines on the network and remember to enable CLAIMTOBE also on the host you are flocking from  <pre class="screen">
\#
\# Networking - If you did not already, remember that you need to set BOSCO not to use the loopback port
\#
NETWORK_INTERFACE =

\#
\# Flocking configuration
\#
FLOCK_FROM = %RED%host_from.domain%ENDCOLOR%

\#
\# Security definitions overrides
\# 
SEC_DEFAULT_ENCRYPTION = OPTIONAL
SEC_DEFAULT_INTEGRITY = PREFERRED
\# To allow status read
SEC_READ_INTEGRITY = OPTIONAL

SEC_CLIENT_AUTHENTICATION_METHODS = FS, PASSWORD, CLAIMTOBE

ALLOW_ADVERTISE_SCHEDD = */%RED%IP_of_the_host_in_flock_from%ENDCOLOR% $(FULL_HOSTNAME) $(IP_ADDRESS) $(ALLOW_DAEMON)

SEC_DAEMON_AUTHENTICATION = PREFERRED
SEC_DAEMON_INTEGRITY = PREFERRED
SEC_DAEMON_AUTHENTICATION_METHODS = FS,PASSWORD,CLAIMTOBE
SEC_WRITE_AUTHENTICATION_METHODS = FS,PASSWORD,CLAIMTOBE
</pre> 

After copying from the examples (click above to expand the example files) or editing your configuration file, save it as =$HOME/bosco/local.bosco/config/zzz_condor_config.flocking=. 
Other names are OK as long as its definition override the default ones of BOSCO (check with =condor_config_val -config=).

Then stop and restart BOSCO.
### Troubleshooting
**Useful Configuration and Log Files** 
BOSCO underneath is using Condor. You can find all the Condor log files in `/opt/bosco/local.HOSTNAME/log` (supposing that `/opt/bosco` is the installation directory).

## Make sure that you can connect to the BOSCO host
If you see errors like:<pre class="screen">
 Installing BOSCO on user@osg-ss-submit.chtc.wisc.edu...
 ssh: connect to host osg-ss-submit.chtc.wisc.edu port 22: Connection timed out
 rsync: connection unexpectedly closed (0 bytes received so far) [sender]
 rsync error: unexplained error (code 255) at io.c(600) [sender=3.0.6]
 ssh: connect to host osg-ss-submit.chtc.wisc.edu port 22: Connection timed out
 rsync: connection unexpectedly closed (0 bytes received so far) [sender]
 rsync error: unexplained error (code 255) at io.c(600) [sender=3.0.6]
 ssh: connect to host osg-ss-submit.chtc.wisc.edu port 22: Connection timed out
 rsync: connection unexpectedly closed (0 bytes received so far) [sender]
 rsync error: unexplained error (code 255) at io.c(600) [sender=3.0.6]
</pre>
Please try manually to ssh from the BOSCO host to the cluster submit node. The ability to connect is required in order to install BOSCO.

## Make sure that BOSCO is running
BOSCO may not survive after you log out, for example if the BOSCO node was restarted while you where logged out. 
When you log back in after sourcing the setup as described in the [[#SetupEnvironment][setup environment section]], you should start BOSCO as described in the [[#BoscoStart][BOSCO start section]], specially if the command =condor_q= is failing.

## Errors due to leftover files
Bosco files on the submit host are in:
   * ~/bosco/ - the release directory
   * ~/.bosco/ - some service files
   * ~/.ssh/ - the ssh key used by BOSCO

If you used bosco_uninstall it will remove all BOSCO related files. If you removed BOSCO by hand you must pay attention.
If the service key is still in .ssh but the other files are missing, during the execution of BOSCO commands you will get some unclear errors like 
**"IOError: [Errno 2] No such file or directory: '/home/marco/.bosco/.pass'"** , **"OSError: [Errno 5] Input/output error"** , all followed by:
<pre>Password-less ssh to marco@itb2.uchicago.edu did NOT work, even after adding the ssh-keys.
Does the remote resource allow password-less ssh?
</pre>

If that happens you can remove the service files and the keys using:<pre>rm -r ~/.bosco
rm ~/.ssh/bosco_key.rsa*</pre>
and then re-add all the clusters with bosco_cluster --add.

## Unable to download and prepare BOSCO for remote installation. 
BOSCO can return this error:
   1. Because the BOSCO submit host is unable to download BOSCO for the resource installation, e.g. a firewall is blocking the download or the server is down
   2. More commonly because there are problems with the login host of the BOSCO resource, e.g. the disk is full or there are multiple login nodes
You can check 1 byy downloading BOSCO on your BOSCO submit host.
To check 2 you have to login on the BOSCO resource: =df= will tell you you some disks are full, with =hostname -f= you can check if the name is different form the one that you used to login with ssh. If the name differs probably you are using a cluster with multiple login nodes and you must use only one for BOSCO. Se the second "IMPORTANT" note in the [[#AddResourceSection][section to add a cluster to BOSCO]] (above).

If you see errors similar to the one below while executing *bosco_cluster --add*:
<pre class="screen">
Downloading for USER@RESOURCE
Unpacking.tar: Cannot save working directory 
tar: Error is not recoverable: exiting now 
ls: /tmp/tmp.qeIJ9139/condor*: No such file or directory 
Unable to download and prepare BOSCO for remote installation. 
</pre>
then you are using most likely the generic name of a multi-login cluster and you should use the name of one of the nodes as suggested in the [[#AddResourceSection][note above]]. 

### Get Help/Support To get assistance you can send an email to <bosco-discuss@opensciencegrid.org>

### References 
[[http://bosco.opensciencegrid.org/][BoSCO Web site]] and documents about the latest production release (v1.2)
   * [[BoSCO][Using Bosco]]
   * [[BoscoInstall][Installing BoSCO]]
   * [[BoscoMultiUser][Installing BoSCO Multi User]]
   * [[BoscoQuickStart][Quick start guide to Bosco]]
   * BoscoR

Campus Grids related documents:
   * https://twiki.grid.iu.edu/bin/view/CampusGrids
   * https://twiki.grid.iu.edu/bin/view/Documentation/CampusFactoryInstall

Condor documents:
   * Condor manual: http://research.cs.wisc.edu/condor/manual/

How to submit Condor jobs:
   * Tutorial: http://research.cs.wisc.edu/condor/tutorials/alliance98/submit/submit.html
   * Condor manual: http://research.cs.wisc.edu/condor/manual/v7.6/2_5Submitting_Job.html

Developers documents:
   * [[TestBoSCO][BOSCO tests for developers and testers]]
   * [[BoscoRoadmap][BOSCO Roadmap (planned and desired features)]]

Here you can check out older releases:
   * [[BoSCOv0][BOSCO version 0]]
   * [[BoSCOv1][BOSCO version 1]]
   * [[BoSCOv1p1][BOSCO version 1.1]]
   * [[BoSCOv1p2][BOSCO version 1.2]]

   * CICiForum130418


