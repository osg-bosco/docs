Bosco Installation
==================


## Introduction 
BOSCO is a job
submission manager designed to help researchers manage large numbers
(\~1000s) of job submissions to the different resources that they can
access on a campus. This is release 1.2 of BOSCO, if you find any
problems or need help installing or running Bosco, please email <bosco-discuss@opensciencegrid.org>.

It offers the following capabilities:

-   Jobs are automatically resubmitted when they fail. The researcher
    does not need to babysit their jobs.
-   Job submissions can be throttled to meet batch scheduler
    settings (e.g. only 10 jobs running concurrently). The researcher
    does not need to make multiple submissions. BOSCO handles that
    for them.
-   BOSCO is designed to be flexible and allows jobs to be submitted to
    multiple clusters, with different job schedulers (e.g. PBS, LSF,
    SGE, HTCondor, SLURM*).

\* SLURM support is via its PBS emulation

The primary advantage for the researcher is that they only need to learn
one job scheduler environment even if the clusters utilize different
native environments.


This document explains how to install, configure and use BOSCO for a
single user. 

## Requirements

* **BOSCO Submit-node**: This is the system that the researcher uses to submit jobs. In
    general it can be the user's laptop, workstation, or it can be
    another system that the user logs into for submitting jobs to
    the cluster. **There can not be any Condor collector running on the
    submit node**, otherwise it will conflict with Bosco.

* **BOSCO resource (aka Cluster) submit-node**: This is the node that you normally login to on the PBS, SGE, LSF,
    SLURM or HTCondor cluster (the BOSCO resource that you'd like
    to add).

    * **PBS flavors supported**: Torque and PBSPro

    * **HTCondor flavors supported**: HTCondor 7.6 or later

    * **SGE flavors supported**: no special requirements (Sun Grid Engine and other Grid Engine
    versions supported)

    * **LSF flavors**: no special requirements

    * **SLURM flavors**: no special requirements

