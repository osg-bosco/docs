%META:TOPICINFO{author="KyleGross" date="1476284786" format="1.1"
version="1.5"}% %META:TOPICPARENT{name="BoscoInstall"}%

# Bosco + SkeletonKey for High Throughput R Applications

Here is an example using BOSCO and SkeletonKey with the OASIS software
service to run distributed high-throughput R-applications on campus grid
environments.

  - First we'll be installing BOSCO as shown in
    Trash/CampusGrids.BoscoAHM13
  - Then we'll install SkeletonKey and use it to run R as seen in
    [Trash/CampusGrids.Quickstart](Trash/CampusGrids.Quickstart) and
    [Trash/CampusGrids.SoftwareAndDataAccess](Trash/CampusGrids.SoftwareAndDataAccess).

# Getting Started

You will need login in your host, be able to share a `public_html`
directory and have login access to a remote cluster.

You will need also access to Web proxy (e.g. a squid proxy server). The
closer this is to your cluster, the more efficient will be your jobs.
Several campuses provide one (check with your network administrators).
If you want to install one OSG provides a package and
[instructions](Documentation/Release3.InstallFrontierSquid). For this
tutorial you can also use the OSG ITB proxy server, even if it will not
be very efficient if you are far form Chicago\!

In this tutorial I will be using `bash` shell. If `echo $SHELL` returns
something different from `/bin/bash` then run `/bin/bash` to start a
Bash session. This is a very abbreviated install document for Bosco. For
the full install document, view [Bosco Installer](BoscoInstall). And for
more information on data transfer see SkeletonKey.

<span class="twiki-macro NOTE"></span> You will need to install Bosco
and SkeletonKey on a RedHat (Or CentOS or Scientific Linux) computer. It
must also not have HTCondor already running.

# Let's start with Bosco

## Download & Install Bosco

 <a href="http://bosco.opensciencegrid.org/download/">
     <img src="https://raw.github.com/osg-bosco/bosco-download-images/master/images/download-orange.png" 
     alt="Bosco Download"
     style="border-width: 0;"/>
 </a>

