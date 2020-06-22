%META:TOPICINFO{author="KyleGross" date="1476284786" format="1.1"
version="1.15"}% %META:TOPICPARENT{name="BoSCO"}% \<img
src="%ATTACHURL%/BoscoR-pic.png" width=400\>\</img\>

# Bosco-R Instructions

<span class="twiki-macro NOTE"></span> This is a alpha document. If you
have any issues with installing and running Bosco or GridR, please email
<bosco-discuss@opensciencegrid.org>

Bosco-R is the transparent integration of the [R
statistics](http://www.r-project.org/) computing environment with
[Bosco](http://bosco.opensciencegrid.org/).

<span class="twiki-macro TOC"></span>

## Requirements

1.  Installation of R on your submit machine. You can find binaries on
    the [R website](http://cran.rstudio.com/)

## Installation Instructions

Installation is a three step process:

1.  Install Bosco
2.  Connect your cluster to Bosco
3.  Install the Bosco+GridR package.

Both 1 and 2 (the installation of Bosco and connecting your cluster) are
covered in the Bosco [quick start
guide](https://twiki.grid.iu.edu/bin/view/Trash/Trash/CampusGrids/BoscoQuickStart),
please use that guide to install and configure Bosco. This document will
only cover the installation of the Bosco+GridR package.

### Install Modified GridR

<span class="twiki-macro NOTE"></span> This section will be greatly
simplified once the modifications to GridR have been sent upstream.

<span class="twiki-macro NOTE"></span> Official documentation for
installing GridR can be found on GridR's
[wiki](https://github.com/osg-bosco/GridR/wiki/Installing-GridR).

Download
[GridR](https://www.dropbox.com/s/9yb50t07bn111xd/GridR_0.9.7.tar.gz).
Install with the command line:

``` file
%UCL_PROMPT% R CMD INSTALL --build Downloads/GridR_0.9.7.tar.gz 
```

## Using BoscoR

### A simple example.

Be sure to have bosco running. To ensure that Bosco is running, you can
run, from the command line:

``` screen
%UCL_PROMPT% source ~/bosco/bosco_setenv;
%UCL_PROMPT% bosco_start
```

Start R, or RStudio, or whatever R environment you prefer. First, you
need to load the GridR library:

``` screen
> library("GridR")
```

Then, you need to initialize GridR to use Bosco:

``` screen
> grid.init(service="bosco.direct", localTmpDir="tmp")
```

Now you are ready to create a function and send it to the connected
cluster. Below is a very simple function that will double the input
value:

``` screen
> a <- function(s) { return (2*s) }
```

Now, you can send it to the remote cluster.

``` screen
> grid.apply("x", a, 13)
starting bosco.direct mode
```

You can check on the status of the job with the command
grid.printJobs():

``` screen
> grid.printJobs()
```

Once the job has completed, the output value from the function `a` will
be assigned the the variable `x`.

``` screen
> x
[1] 26
```

%META:FILEATTACHMENT{name="Rbosco.png" attachment="Rbosco.png" attr=""
comment="" date="1372098727" path="R\&bosco.png" size="94331"
stream="R\&bosco.png" tmpFilename="/usr/tmp/CGItemp1407"
user="DerekWeitzel" version="1"}%
%META:FILEATTACHMENT{name="BoscoR-pic.png" attachment="BoscoR-pic.png"
attr="" comment="" date="1372296222" path="BoscoR-pic.png" size="87708"
stream="BoscoR-pic.png" tmpFilename="/usr/tmp/CGItemp1567"
user="DerekWeitzel" version="1"}%