* **BOSCO resource (aka Cluster)**: This is the remote cluster that jobs will execute on (the
    BOSCO resource). The Cluster submit-node is a node belonging to
    this cluster. The nodes where jobs run are referred as worker nodes.
    All the cluster needs:

    * **Shared Filesystem**: The Cluster needs a shared home filesystem (if the Cluster has no shared filesystem only Grid universe jobs can be sent to it)

    * **Network Access**: The worker nodes need to have access to the submit host. The worker nodes
    can be behind a [NAT](https://en.wikipedia.org/wiki/Network_address_translation)
    between the worker nodes and the submit host.

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

* the BOSCO host needs connectivity to the cluster submit nodes of the
    BOSCO resources
* the worker nodes of the BOSCO resources (running the jobs, e.g. the
    nodes in the PBS cluster) must have network connectivity to the jobs
    submit node (the BOSCO host or a different Condor schedd flocking
    into it)
    

### Networking

#### BOSCO submit host

The BOSCO submit host requires no ports open for `universe = grid` jobs.  For 
`universe = vanilla` jobs, port 11000 is required.


!!! note 
    The port 11000 is required
    **ONLY** if "vanilla" jobs are submitted. "vanilla" jobs require also
    that all worker nodes be able to reach the submit host: this means that
    the submit host has to have a public IP address or at least an address
    known by all BOSCO resources that are going to be added. If you need to
    use a port different from 11000, you can edit the BOSCO configuration.


#### BOSCO Resources

The cluster login node requires port 22 from the BOSCO submit host.  BOSCO will
use the SSH protocol to communicate with the login node.

The second requirement is for the BOSCO worker nodes (wherever the jobs
will run) and it is needed **only if "vanilla" jobs are used**.  The worker nodes must
be able to connect to the BOSCO submit host.  Only outgoing from the worker node to the 
BOSCO submit host is required.

## How to Install

### Installation Procedure

1. [Download](https://research.cs.wisc.edu/htcondor/bosco/latest/boscoinstaller) Bosco from
    the bosco website.

1. Run the installer:

        $ cd Downloads   
        $ python boscoinstaller


## How to Use

Now BOSCO is installed. To use it:

1. Setup the environment
1. Add all the desired clusters (at least one)
1. Start BOSCO
1. Submit a test job
1. Ready to submit a real job

### Setup environment before using

Since BOSCO is not installed in the system path. An environment file must be sourced all the times you use BOSCO (start/stop/job submission or query, anything):

    $ source ~/bosco/bosco_setenv

### Starting BOSCO 
BOSCO has some persistent services that must be running. You'll have to start it at the beginning and probably after each reboot of your host.
You should stop BOSCO before an upgrade and possibly before a shutdown of your host.
If you will not use BOSCO anymore, `bosco_uninstall` will remove it from your system.

To start BOSCO:

    $ bosco_start



### Add a cluster to BOSCO

To add a new cluster to the resources you will be using through BOSCO:

1. Setup the environment appropriate for your shell as described in the [Setup environment before using](#setup-environment-before-using) (above).
   
1. For the cluster `mycluster` with user `username` and submit host `mycluster-submit.mydomain.org` (Fully Qualified Domain Name, aka full hostname including the domain name) and Local Resource Management System `LRMS` (valid values are *pbs*, *condor*, *sge* or *lsf*). Replace the parts in red: 

        $ bosco_cluster --add username@mycluster-submit.mydomain.org LRMS

When you add your first cluster, BOSCO will prompt you for a password that will be used to store the SSH keys used by BOSCO to access all your clusters (*Enter password for bosco ssh key:*). Select a random string. It is preferable if you do not use the password you use to access the cluster or to unlock your SSH keys.

Then, if you don't have a ssh key agent with that cluster enabled, you will be prompted for the password that you use to access the cluster you are adding to BOSCO (*Enter password to copy ssh keys to ...*). This may be followed by a confirmation of the RSA key fingerprint, if it is your first ssh connection from this host, where you have to answer *yes*.

!!! important 
    You must be able to login to the remote cluster. If password authentication is OK, the script will ask you for your password. If key only login is allowed, then you must load your key in the *ssh-agent*. Here is an example adding the key and testing the login:

        $ eval `ssh-agent`
        Agent pid 17103;
        $ ssh-add id_rsa_bosco 
        Enter passphrase for id_rsa_bosco: 
        Identity added: id_rsa_bosco (id_rsa_bosco)
        $ ssh  uc3@itbv-ce-pbs
        Last login: Thu Sep 13 13:49:33 2012 from uc3-bosco.mwt2.org
        $ logout

 
!!! important
    Some clusters have multiple login nodes behind a round robin DNS server.
    You can recognize them because when you login to the node
    (e,g: ssh login.mydomain.org), it will show a name different form the one
    used to connect (e.g.: **hostname -f will return **login2.mydomain.org**).
    
    If this happens you must add the BOSCO resources by using a name of the
    host, not the DNS alias (e.g. **bosco_cluster --add login2.mydomain.org**).
    This is because sometime these multiple login nodes do not share all the
    directories and BOSCO may be unable to find its files if different
    connections land on different hosts.
   
    Note how **midway-login2.rcc.uchicago.edu** is use instead of **midway.rcc.uchicago.edu**:


        $ ssh  mm@midway.rcc.uchicago.edu
        mm@midway.rcc.uchicago.edu's password: 
        =========================================================================
                                       Welcome to Midway
                                   Research Computing Center
                                     University of Chicago
        ...
        =========================================================================
        
        $ hostname -f
        midway-login2.rcc.uchicago.edu
        $ logout
        Connection to midway.rcc.uchicago.edu closed.
        $ bosco_cluster --add mm@midway-login2.rcc.uchicago.edu
        Warning: No batch system specified, defaulting to PBS
        If this is incorrect, rerun the command with the batch system specified

        Enter the password to copy the ssh keys to mm@midway-login2.rcc.uchicago.edu:
        mm@midway-login2.rcc.uchicago.edu's password: 
        Detecting PBS cluster configuration...bash: cannot set terminal process group (-1): Invalid argument
        bash: no job control in this shell
        bash: qmgr: command not found
        Done!
        Downloading for mm@midway-login2.rcc.uchicago.edu....
        Unpacking.........
        Sending libraries to mm@midway-login2.rcc.uchicago.edu...
        Creating BOSCO for the WN's....................................................................
        Installation complete
        The cluster mm@midway-login2.rcc.uchicago.edu has been added to BOSCO
        It is available to run jobs submitted with the following values:
        > universe = grid
        > grid_resource = batch pbs mm@midway-login2.rcc.uchicago.edu

When adding the cluster, if the last message is **Done!**. Your cluster has been added successfully.

You can see a list of the current clusters in BOSCO by typing:

    $ bosco_cluster --list


### Submitting a test job
You can send a simple test job to verify that the cluster added is working correctly.

To send a BOSCO test job to the host *username@mycluster-submit.mydomain* (name as listed in the output of `bosco_cluster --list`):

1. Setup the environment appropriate for your shell as described in the [Setup environment before using](#setup-environment-before-using) section (above).

1. For the cluster *username@mycluster-submit.mydomain* (identical to output of  *bosco_cluster --list*). Replace the username and cluster: 
   
        $ bosco_cluster --test username@mycluster-submit.mydomain

  For example: 

        $ bosco_cluster -t dweitzel@ff-grid.unl.edu
        dweitzel@ff-grid.unl.edu
        Testing ssh to dweitzel@ff-grid.unl.edu...Passed!
        Testing bosco submission...Passed!
        Checking for submission to remote pbs cluster (could take ~30 seconds)...Passed!
        Submission files for these jobs are in /home/dweitzel/bosco/local.localhocentos56/bosco-test
        Execution on the remote cluster could take a while...Exiting

### How to Stop and Remove
To stop BOSCO:
    
    $ bosco_stop


To uninstall BOSCO:

1. If you want to remove remote clusters get the list and remove them one by one:

        $ bosco_cluster --list
        
    For each remote cluster:

        $ bosco_cluster -r user@cluster_as_spelled_in_list

2. Remove the installation directory:  

        $ bosco_uninstall

!!! note
    Uninstalling BOSCO removes the software but leaves the files in your `.bosco` and `.ssh` directories with all the information about the added clusters and the SSH keys. Files installed on the remote clusters are not removed either. 

### How to Update BOSCO
If you want to update BOSCO to a new version you have to:

   1. setup BOSCO:

        $ source ~/bosco/bosco_setenv

   1. stop BOSCO:

        $ bosco_stop

   1. remove the old BOSCO:

        $ bosco_uninstall

   1. download and install the new BOSCO (see install section above)  and re-add all the clusters in your setup:
   1. for each installed cluster (the list is returned by *bosco_cluster --list*):
      1. remove the cluster:

            $ bosco_cluster --remove username@mycluster-submit.mydomain.org

      1. add the cluster:

            $ bosco_cluster --add username@mycluster-submit.mydomain.org queue

   1. start BOSCO:

        $ bosco_start

This will update the local installation and the software on the remote clusters

### Job submission example

You can submit a regular Condor vanilla job to BOSCO. The Campus Factory, a component within BOSCO, will take care to run it on the different clusters that you added and to transfer the input and output files as needed.
Here is a simple example. The Condor team provides [many great tutorials](http://research.cs.wisc.edu/condor/tutorials/) to learn more.

### Configuring Executable

Your may wrap your job in a script (e.g. using your favorite shell or Python) so that you can invoke more executables and do some tests

This is a simple bash script, called *myjob.sh*:

    #!/bin/bash
    # Prepare for the execution
    # Run the actual applications
    hostname 
    date 
    id 
    whoami 
    # Final steps

### Example Submission File

With BOSCO you can do direct submission to the cluster, using the grid universe, or use the the glideins so that regular (vanilla) HTCondor jobs can be used.
There is a small difference between the 2 options is in the submit file (see below) and vanilla have some additional
[Firewall and Network requirements](#networking) because they use glideins.
All the other steps, job file creation, job submission and checking the jobs, are the same.

*Use one or the other*

#### Direct Job submission example

Here is an example submission file for direct submission.  Copy it to a file, *example.condor*:

    universe = grid
    grid_resource = batch %RED%pbs%ENDCOLOR% marco@uc3-pbs.uchicago.edu
    Executable     = myjob.sh
    arguments = 
    output = myjob.out
    error = myjob.error
    log = myjob.log
    transfer_output_files = 
    should_transfer_files = YES
    when_to_transfer_output = ON_EXIT
    queue 1

The type of cluster that you are submitting to, pbs, lsf, sge, or condor, must be supplied on the grid_resource line.



### Job Submission
Submit the job file *example.condor* with the *condor_submit* command:

    $ condor_submit example.condor

### Job Monitoring

Monitor the job with *condor_q*.  For example, the job when idle is:

    $ condor_q
    -- Submitter: uc3-c001.mwt2.org : <10.1.3.101:45876> : uc3-c001.mwt2.org
     ID      OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD               
      12.0   marco           7/6  13:45   0+00:00:00 I  0   0.0  short2.sh 10      
      13.0   marco           7/6  13:45   0+00:00:00 I  0   0.0  short2.sh 10      
      14.0   marco           7/6  13:46   0+00:00:00 I  0   0.0  glidein_wrapper.sh
      15.0   marco           7/6  13:46   0+00:00:00 I  0   0.0  glidein_wrapper.sh
      16.0   marco           7/6  13:46   0+00:00:00 I  0   0.0  glidein_wrapper.sh
      17.0   marco           7/6  13:46   0+00:00:00 I  0   0.0  glidein_wrapper.sh
      18.0   marco           7/6  13:46   0+00:00:00 I  0   0.0  glidein_wrapper.sh
      19.0   marco           7/6  13:46   0+00:00:00 I  0   0.0  glidein_wrapper.sh

    8 jobs; 0 completed, 0 removed, 8 idle, 0 running, 0 held, 0 suspended


!!! note
    That condor\_q will show also the glidein jobs. Auxiliary jobs that BOSCO is using to run your actual job. Like in the example above, job 11 was the one submitted.


The job could be idle if it is currently idle at the remote cluster.  When the job is being executed on the remote cluster, the *ST* (State) will change to *R*, and the *RUN_TIME* will grow.

Another method of monitoring a job is to check the job's *log*, a human readable (for the most part) time line of actions relating to the job.  The *logfile* was specified in the submission script, for example *logfile* in the example above.  You can view the log file by using *cat*:

    $ cat logfile

### Job output
Once the job completes BOSCO will transfer back standard output, standard error and the output files (if specified in the submit file), e.g. the job above will create stdout and stderr files (unique for each submission) and a file *myout-file-10* in the directory where the *condor_submit* command was executed.


## Command summary

User commands:

| *Action*	| *Arguments*	| *Implicit Input* | *Output* |
|-----------|---------------|------------------|----------|
| condor_* | Various | Various | Various see the [Condor manual](http://research.cs.wisc.edu/condor/manual/)| 

There are many Condor commands. The most common user commands are *condor_q*, *condor_submit*, *condor_rm* and *condor_status* .


Administration commands:

| *Action*	| *Arguments*	| *Implicit Input* | *Output* |
------------|---------------|------------------|----------|
| bosco_install | | | Success/Failure |
| source bosco.\[csh,sh\] | | | |
| bosco_start | | | Success/Failure |
| bosco_stop | | | Success/Failure |
| bosco_uninstall | | | Success/Failure |
| bosco_cluster | --add user@Hostname queue | | Success/Fail, entry in head node table |
| | --list | Head-node table | List of add head nodes and their status |
| | --test Hostname | Submit file | Status of submitted jobs |
| | --remove Hostname | | Success/Fail, head node table with Hostname removed, delete if empty |
| condor_* | Various | Various | Various see the [Condor manual](http://research.cs.wisc.edu/condor/manual/)| | 

Manually transfer output data from batch system.


## Multi-Cluster Bosco

In order to use Multi-Cluster Bosco, you must make 1 configuration change.  The multi-cluster also requires a public IP address.

### Changing the Bosco Configuration for Multi-Cluster

BOSCO by default is using the loopback IP address.  You must change the configuration to listen on the public interface.  You can do this by editing the configuration file *$HOME/bosco/local.bosco/config/condor_config.factory*, adding anywhere the line:

    NETWORK_INTERFACE = 

By setting this, you are enabling Bosco's smart interface detection which will automatically choose and listen on the public interface.

### Glidein Job submission example

You can submit a regular HTCondor vanilla job to BOSCO. The Campus Factory, a component within BOSCO, will take care to run it on the different clusters that you added and to transfer the input and output files as needed.
Here follow a simple example. The Condor team provides [many great tutorials](http://research.cs.wisc.edu/condor/tutorials/) to learn more.

Here is an example of  a vanilla submission file (using glideins).  Copy it to a file, *example.condor*

    universe = vanilla
    Executable     = myjob.sh
    arguments = $(Cluster) $(Process)
    output = cfjob.out.$(Cluster)-$(Process)
    error = cfjob.err.$(Cluster)-$(Process)
    log = cfjob.log.$(Cluster)
    should_transfer_files = YES
    when_to_transfer_output = ON_EXIT
    queue 1

!!! note
    The BOSCO submit host needs to satisfy these additional [Firewall and Network requirements](#networking) to be able to submit and run vanilla jobs. Those requirement include being reachable by all BOSCO resources. 


## Advanced use

### Changing the BOSCO port

BOSCO is using the HTCondor [Shared port daemon](https://htcondor.readthedocs.io/en/latest/admin-manual/networking.html#reducing-port-usage-with-the-condor-shared-port-daemon).
This means that all the communication are coming to the same port, by default 11000. If that port is taken (already bound) you can change the port used by BOSCO by editing the file *$HOME/bosco/local.bosco/config/condor_config.factory*.
You can change the port passed to the shared port daemon:

    # Enabled Shared Port
    USE_SHARED_PORT = True
    SHARED_PORT_ARGS = -p 11000

!!! note
    You need to restart BOSCO after you change the configuration (*bosco_stop; bosco_start*).

If you are referring to this BOSCO pool (e.g. for flocking) you'll need to use a string like: *your_host.domain:11000?sock=collector* .
Replace host and port with the correct ones.

### Multi homed hosts

Multi homed hosts are hosts with multiple Network Interfaces (aka dual-homed when they have 2 NICs).
BOSCO configuration is tricky on multi-homed hosts. BOSCO requires the submit host to be able to connect back to the BOSCO host, so it must advertise an interface that is reachable from all the chosen submit hosts. E.g. a host with a NIC on a private network and one with a public IP address must advertise the public address if the submit hosts are outside of the private network. 
In order to do that you have to:

   - make sure that the name returned by the command `/bin/hostname -f` is the name resolving in the public address (e.g. `host hostname -f`= should return the public address). If not you should change it.

   - edit `~/bosco/local.<HOST>/condor_config.local` (`<HOST>` is the short host name) and add a line like `NETWORK_INTERFACE = xxx.xxx.xxx.xxx` ,
substituting `xxx.xxx.xxx.xxx` with the public IP address. This will tell BOSCO to use that address.


### Modifying maximum number of submitted jobs to a resource

Many clusters limit the number of jobs that can be submitted to the scheduler.  For PBS, we are able to detect this limit.  For SGE and LSF, we are not able to detect this limit.  In the cases where we cannot find the limit, we set the maximum number of jobs very conservatively, to a maximum of 10.  This includes both the number of idle and running jobs to the cluster.

The limit is specified in the condor config file `~/bosco/local.bosco/condor_config.local`, at the bottom.  Edit the value of the configuration variable `GRIDMANAGER_MAX_SUBMITTED_JOBS_PER_RESOURCE`

    GRIDMANAGER_MAX_SUBMITTED_JOBS_PER_RESOURCE = 10

### Custom submit properties
Bosco has the ability to add custom submit properties to every job submitted to a cluster.
On the cluster's login node (the BOSCO resource, the host you used at the end of the line when typing the `bosco_cluster --add` command),
create the file in one of the custom locations:

**Custom Script Locations:**

   - *PBS/SLURM* - `~/bosco/glite/bin/pbs_local_submit_attributes.sh`
   - *Condor* - `~/bosco/glite/bin/condor_local_submit_attributes.sh`
   - *SGE* (and other GE) - `~/bosco/glite/bin/sge_local_submit_attributes.sh`
   - *LSF* - `~/bosco/glite/bin/lsf_local_submit_attributes.sh`

!!! note
     This file is executed and the output is inserted into the submit script. I.e. It is not cat, use echo/cat statements in the script.

Below is an example `pbs_local_submit_attributes.sh` script which will cause every job submitted to this cluster through Bosco to request 1 node with 8 cores:

    #!/bin/sh
    echo "#PBS -l nodes=1:ppn=8"

#### Passing parameters to the custom submit properties.

You may also pass parameters to the custom scripts by adding a special parameter to the Bosco submit script.
For example, in your Bosco submit script, add:

    +remote_cerequirements = NumJobs == 100
    ...
    queue

After you submit this job to Bosco, it will execute the [custom scripts](#custom-script-locations) with,
in this example, *NumJobs* set in the environment equal to *100*.
The custom script can take advantage of these values.  For example, a PBS script can use the *$NumJobs*:

    #!/bin/sh
    echo "#PBS -l select=$NumJobs"

This will set the number of requested cores from PBS to *$NumJobs* specified in the original Bosco Submit file.

### Flocking to a BOSCO installation

In some special cases you may desire to flock to your BOSCO installation. If you don't know what I'm talking about, then skip this section.

In order to enable flocking you must use an IP so that all the hosts you are flocking from can communicate with the BOSCO host.
Then you must setup `FLOCK_FROM` and the security configuration so that the communications are authorized.

BOSCO has strong security settings. Here are two examples:

1. Using GSI authentication (a strong authentication method) you must provide and install X509 certificates,
    you must change the **configuration file** (see example below) and define or update the **condor_mapfile**(see example below)
    (e.g. `$HOME/bosco/local.bosco/certs/condor_mapfile`)

    **configuration file:**

        # Networking - If you did not already, remember that you need to set BOSCO not to use the loopback port
        #
        NETWORK_INTERFACE =
        
        #
        # Hosts definition
        #
        # BOSCO host
        H_BOSCO = %RED%bosco.mydomain.edu%ENDCOLOR%
        H_BOSCO_DN = %RED%/DC=com/DC=DigiCert-Grid/O=Open Science Grid/OU=Services/CN=bosco.mydomain.edu%ENDCOLOR%
        
        # submit host (flocking to BOSCO host)
        H_SUB = %RED%sub.mydomain.edu%ENDCOLOR%
        H_SUB_DN = %RED%/DC=com/DC=DigiCert-Grid/O=Open Science Grid/OU=Services/CN=sub.mydomain.edu%ENDCOLOR%
        
        #
        # Flocking configuration
        # 
        FLOCK_FROM = $(FLOCK_FROM) $(H_SUB)
        
        #
        # Security definitions
        # 
        # Assuming system-wide installed CA certificates 
        GSI_DAEMON_DIRECTORY = /etc/grid-security
        # This host's certificates
        GSI_DAEMON_CERT = /etc/grid-security/hostcert.pem
        GSI_DAEMON_KEY = /etc/grid-security/hostkey.pem
        # default GSI_DAEMON_TRUSTED_CA_DIR = $(GSI_DAEMON_DIRECTORY)/certificates
        CERTIFICATE_MAPFILE= $HOME/bosco/local.bosco/certs/condor_mapfile
        
        # Not used
        MY_DN = $(H_BOSCO_DN)
        
        # Who to trust?  Include the submitters flocking here
        GSI_DAEMON_NAME = $(GSI_DAEMON_NAME), $(H_BOSCO_DN), $(H_SUB_DN)
        
        # Enable authentication from the Negotiator
        SEC_ENABLE_MATCH_PASSWORD_AUTHENTICATION = TRUE
        
        # Enable gsi authentication, and claimtobe (for campus factories)
        # The default (unix) should be: FS, KERBEROS, GSI
        SEC_DEFAULT_AUTHENTICATION_METHODS = FS,GSI, PASSWORD, $(SEC_DEFAULT_AUTHENTICATION_METHODS)
        SEC_CLIENT_AUTHENTICATION_METHODS = FS, PASSWORD, GSI, CLAIMTOBE
        SEC_DAEMON_AUTHENTICATION_METHODS = FS, PASSWORD, GSI, CLAIMTOBE
        SEC_WRITE_AUTHENTICATION_METHODS = FS, PASSWORD, GSI, CLAIMTOBE
        SEC_ADVERTISE_SCHEDD_METHODS = FS, PASSWORD, GSI, CLAIMTOBE
        
        ALLOW_DAEMON = $(ALLOW_DAEMON) condor_pool@*/* %RED%boscouser%ENDCOLOR%@*/* $(FULL_HOSTNAME) $(IP_ADDRESS)
        ALLOW_ADVERTISE_SCHEDD = %RED%boscouser%ENDCOLOR%@*/*

    **condor-mapfile:**

        GSI "^%RED%\/DC\=com\/DC\=DigiCert\-Grid\/O\=Open\ Science\ Grid\/OU\=Services\/CN\=sub\.mydomain\.edu%ENDCOLOR%$" %RED%boscouser@sub.mydomain.edu%ENDCOLOR%
        GSI "^%RED%\/DC\=com\/DC\=DigiCert\-Grid\/O\=Open\ Science\ Grid\/OU\=Services\/CN\=bosco\.mydomain\.edu%ENDCOLOR%$" %RED%boscouser%ENDCOLOR%
        # #
        SSL (.*) ssl@unmapped
        CLAIMTOBE (.*) \1
        PASSWORD (.*) \1
        # #
        GSI (.*) anonymous
        FS (.*) \1

    Remember to enable and configure GSI authentication also on the host you are flocking form.<br><br> 


1. Relaxing BOSCO security setting to allow CLAIMTOBE authentication. This is not very secure. Use it only if you can trust all the machines
   on the network and remember to enable CLAIMTOBE also on the host you are flocking from, see a **configuration file** example below: 

    **configuration file:**

        # Networking - If you did not already, remember that you need to set BOSCO not to use the loopback port
        #
        NETWORK_INTERFACE =
        
        #
        # Flocking configuration
        # 
        FLOCK_FROM = %RED%host_from.domain%ENDCOLOR%
        
        #
        # Security definitions overrides
        # 
        SEC_DEFAULT_ENCRYPTION = OPTIONAL
        SEC_DEFAULT_INTEGRITY = PREFERRED
        # To allow status read
        SEC_READ_INTEGRITY = OPTIONAL
        
        SEC_CLIENT_AUTHENTICATION_METHODS = FS, PASSWORD, CLAIMTOBE
        
        ALLOW_ADVERTISE_SCHEDD = */%RED%IP_of_the_host_in_flock_from%ENDCOLOR% $(FULL_HOSTNAME) $(IP_ADDRESS) $(ALLOW_DAEMON)
        
        SEC_DAEMON_AUTHENTICATION = PREFERRED
        SEC_DAEMON_INTEGRITY = PREFERRED
        SEC_DAEMON_AUTHENTICATION_METHODS = FS,PASSWORD,CLAIMTOBE
        SEC_WRITE_AUTHENTICATION_METHODS = FS,PASSWORD,CLAIMTOBE

After copying from the examples or editing your configuration file, save it as `$HOME/bosco/local.bosco/config/zzz_condor_config.flocking`. 
Other names are OK as long as its definition override the default ones of BOSCO (check with `condor_config_val -config`).

Then stop and restart BOSCO.

## Troubleshooting

### Useful Configuration and Log Files

BOSCO underneath is using Condor. You can find all the Condor log files in `~/bosco/local.HOSTNAME/log`


### Make sure that you can connect to the BOSCO host

If you see errors like:

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

Please try manually to ssh from the BOSCO host to the cluster submit node. The ability to connect is required in order to install BOSCO.

### Make sure that BOSCO is running

BOSCO may not survive after you log out, for example if the BOSCO node was restarted while you where logged out. 
When you log back in after sourcing the setup as described in the [setup environment section](#setup-environment-before-using), 
you should start BOSCO as described in the [BOSCO start section](#starting-bosco), specially if the command `condor_q` is failing.

### Errors due to leftover files

Bosco files on the submit host are in:

   - `~/bosco/` - the release directory
   - `~/.bosco/` - some service files
   - `~/.ssh/` - the ssh key used by BOSCO

If you used `bosco_uninstall` it will remove all BOSCO related files. If you removed BOSCO by hand you must pay attention.
If the service key is still in `.ssh` but the other files are missing, during the execution of BOSCO commands you will get some unclear errors like:
 
 - `_"IOError: [Errno 2] No such file or directory: '/home/marco/.bosco/.pass'"`  or 
 -  `"OSError: [Errno 5] Input/output error"` , all followed by:

`Password-less ssh to marco@itb2.uchicago.edu did NOT work, even after adding the ssh-keys.
Does the remote resource allow password-less ssh?`

If that happens you can remove the service files and the keys using:

    $ rm -r ~/.bosco
    $ rm ~/.ssh/bosco_key.rsa

and then re-add all the clusters with:

    $ bosco_cluster --add

### Unable to download and prepare BOSCO for remote installation. 

BOSCO can return this error:

1. Because the BOSCO submit host is unable to download BOSCO for the resource installation, e.g. a firewall is blocking the download or the server is down
1. More commonly because there are problems with the login host of the BOSCO resource, e.g. the disk is full or there are multiple login nodes

You can check 1 by downloading BOSCO on your BOSCO submit host.
To check 2 you have to login on the BOSCO resource: `df` will tell you if some disks are full, with `hostname -f` you can check if the name
 is different form the one that you used to login with ssh. If the name differs probably you are using a cluster with multiple login nodes and
 you must use only one for BOSCO. Se the second **note** in the [section to add a cluster to BOSCO](#add-a-cluster-to-bosco) (above).

If you see errors similar to the one below while executing `bosco_cluster --add`:

    Downloading for USER@RESOURCE
    Unpacking.tar: Cannot save working directory 
    tar: Error is not recoverable: exiting now 
    ls: /tmp/tmp.qeIJ9139/condor*: No such file or directory 
    Unable to download and prepare BOSCO for remote installation. 

then you are using most likely the generic name of a multi-login cluster and you should use the name of one of the nodes
 as suggested in the [note above](#add-a-cluster-to-bosco). 


## Get Help/Support

To get assistance you can send an email to bosco-discuss@opensciencegrid.org


## References 

[BoSCO Web site](http://bosco.opensciencegrid.org/) and documents about the latest production release (v1.2)

 - [Using Bosco](#how-to-use)
 - [Installing BoSCO](BoscoInstall)
 - [Installing BoSCO Multi User](BoscoMultiUser)

Condor documents:

 -  Condor manual: http://research.cs.wisc.edu/condor/manual/

How to submit Condor jobs:

 -  Tutorial: http://research.cs.wisc.edu/condor/tutorials/alliance98/submit/submit.html
 -  Condor manual: http://research.cs.wisc.edu/condor/manual/v7.6/2_5Submitting_Job.html