Visit the Bosco [download](http://bosco.opensciencegrid.org/download/)
page. Choose the Multi-Platform Installer. After downloading the
installer, from the terminal, untar it and run the installer as a
regular user:

``` screen
%UCL_PROMPT% tar xzf boscoinstaller.tar.gz
%UCL_PROMPT% python boscoinstaller
```

## Starting Bosco & adding your first cluster

First you will need to setup your environment to have Bosco installed:

``` screen
%UCL_PROMPT% source ~/bosco/bosco_setenv
```

Start Bosco:

``` screen
%UCL_PROMPT% bosco_start
```

Add your first cluster. You will need your username and password from
the sheet.

``` screen
%UCL_PROMPT% bosco_cluster --add demo%RED%XX%ENDCOLOR%@boscopbs.opensciencegrid.org pbs
```

## Test the new Cluster

In order to confirm everything is working with the remote cluster, you
may want to test the Bosco cluster.

``` screen
%UCL_PROMPT% bosco_cluster -t demo%RED%XX%ENDCOLOR%@boscopbs.opensciencegrid.org
```

# Now setup SkeletonKey

## Download and Install

SkeletonKey uses a python script to install and set things up for the
user. The installation procedure is as outlined below:

1.  First download the SkeletonKey installer script \<pre
    class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> wget
uc3-data.uchicago.edu/sk/install-skeletonkey.py \</pre\>

1.  Pick a directory to install the CCTools and SkeletonKey binaries in
    (e.g. `bin` in your home directory). Ideally this directory should
    be in `$PATH`. \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> mkdir \~/bin \</pre\>

1.  Pick a directory to export from Chirp (for now the tutorial will use
    /tmp/%RED%your\_user<span class="twiki-macro ENDCOLOR"></span> where
    your\_user is your username)
2.  Run the installer, specifying the directory to install and the
    directory to export from Chirp: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> python
install-skeletonkey.py -b \~/bin -e
/tmp/%RED%your\_user<span class="twiki-macro ENDCOLOR"></span> \</pre\>

1.  Add the directory specified in `-b` option (e.g. `~/bin`) to
    `$PATH`: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> export PATH=$PATH:\~/bin
\</pre\>

1.  Edit `~/.profile` and append the following line: \<pre
    class="file"\>export PATH=$PATH:\~/bin\</pre\>

## Setup and start Chirp

Add `chirp` in your path:\<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> cd bin/
<span class="twiki-macro UCL_PROMPT"></span> ln -s
cctools-3.7.1-x86\_64-redhat5/bin/chirp chirp \</pre\>

Create your data directories (the root directory has to be the same that
you used in the -e option during the SkeletonKey installation above):
\<pre class="screen"\> <span class="twiki-macro UCL_PROMPT"></span>
mkdir
/tmp/%RED%your\_user\_name<span class="twiki-macro ENDCOLOR"></span>
<span class="twiki-macro UCL_PROMPT"></span> cd
/tmp/%RED%your\_user\_name<span class="twiki-macro ENDCOLOR"></span>
<span class="twiki-macro UCL_PROMPT"></span> mkdir data output
<span class="twiki-macro UCL_PROMPT"></span> cd \</pre\>

Start the chirp server: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> chirp\_control start
\</pre\>

## Test SkeletonKey

Here is a test that will just go through the mechanics of running
skeleton key and generating a job wrapper.

### Setting up binaries

In order for the job wrapper that SkeletonKey provides to work
correctly, you'll need to make the cctools binaries available on a
webserver. The SkeletonKey installer created a file called
`parrot.tar.gz` in the `~/bin` that you'll need to copy to your
webserver and make it available over http: \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> cp \~/bin/parrot.tar.gz
%RED%\~/public\_html<span class="twiki-macro ENDCOLOR"></span>
<span class="twiki-macro UCL_PROMPT"></span> chmod 644
%RED%\~/public\_html/parrot.tar.gz<span class="twiki-macro ENDCOLOR"></span>
\</pre\>

### Creating the job wrapper

You'll need to do the following on the machine where you installed
SkeletonKey

1.  Open a file called `sk_test.ini` and add the following lines: \<pre
    class="file"\>

\[Parrot\] location =
%RED%http://your.host/\~your\_user/parrot.tar.gz<span class="twiki-macro ENDCOLOR"></span>

\[Application\] script = /bin/hostname \</pre\>

1.  In `sk_test.ini`, change the url
    `http://your.host/~your_name/parrot.tar.gz` to point to the url of
    the parrot tarball that you copied previously.
2.  Run SkeletonKey on `sk_test.ini`: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> skeleton\_key -c
sk\_test.ini \</pre\> 1. Finally, run the job wrapper to verify that
it's working correctly \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> sh ./job\_script.sh
--2013-04-18 12:48:54--
<http://uc3-test.uchicago.edu/~testu1/parrot.tar.gz> Resolving
uc3-test.uchicago.edu... 128.135.158.156 Connecting to
uc3-test.uchicago.edu|128.135.158.156|:80... connected. HTTP request
sent, awaiting response... 200 OK Length: 10488915 (10M)
\[application/x-gzip\] Saving to: \`parrot.tar.gz'

100%\[=====================================================================================================\>\]
10,488,915 --.-K/s in 0.02s

2013-04-18 12:48:54 (569 MB/s) - \`parrot.tar.gz' saved
\[10488915/10488915\]

uc3-test.uchicago.edu \</pre\>

In the ini file used by SkeletonKey, two sections are used. The
SkeletonKey used the `location` setting in the `Parrot` section to
determine where it can download Parrot binaries to use when running user
applications. In the `Application` section, the `script` setting
indicates the command to run in the Parrot environment.

# Now submit the R jobs

The next example will create a job that will read and write from a
filesystem exported by Chirp using an application that's available using
OASIS (or any other CVMFS repository). The specific example runs a R
script elaborating a raster image but you can easily change it.

OASIS is the OSG Application Software Installation Service. For more
information on OASIS see ReleaseDocumentation.OasisUpdateMethod and to
install software in OASIS (you need to be a VO software manager) contact
<support@opensciencegrid.org>

R has been installed in the OSG VO space on OASIS and is available at
the following paths:

  - RHEL5 64bit: `sw/R/rhel5/x86_64/current/`
  - RHEL6 64bit: `sw/R/rhel6/x86_64/current/`

<span class="twiki-macro NOTE"></span> Before you start, please make
sure that Chirp is installed and exporting a directory (this tutorial
will assume that Chirp is exporting
=/tmp/%RED%your\_user\_name<span class="twiki-macro ENDCOLOR"></span>)

## Creating the application tarball

Since we'll be running an application from OASIS, we'll include in the
application tarball a script to do some initial setup and then invoke
the actual application

1.  Create a directory for the script \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> mkdir /tmp/rjob\_test
\</pre\>

1.  Create a R script `/tmp/rjob_test/test.R` with the following lines:
    \<pre class="file"\>

\#/usr/bin/Rscript --vanilla

library( raster) args \<- commandArgs(TRUE) grbFile \<- args\[1\]
scanHowMany \<- args\[2\] output \<- args\[3\] grb \<- brick( grbFile)

for( n in 1:scanHowMany) { r \<- subset( grb, n) cat( paste( names( r),
cellStats( r, "sum"), sep= " "), "\\n", file=output) } \</pre\>

1.  Create a shell script , `/tmp/rjob_test/myapp.sh` setting up the
    environment and then running R (as visible above `test.R` requires 3
    arguments: a raster file, `data.grb`, the number of iterations,
    `100`, and the output file): \<pre class="screen"\>

ROOT\_DIR=/cvmfs/uc3.uchicago.edu/sw export
LD\_LIBRARY\_PATH=$LD\_LIBRARY\_PATH:$ROOT\_DIR/lib
$ROOT\_DIR/bin/Rscript ./rjob\_test/test.R ./rjob\_test/data.grb 100
$CHIRP\_MOUNT/output/$1 echo "Finishing script at: " echo \`date\`
\</pre\>

1.  Next, make sure the `myapp.sh` script is executable and create a
    tarball: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> chmod 755
/tmp/rjob\_test/myapp.sh <span class="twiki-macro UCL_PROMPT"></span> cd
/tmp <span class="twiki-macro UCL_PROMPT"></span> tar cvzf
rjob\_test.tar.gz rjob\_test \</pre\> 1. Then copy the tarball to your
webserver \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> cd /tmp
<span class="twiki-macro UCL_PROMPT"></span> cp rjob\_test.tar.gz
%RED%\~/public\_html/<span class="twiki-macro ENDCOLOR"></span>
<span class="twiki-macro UCL_PROMPT"></span> chmod 644
%RED%\~/public\_html/rjob\_test.tar.gz
<span class="twiki-macro ENDCOLOR"></span> \</pre\> 1. Finally, copy or
download the CVMFS repository key and make this available on your Web
server. If it is already available on a public server that you trust,
you can use directly that URL

  - For OASIS the key is at
    `http://uc3-test.uchicago.edu/~testu1/oasis.opensciencegrid.org.pub`:
    \<pre clas=file\>

\-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAqQGYXTp9cRcMbGeDoijB
gKNTCEpIWB7XcqIHVXJjfxEkycQXMyZkB7O0CvV3UmmY2K7CQqTnd9ddcApn7BqQ
/7QGP0H1jfXLfqVdwnhyjIHxmV2x8GIHRHFA0wE+DadQwoi1G0k0SNxOVS5qbdeV
yiyKsoU4JSqy5l2tK3K/RJE4htSruPCrRCK3xcN5nBeZK5gZd+/ufPIG+hd78kjQ
Dy3YQXwmEPm7kAZwIsEbMa0PNkp85IDkdR1GpvRvDMCRmUaRHrQUPBwPIjs0akL+
qoTxJs9k6quV0g3Wd8z65s/k5mEZ+AnHHI0+0CL3y80wnuLSBYmw05YBtKyoa1Fb
FQIDAQAB -----END PUBLIC KEY----- \</pre\>

  - For UC3's CVMFS the key is
    `http://uc3-data.uchicago.edu/uc3.key`:\<pre calss=file\>

\-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA3tsg79ghhbxquF3m7oQ3
A7D77TfafuU2qK5SfW/HeERmBSfWdTNagygNhUjK1rROCmqekz3lnn25hma2Qodz
9W3oqbQHRdCT/MTPLpcTl/n12fCtjMDHPfclnc39gu6uPGkRU21DHCusPaznMtGL
hvwa3qSsi646UTaqKvD0dsUFVnUbVaG+XTi5jSQMHRTaGy1JCZBpVDMrMIgZzwDp
9TLy1/5VEejBtYBt2rpV09IieurmA2T4Wsa+7zPUazPx2g+xsMyQ3fCu1fP7oszx
JVbEnNXWhtuZ4R/1DrebXtojtrj6oc2bGlN92UDdthtC1/gE80Kc8tONfQt4P1Ea
KQIDAQAB -----END PUBLIC KEY----- \</pre\>

One thing to note here is that Parrot makes mounted CVMFS repositories,
like OASIS, available under `/cvmfs/repository_name` where
repository\_name is replaced by the name that the repository is
published under (see the configuration file `rjob_test.ini` below).

### Creating a job wrapper

You'll need to do the following on the machine where you installed
SkeletonKey

1.  Open a file called `rjob_test.ini` and add the following lines:
    \<pre class="file"\>

\[CVMFS\] repo1 = uc3.uchicago.edu repo1\_options =
url=http://uc3-cvmfs.uchicago.edu/opt/uc3/,pubkey=%RED%http://repository\_key\_url<span class="twiki-macro ENDCOLOR"></span>,quota\_limit=1000,proxies=%RED%squid-proxy:3128<span class="twiki-macro ENDCOLOR"></span>
repo1\_key =
%RED%http://repository\_key\_url<span class="twiki-macro ENDCOLOR"></span>
repo2 = oasis.opensciencegrid.org repo2\_options =
url=http://oasis-replica.opensciencegrid.org/cvmfs/oasis/,pubkey=%RED%http://repository\_key\_url<span class="twiki-macro ENDCOLOR"></span>,quota\_limit=1000,proxies=%RED%squid-proxy:3128<span class="twiki-macro ENDCOLOR"></span>
repo2\_key =
%RED%http://repository\_key\_url<span class="twiki-macro ENDCOLOR"></span>

\[Directories\] export\_base =
/tmp/%RED%your\_user<span class="twiki-macro ENDCOLOR"></span> read = /,
data write = /, output

\[Parrot\] location =
%RED%<http://your.host/~your_user%ENDCOLOR%/parrot.tar.gz>

\[Application\] location =
%RED%<http://your.host/~your_user%ENDCOLOR%/rjob_test.tar.gz> script =
./rjob\_test/myapp.sh \</pre\>

1.  In `rjob_test.ini`, change the url
    `http://your.host/~your_user/parrot.tar.gz` to point to the url of
    the parrot tarball that you copied previously. And set correctly the
    value of the exported local directory (/tmp/your\_user) and the
    CVMFS servers (repository keys and proxy)
2.  Run SkeletonKey on `rjob_test.ini`: \<pre class="screen"\>

<span class="twiki-macro UCL_PROMPT"></span> skeleton\_key -c
rjob\_test.ini \</pre\> 1. Run the job wrapper locally to verify that
it's working correctly \<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> sh ./job\_script.sh
test.output \</pre\>

## Submitting the job wrapper via Bosco

Once the job wrapper has been verified to work, multiple instances can
be run via Bosco. In this example we'll run 5 instances (`queue=5` in
the submit file).

Create =/tmp/bosco\_logs = for the log and output files for Bosco \<pre
class="screen"\> <span class="twiki-macro UCL_PROMPT"></span> mkdir
/tmp/bosco\_logs \</pre\>

Create Bosco (HTCondor) submit file called `rjob_test.submit` with the
following contents \<pre class="file"\> universe = grid grid\_resource =
batch %RED%pbs<span class="twiki-macro ENDCOLOR"></span>
%<RED%demoXX@boscopbs.opensciencegrid.org%ENDCOLOR>% notification=never
executable = ./job\_script.sh arguments = test.output.$(Process) output
= /tmp/bosco\_logs/rjtest\_$(Cluster).$(Process).out error =
/tmp/bosco\_logs/rjtest\_$(Cluster).$(Process).err log =
/tmp/bosco\_logs/rjtest.log ShouldTransferFiles = YES
when\_to\_transfer\_output = ON\_EXIT queue 5 \</pre\> Make sure to
specify the grid\_resource line as it was suggested when you added the
cluster with `bosco_cluster --add`. You can use also vanilla jobs but
there may be some more network requirement about the Bosco submit host.
See [Trash/CampusGrids.BoSCO](Trash/CampusGrids.BoSCO) for more
information.

Finally submit the job to Bosco and verify that the jobs ran
successfully\<pre class="screen"\>
<span class="twiki-macro UCL_PROMPT"></span> condor\_submit
rjob\_test.submit \</pre\>

Something to note in the HTCondor submit file, is that we're passing the
name of the output file that should be written using the `arguments`
setting and the file name we are using includes the `$(Process)`
variable to ensure that each queued job writes to a different file.
HTCondor will pass the variable to the job\_script.sh which then makes
sure that it gets appended to the arguments passed to the `myapp.sh`
script.

\-- Main.MarcoMambelli - 18 Apr 2013
