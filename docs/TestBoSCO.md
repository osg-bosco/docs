%META:TOPICINFO{author="KyleGross" date="1481047997" format="1.1"
version="1.18"}%

# Testing BOSCO

<span class="twiki-macro DOC_STATUS_TABLE"></span>
<span class="twiki-macro TOC" data-depth="3"></span>

\---\# Introduction This document describes a standard test of BOSCO. It
is for BOSCO developers and testers. It requires some knowledge of BOSCO
and Condor.

The BOSCO submit node is the host where BOSCO is installed and where
user/s login to submit jobs via BOSCO. The multiple clusters added to
BOSCO (i.e. where the user/s can submit jobs via BOSCO) are referred as
BOSCO resources. If you are not familiar with these terms or would like
more information about the BOSCO architecture, please check BoSCOv1p1.

\---\#\# Known Issues

  - [57](https://jira.opensciencegrid.org/browse/CAMPUS-57) Depreciation
    warning on cluster add.

\---\# Performing basic tests

Always:

  - Note the BOSCO/Condor version, output of `condor_version`
  - Note the platform of the BOSCO submit node (OS, version, 32/64 bit)
  - Note the platform of each BOSCO resource and its queue manager (if
    you have any resource added)

\---\#\# Install test Perform the installation as described in
BoscoInstall or BoscoMultiUser then start BOSCO with `bosco_start`.
Verify that condor\_q is working.

Report which platform was installed and if there is any error.

Verify that `bosco_cluster --list` and `findplatform` work correctly.

\---\#\# Add clusters Add the BOSCO resource as described in
BoscoInstall or BoscoMultiUser (`bosco_cluster --add`) then test the
resource with `bosco_cluster --test NAME`.

Report the output of `bosco_cluster --list`, the NAME of the resource,
jobmanager type, if the job completed successfully, the stdout of the
test, the username used, if it was single pr multi user condor and
important notes.

\---\#\# Run BOSCO (Grid) Jobs Run jobs as the `bosco` user. Submit
using the Condor "grid" universe for the specific cluster you added
(PBS, Condor, SGE, ...). For an example check the [BOSCO
documentation](BoSCOv1p1#6_2_1_Direct_Job_submission_exam).

Report if it ran correctly and on which resource it ran.

If you have multiple resources, verify that the jobs run on all of them,
one at the time.

\---\#\# Remove clusters Remove the BOSCO resource as described in
BoscoInstall or BoscoMultiUser (`bosco_cluster --remove`)

Verify that the command runs correctly and that the resource is no more
in the output of `bosco_cluster --list`.

\---\#\# Uninstall You can run this test after all the others to avoid
to re-install BOSCO.

Stop and/or Remove BOSCO:

  -  `bosco_stop` stops BOSCO
  - `bosco_uninstall` removes the BOSCO installation
  - `bosco_uninstall --all` removes both the BOSCO installation and the
    configuration files (list of installed clusters and keys)

Verify that the command runs correctly and that the uninstalled
directories/files are actually removed.

\---\# BOSCO Advanced (or Multi) user tests These tests verify BOSCO
with more than one cluster when the Campus Factory is in use.

\---\#\# Run BOSCO Vanilla Jobs Run Condor "vanilla" jobs as the `bosco`
user for single user BOSCO, as a user different from `bosco` or `root`
for multi user bosco. For an example check the [BOSCO
documentation](BoSCOv1p1#6_2_2_Glidein_Job_submission_exa).

Report if it ran correctly and on which resource it ran.

If you have multiple resources, verify that the jobs run on all of them.

\---\# BOSCO multi user tests On top of the basic tests in the multi
user environment, there are some tests specific for the multi user
environment (not as important for BOSCO single user)

\---\#\# Flocking to BOSCO Verify that other submit hosts can flock to
the BOSCO submit host by following the instructions in BoscoMultiUser

\---\#\# Querying BOSCO from a monitoring host Check that a monitoring
hosts can run commands like `condor_q` or `condor_status` against the
BOSCO submit host

\---\# BOSCO scalability and reliability tests Previous tests verify
BOSCO functionalities. This section consider tests measuring scalability
and/or reliability when running for longer period of time.

\---\#\# Scalability test Submit many jobs to BOSCO (while no other user
is using it), let it submit them to your available resources and measure
the results and the time used to complete. Please document in the note
if you are sending "vanilla" or "grid" universe jobs; how many resources
you are using, their type, an estimate of the available nodes (e.g. PBS
cluster with 20 nodes, Condor with 30 nodes - it has 60 but half of it
is normally used and not available for me). At the and measure how many
jobs failed (because of BOSCO or the clusters) and how long it took to
complete all the jobs.

\---\#\# Reliability test

\---\# Test policies Before a release we should test all the
functionalities above for all the supported queue managers, possibly on
all the platforms (at least both Mac and Linux).

\---\# Test results

Each person performing a test can copy the template. Then update the
summary for the version ---\#\# BOSCO 1.1 alpha Summary:

| Test                | Result                              | Date  | Notes |
| :------------------ | :---------------------------------- | :---- | :---- |
| **Install**         | <span class="twiki-macro Y"></span> | 11/8  | Marco |
| **Condor add/test** | <span class="twiki-macro Y"></span> | 11/8  | Marco |
| **LSF add/test**    |                                     |       |       |
| **PBS add/test**    | <span class="twiki-macro Y"></span> | 11/30 | Derek |
| **SGE add/test**    |                                     |       |       |
| **SLURM add/test**  |                                     |       |       |
| **Jobs**            | <span class="twiki-macro Y"></span> | 11/30 | Derek |
| **Flocking**        |                                     |       |       |
| **Querying**        |                                     |       |       |

\---\#\#\# Individual tests

Test by Marco Mambelli:

  - BOSCO version: $CondorVersion: 7.9.2 Nov 06 2012 BuildID: 76336
    PRE-RELEASE-UWCS $, $CondorPlatform: x86\_64\_RedHat5 $
  - Single/Multi user: multi
  - Platform: RH5
  - Hosts: uc3-bosco

| Test                | Result                              | Date | Notes                                                               |
| :------------------ | :---------------------------------- | :--- | :------------------------------------------------------------------ |
| **Install**         | <span class="twiki-macro Y"></span> |      |                                                                     |
| **Condor add/test** | <span class="twiki-macro Y"></span> |      |                                                                     |
| **LSF add/test**    |                                     |      |                                                                     |
| **PBS add/test**    | <span class="twiki-macro X"></span> |      | Jobs go on hold                                                     |
| **SGE add/test**    | <span class="twiki-macro X"></span> |      | Jobs go on hold                                                     |
| **SLURM add/test**  |                                     |      |                                                                     |
| **Jobs**            |                                     |      |                                                                     |
| **Flocking**        | <span class="twiki-macro X"></span> |      | CF crashing [39](https://jira.opensciencegrid.org/browse/CAMPUS-39) |
| **Querying**        |                                     |      |                                                                     |

Notes:

  - Have still to test Derek's 11/9 patch

Test by Derek:

  - BOSCO version:$CondorVersion: 7.9.2 Nov 30 2012 BuildID: 82024 BOSCO
    $, $CondorPlatform: x86\_64\_RedHat6 $
  - Single/Multi user: Single
  - Platform: RH6
  - Hosts: hcc-cloud instance

| Test                | Result                              | Date  | Notes                                                                             |
| :------------------ | :---------------------------------- | :---- | :-------------------------------------------------------------------------------- |
| **Version**         | <span class="twiki-macro Y"></span> | 11/30 |                                                                                   |
| **Single/Multi**    | Single                              | 11/30 |                                                                                   |
| **Target Platform** | RH5, SL6                            | 11/30 |                                                                                   |
| **Tester**          | Derek                               | 11/30 |                                                                                   |
| **Install**         | <span class="twiki-macro Y"></span> | 11/30 |                                                                                   |
| **Condor add/test** | <span class="twiki-macro Y"></span> | 11/30 | Worked, but inconvenience: [57](http://jira.opensciencegrid.org/browse/CAMPUS-57) |
| **LSF add/test**    |                                     |       |                                                                                   |
| **PBS add/test**    | <span class="twiki-macro Y"></span> | 11/30 | Worked, but inconvenience: [57](http://jira.opensciencegrid.org/browse/CAMPUS-57) |
| **SGE add/test**    | <span class="twiki-macro Y"></span> | 11/30 | Worked, but inconvenience: [57](http://jira.opensciencegrid.org/browse/CAMPUS-57) |
| **SLURM add/test**  |                                     |       |                                                                                   |
| **Grid Jobs**       | <span class="twiki-macro Y"></span> | 11/30 | Test is a grid job                                                                |
| **Vanilla Jobs**    | <span class="twiki-macro Y"></span> | 11/30 | Glideins started and ran test job                                                 |
| **Flocking**        |                                     |       |                                                                                   |
| **Querying**        |                                     |       |                                                                                   |

Test by Marco:

  - BOSCO version: (BOSCO 1.1 alpha3) $CondorVersion: 7.9.2 Nov 30 2012
    BuildID: 82024 BOSCO $ $CondorPlatform: x86\_64\_RedHat5 $
  - Single/Multi user: multi
  - Platform: SL5
  - Hosts: uc3-bosco
  - Tester: Marco

| Test                | Result                              | Date       | Notes                                                                                |
| :------------------ | :---------------------------------- | :--------- | :----------------------------------------------------------------------------------- |
| **Install**         | <span class="twiki-macro Y"></span> | 12/3       |                                                                                      |
| **Condor add/test** | <span class="twiki-macro Y"></span> | 12/4       |                                                                                      |
| **LSF add/test**    |                                     |            |                                                                                      |
| **PBS add/test**    | <span class="twiki-macro Y"></span> | 12/3       | Test job is OK                                                                       |
| **SGE add/test**    | <span class="twiki-macro X"></span> | 12/4       | <uc3@siraf-login.bsd.uchicago.edu> Some jobs go on hold. BLAH\_JOB\_STATUS timed out |
| **SLURM add/test**  | <span class="twiki-macro X"></span> | 12/4 and 7 | LSURM via PBS emulation                                                              |
| **Grid Jobs**       | <span class="twiki-macro Y"></span> |            | Only on Condor and PBS                                                               |
| **Vanilla Jobs**    | <span class="twiki-macro Y"></span> | 12/5       | On the supported clusters                                                            |
| **Flocking**        |                                     |            |                                                                                      |
| **Querying**        | <span class="twiki-macro Y"></span> | 12/4       | condor\_status -pool uc3-bosco.uchicago.edu:11000?sock=collector                     |


%TWISTY{%TWISTY_OPTS_OUTPUT% showlink="Click to show the test notes"}%
Notes:
   * Example of job going on hold on SGE (=/opt/bosco/local.uc3-bosco/bosco-test/tmp.fJeWQ16622/=): <pre class="file">000 (002.000.000) 12/04 17:28:49 Job submitted from host: <128.135.158.154:11000?sock=15493_1853_3>
...
027 (002.000.000) 12/04 17:28:57 Job submitted to grid resource
    GridResource: batch sge uc3@siraf-login.bsd.uchicago.edu
    GridJobId: batch sge uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#2.0#1354663729 sge/20121204173819/868604
...
012 (002.000.000) 12/04 17:35:03 Job was held.
	BLAH_JOB_STATUS timed out
	Code 0 Subcode 0
...
</pre> and job completing OK: <pre class="screen">-bash-3.2$ cat /opt/bosco/local.uc3-bosco/bosco-test/tmp.OklFU17319/logfile 
000 (003.000.000) 12/04 17:41:55 Job submitted from host: <128.135.158.154:11000?sock=15493_1853_3>
...
027 (003.000.000) 12/04 17:42:05 Job submitted to grid resource
    GridResource: batch sge uc3@siraf-login.bsd.uchicago.edu
    GridJobId: batch sge uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#3.0#1354664515 sge/20121204175125/868605
...
005 (003.000.000) 12/04 17:43:40 Job terminated.
	(1) Normal termination (return value 0)
		Usr 0 00:00:00, Sys 0 00:00:00  -  Run Remote Usage
		Usr 0 00:00:00, Sys 0 00:00:00  -  Run Local Usage
		Usr 0 00:00:00, Sys 0 00:00:00  -  Total Remote Usage
		Usr 0 00:00:00, Sys 0 00:00:00  -  Total Local Usage
	0  -  Run Bytes Sent By Job
	0  -  Run Bytes Received By Job
	0  -  Total Bytes Sent By Job
	0  -  Total Bytes Received By Job
...
</pre>
   * SLURM via PBS submission: <pre class="file">
12/07/12 13:29:38 [23823] Trying to update collector <128.135.158.154:11000?sock=collector>
12/07/12 13:29:38 [23823] Attempting to send update via TCP to collector uc3-bosco.uchicago.edu <128.135.158.154:11000?sock=collector>
12/07/12 13:29:38 [23823] (19.0) doEvaluateState called: gmState GM_INIT, remoteState 0
12/07/12 13:29:38 [23823] GAHP server pid = 24844
12/07/12 13:29:38 [23823] GAHP[24844] (stderr) -> Agent pid 24863
12/07/12 13:29:39 [23823] GAHP server version: $GahpVersion: 1.16.5 Mar 31 2008 INFN blahpd (poly,new_esc_format) $
12/07/12 13:29:39 [23823] GAHP[24844] <- 'COMMANDS'
12/07/12 13:29:39 [23823] GAHP[24844] -> 'S' 'ASYNC_MODE_OFF' 'ASYNC_MODE_ON' 'BLAH_GET_HOSTPORT' 'BLAH_JOB_CANCEL' 'BLAH_JOB_HOLD' 'BLAH_JOB_REFRESH_PROXY' 'BLAH_JOB_RESUME' 'BLAH_JOB_SEND_PROXY_TO_WORKER_NODE' 'BLAH_JOB_STATUS' 'BLAH_JOB_SUBMIT' 'BLAH_SET_GLEXEC_DN' 'BLAH_SET_GLEXEC_OFF' 'BLAH_SET_SUDO_ID' 'BLAH_SET_SUDO_OFF' 'COMMANDS' 'QUIT' 'RESULTS' 'VERSION'
12/07/12 13:29:39 [23823] GAHP[24844] <- 'ASYNC_MODE_ON'
12/07/12 13:29:39 [23823] GAHP[24844] -> 'S' 'Async mode on'
12/07/12 13:29:39 [23823] GAHP server pid = 24869
12/07/12 13:29:39 [23823] GAHP[24869] (stderr) -> Agent pid 24885
12/07/12 13:29:40 [23823] GAHP[24869] (stderr) -> Allocated port 55604 for remote forward to
12/07/12 13:29:40 [23823] GAHP server version: $GahpVersion 2.0.1 Jul 30 2012 Condor_FT_GAHP $
12/07/12 13:29:40 [23823] GAHP[24869] <- 'COMMANDS'
12/07/12 13:29:40 [23823] GAHP[24869] -> 'S' 'DOWNLOAD_SANDBOX' 'UPLOAD_SANDBOX' 'DESTROY_SANDBOX' 'ASYNC_MODE_ON' 'ASYNC_MODE_OFF' 'RESULTS' 'QUIT' 'VERSION' 'COMMANDS'
12/07/12 13:29:40 [23823] GAHP[24869] <- 'ASYNC_MODE_ON'
12/07/12 13:29:40 [23823] GAHP[24869] -> 'S'
12/07/12 13:29:40 [23823] (19.0) gm state change: GM_INIT -> GM_START
12/07/12 13:29:40 [23823] (19.0) gm state change: GM_START -> GM_CLEAR_REQUEST
12/07/12 13:29:40 [23823] (19.0) gm state change: GM_CLEAR_REQUEST -> GM_UNSUBMITTED
12/07/12 13:29:40 [23823] (19.0) gm state change: GM_UNSUBMITTED -> GM_SAVE_SANDBOX_ID
12/07/12 13:29:42 [23823] (18.0) doEvaluateState called: gmState GM_SUBMITTED, remoteState 2
12/07/12 13:29:42 [23823] (18.0) gm state change: GM_SUBMITTED -> GM_POLL_ACTIVE
12/07/12 13:29:42 [23823] GAHP[23829] <- 'BLAH_JOB_STATUS 22 condor/150972//'
12/07/12 13:29:42 [23823] GAHP[23829] -> 'S'
12/07/12 13:29:43 [23823] (16.0) doEvaluateState called: gmState GM_SUBMITTED, remoteState 2
12/07/12 13:29:43 [23823] (16.0) gm state change: GM_SUBMITTED -> GM_POLL_ACTIVE
12/07/12 13:29:43 [23823] GAHP[23829] <- 'BLAH_JOB_STATUS 23 condor/150971//'
12/07/12 13:29:43 [23823] GAHP[23829] -> 'S'
12/07/12 13:29:43 [23823] resource mmb@midway-login2.rcc.uchicago.edu is now up
12/07/12 13:29:43 [23823] in doContactSchedd()
12/07/12 13:29:43 [23823] SharedPortClient: sent connection request to schedd at <128.135.158.154:11000> for shared port id 23597_7536_3
12/07/12 13:29:43 [23823] querying for removed/held jobs
12/07/12 13:29:43 [23823] Using constraint ((Owner=?="uc3"&&JobUniverse==9)) && ((Managed =!= "ScheddDone")) && (JobStatus == 3 || JobStatus == 4 || (JobStatus == 5 && Managed =?= "External"))
12/07/12 13:29:43 [23823] Fetched 0 job ads from schedd
12/07/12 13:29:43 [23823] Updating classad values for 19.0:
12/07/12 13:29:43 [23823]    GridJobId = "batch pbs uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#19.0#1354908575"
12/07/12 13:29:43 [23823]    LastRemoteStatusUpdate = 1354908580
12/07/12 13:29:43 [23823] leaving doContactSchedd()
12/07/12 13:29:43 [23823] (19.0) doEvaluateState called: gmState GM_SAVE_SANDBOX_ID, remoteState 0
12/07/12 13:29:43 [23823] (19.0) gm state change: GM_SAVE_SANDBOX_ID -> GM_TRANSFER_INPUT
12/07/12 13:29:43 [23823] entering FileTransfer::Init
12/07/12 13:29:43 [23823] entering FileTransfer::SimpleInit
12/07/12 13:29:43 [23823] Entering FileTransfer::InitDownloadFilenameRemaps
12/07/12 13:29:43 [23823] FILETRANSFER: protocol "http" handled by "/opt/bosco/libexec/curl_plugin"
12/07/12 13:29:43 [23823] FILETRANSFER: protocol "ftp" handled by "/opt/bosco/libexec/curl_plugin"
12/07/12 13:29:43 [23823] FILETRANSFER: protocol "file" handled by "/opt/bosco/libexec/curl_plugin"
12/07/12 13:29:43 [23823] FILETRANSFER: protocol "data" handled by "/opt/bosco/libexec/data_plugin"
12/07/12 13:29:43 [23823] GAHP[24869] <- 'DOWNLOAD_SANDBOX 2 uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#19.0#1354908575 [\ Out\ =\ "_condor_stdout";\ TransferOutput\ =\ "";\ Cmd\ =\ "/bin/echo";\ CurrentTime\ =\ time();\ In\ =\ "/dev/null";\ Err\ =\ "_condor_stderr";\ TransferSocket\ =\ "<127.0.0.1:55604?sock=23604_e739_1>";\ TransferKey\ =\ "1#50c243a765f4a9f07e772aab";\ Iwd\ =\ "/share/home/osgvo/uc3/test-condor";\ TransferExecutable\ =\ false\ ]'
12/07/12 13:29:43 [23823] GAHP[24869] -> 'S'
12/07/12 13:29:43 [23823] DaemonCore: No more children processes to reap.
12/07/12 13:29:43 [23823] IPVERIFY: checking uc3-bosco.uchicago.edu against 128.135.158.154
12/07/12 13:29:43 [23823] IPVERIFY: matched 128.135.158.154 to 128.135.158.154
12/07/12 13:29:43 [23823] IPVERIFY: ip found is 1
12/07/12 13:29:43 [23823] entering FileTransfer::HandleCommands
12/07/12 13:29:43 [23823] FileTransfer::HandleCommands read transkey=1#50c243a765f4a9f07e772aab
12/07/12 13:29:43 [23823] Directory::setOwnerPriv() -- path /opt/bosco/local.uc3-bosco/spool/0/0/cluster0.proc0.subproc0.tmp does not exist (yet).
12/07/12 13:29:43 [23823] Directory::Rewind(): path "/opt/bosco/local.uc3-bosco/spool/0/0/cluster0.proc0.subproc0.tmp" does not exist (yet) 
12/07/12 13:29:43 [23823] Directory::setOwnerPriv() -- path /opt/bosco/local.uc3-bosco/spool/0/0/cluster0.proc0.subproc0 does not exist (yet).
12/07/12 13:29:43 [23823] Directory::Rewind(): path "/opt/bosco/local.uc3-bosco/spool/0/0/cluster0.proc0.subproc0" does not exist (yet) 
12/07/12 13:29:43 [23823] entering FileTransfer::Upload
12/07/12 13:29:43 [23823] entering FileTransfer::UploadThread
12/07/12 13:29:43 [23823] entering FileTransfer::DoUpload
12/07/12 13:29:43 [23823] DoUpload: exiting at 3060
12/07/12 13:29:44 [23823] DaemonCore: No more children processes to reap.
12/07/12 13:29:44 [23823] File transfer completed successfully.
12/07/12 13:29:44 [23823] GAHP[24869] <- 'RESULTS'
12/07/12 13:29:44 [23823] GAHP[24869] -> 'R'
12/07/12 13:29:44 [23823] GAHP[24869] -> 'S' '1'
12/07/12 13:29:44 [23823] GAHP[24869] -> '2' 'NULL' '/home/mmb/bosco/sandbox/a551/a551874d/uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#19.0#1354908575'
12/07/12 13:29:44 [23823] (19.0) doEvaluateState called: gmState GM_TRANSFER_INPUT, remoteState 0
12/07/12 13:29:44 [23823] (19.0) gm state change: GM_TRANSFER_INPUT -> GM_SUBMIT
12/07/12 13:29:44 [23823] GAHP[24844] <- 'BLAH_JOB_SUBMIT 2 [\ Out\ =\ "_condor_stdout";\ Environment\ =\ "";\ gridtype\ =\ "pbs";\ GridResource\ =\ "batch\ pbs\ mmb@midway-login2.rcc.uchicago.edu";\ Cmd\ =\ "/bin/echo";\ Args\ =\ "Hello";\ CurrentTime\ =\ time();\ Err\ =\ "_condor_stderr";\ In\ =\ "/dev/null";\ Iwd\ =\ "/home/mmb/bosco/sandbox/a551/a551874d/uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#19.0#1354908575"\ ]'
12/07/12 13:29:44 [23823] GAHP[24844] -> 'S'
12/07/12 13:29:45 [23823] GAHP[24844] <- 'RESULTS'
12/07/12 13:29:45 [23823] GAHP[24844] -> 'R'
12/07/12 13:29:45 [23823] GAHP[24844] -> 'S' '1'
12/07/12 13:29:45 [23823] GAHP[24844] -> '2' '0' 'No error' 'pbs/20121207/Submitted' 'batch' 'job' '3325100'
12/07/12 13:29:45 [23823] (19.0) doEvaluateState called: gmState GM_SUBMIT, remoteState 0
12/07/12 13:29:45 [23823] ERROR "Bad BLAH_JOB_SUBMIT Result" at line 3517 in file /slots/12/dir_25528/userdir/src/condor_gridmanager/gahp-client.cpp
12/07/12 13:34:35 OpSysMajorVersion:  5 
12/07/12 13:34:35 OpSysShortName:  SL 
12/07/12 13:34:35 OpSysLongName:  Scientific Linux SL release 5.5 (Boron) 
12/07/12 13:34:35 OpSysAndVer:  SL5 
12/07/12 13:34:35 OpSysLegacy:  LINUX 
12/07/12 13:34:35 OpSysName:  SL 
12/07/12 13:34:35 OpSysVer:  505 
12/07/12 13:34:35 OpSys:  LINUX 
12/07/12 13:34:35 Using processor count: 4 processors, 4 CPUs, 0 HTs
12/07/12 13:34:35 Enumerating interfaces: lo 127.0.0.1 up
12/07/12 13:34:35 Enumerating interfaces: eth0 10.1.3.89 up
12/07/12 13:34:35 Enumerating interfaces: eth1 128.135.158.154 up
12/07/12 13:34:35 Can't open directory "/config" as PRIV_USER, errno: 2 (No such file or directory)
12/07/12 13:34:35 passwd_cache::cache_uid(): getpwnam("condor") failed: user not found
12/07/12 13:34:35 passwd_cache::cache_uid(): getpwnam("condor") failed: user not found
12/07/12 13:34:35 Setting maximum accepts per cycle 8.
</pre>
   * SLURM via PBS test 2:<pre class="file">12/12/12 09:02:39 [363] (24.0) doEvaluateState called: gmState GM_INIT, remoteState 0
12/12/12 09:02:39 [363] GAHP server pid = 392
12/12/12 09:02:39 [363] GAHP[392] (stderr) -> Agent pid 413
12/12/12 09:02:40 [363] GAHP server version: $GahpVersion: 1.16.5 Mar 31 2008 INFN blahpd (poly,new_esc_format) $
12/12/12 09:02:40 [363] GAHP[392] <- 'COMMANDS'
12/12/12 09:02:40 [363] GAHP[392] -> 'S' 'ASYNC_MODE_OFF' 'ASYNC_MODE_ON' 'BLAH_GET_HOSTPORT' 'BLAH_JOB_CANCEL' 'BLAH_JOB_HOLD' 'BLAH_JOB_REFRESH_PROXY' 'BLAH_JOB_RESUME' 'BLAH_JOB_SEND_PROXY_TO_WORKER_NODE' 'BLAH_JOB_STATUS' 'BLAH_JOB_SUBMIT' 'BLAH_SET_GLEXEC_DN' 'BLAH_SET_GLEXEC_OFF' 'BLAH_SET_SUDO_ID' 'BLAH_SET_SUDO_OFF' 'COMMANDS' 'QUIT' 'RESULTS' 'VERSION'
12/12/12 09:02:40 [363] GAHP[392] <- 'ASYNC_MODE_ON'
12/12/12 09:02:40 [363] GAHP[392] -> 'S' 'Async mode on'
12/12/12 09:02:40 [363] GAHP server pid = 419
12/12/12 09:02:40 [363] GAHP[419] (stderr) -> Agent pid 435
12/12/12 09:02:42 [363] GAHP[419] (stderr) -> Allocated port 49121 for remote forward to
12/12/12 09:02:42 [363] GAHP server version: $GahpVersion 2.0.1 Jul 30 2012 Condor_FT_GAHP $
12/12/12 09:02:42 [363] GAHP[419] <- 'COMMANDS'
12/12/12 09:02:42 [363] GAHP[419] -> 'S' 'DOWNLOAD_SANDBOX' 'UPLOAD_SANDBOX' 'DESTROY_SANDBOX' 'ASYNC_MODE_ON' 'ASYNC_MODE_OFF' 'RESULTS' 'QUIT' 'VERSION' 'COMMANDS'
12/12/12 09:02:42 [363] GAHP[419] <- 'ASYNC_MODE_ON'
12/12/12 09:02:42 [363] GAHP[419] -> 'S'
12/12/12 09:02:42 [363] (24.0) gm state change: GM_INIT -> GM_START
12/12/12 09:02:42 [363] (24.0) gm state change: GM_START -> GM_CLEAR_REQUEST
12/12/12 09:02:42 [363] (24.0) gm state change: GM_CLEAR_REQUEST -> GM_UNSUBMITTED
12/12/12 09:02:42 [363] (24.0) gm state change: GM_UNSUBMITTED -> GM_SAVE_SANDBOX_ID
12/12/12 09:02:42 [363] SharedPortClient: sent connection request to collector uc3-bosco.uchicago.edu:11000?sock=collector for shared port id collector
12/12/12 09:02:42 [363] Evaluating staleness of remote job statuses.
12/12/12 09:02:44 [363] resource mmb@midway-login2.rcc.uchicago.edu is now up
12/12/12 09:02:44 [363] in doContactSchedd()
12/12/12 09:02:44 [363] SharedPortClient: sent connection request to schedd at <128.135.158.154:11000> for shared port id 23597_7536_3
12/12/12 09:02:44 [363] querying for removed/held jobs
12/12/12 09:02:44 [363] Using constraint ((Owner=?="uc3"&&JobUniverse==9)) && ((Managed =!= "ScheddDone")) && (JobStatus == 3 || JobStatus == 4 || (JobStatus == 5 && Managed =?= "External"))
12/12/12 09:02:44 [363] Fetched 0 job ads from schedd
12/12/12 09:02:44 [363] Updating classad values for 24.0:
12/12/12 09:02:44 [363]    GridJobId = "batch pbs uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#24.0#1355324556"
12/12/12 09:02:44 [363]    LastRemoteStatusUpdate = 1355324562
12/12/12 09:02:44 [363] leaving doContactSchedd()
12/12/12 09:02:44 [363] (24.0) doEvaluateState called: gmState GM_SAVE_SANDBOX_ID, remoteState 0
12/12/12 09:02:44 [363] (24.0) gm state change: GM_SAVE_SANDBOX_ID -> GM_TRANSFER_INPUT
12/12/12 09:02:44 [363] entering FileTransfer::Init
12/12/12 09:02:44 [363] entering FileTransfer::SimpleInit
12/12/12 09:02:44 [363] Entering FileTransfer::InitDownloadFilenameRemaps
12/12/12 09:02:44 [363] FILETRANSFER: protocol "http" handled by "/opt/bosco/libexec/curl_plugin"
12/12/12 09:02:44 [363] FILETRANSFER: protocol "ftp" handled by "/opt/bosco/libexec/curl_plugin"
12/12/12 09:02:44 [363] FILETRANSFER: protocol "file" handled by "/opt/bosco/libexec/curl_plugin"
12/12/12 09:02:44 [363] FILETRANSFER: protocol "data" handled by "/opt/bosco/libexec/data_plugin"
12/12/12 09:02:44 [363] GAHP[419] <- 'DOWNLOAD_SANDBOX 2 uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#24.0#1355324556 [\ Out\ =\ "_condor_stdout";\ TransferOutput\ =\ "";\ Cmd\ =\ "/bin/echo";\ CurrentTime\ =\ time();\ In\ =\ "/dev/null";\ Err\ =\ "_condor_stderr";\ TransferSocket\ =\ "<127.0.0.1:49121?sock=23604_e739_20>";\ TransferKey\ =\ "1#50c89c9422ead8da608694b0";\ Iwd\ =\ "/share/home/osgvo/uc3/test-condor";\ TransferExecutable\ =\ false\ ]'
12/12/12 09:02:44 [363] GAHP[419] -> 'S'
12/12/12 09:02:44 [363] DaemonCore: No more children processes to reap.
12/12/12 09:02:44 [363] IPVERIFY: checking uc3-bosco.uchicago.edu against 128.135.158.154
12/12/12 09:02:44 [363] IPVERIFY: matched 128.135.158.154 to 128.135.158.154
12/12/12 09:02:44 [363] IPVERIFY: ip found is 1
12/12/12 09:02:44 [363] entering FileTransfer::HandleCommands
12/12/12 09:02:44 [363] FileTransfer::HandleCommands read transkey=1#50c89c9422ead8da608694b0
12/12/12 09:02:44 [363] Directory::setOwnerPriv() -- path /opt/bosco/local.uc3-bosco/spool/0/0/cluster0.proc0.subproc0.tmp does not exist (yet).
12/12/12 09:02:44 [363] Directory::Rewind(): path "/opt/bosco/local.uc3-bosco/spool/0/0/cluster0.proc0.subproc0.tmp" does not exist (yet) 
12/12/12 09:02:44 [363] Directory::setOwnerPriv() -- path /opt/bosco/local.uc3-bosco/spool/0/0/cluster0.proc0.subproc0 does not exist (yet).
12/12/12 09:02:44 [363] Directory::Rewind(): path "/opt/bosco/local.uc3-bosco/spool/0/0/cluster0.proc0.subproc0" does not exist (yet) 
12/12/12 09:02:44 [363] entering FileTransfer::Upload
12/12/12 09:02:44 [363] entering FileTransfer::UploadThread
12/12/12 09:02:44 [363] entering FileTransfer::DoUpload
12/12/12 09:02:44 [363] DoUpload: exiting at 3060
12/12/12 09:02:44 [363] DaemonCore: No more children processes to reap.
12/12/12 09:02:44 [363] File transfer completed successfully.
12/12/12 09:02:44 [363] GAHP[419] <- 'RESULTS'
12/12/12 09:02:44 [363] GAHP[419] -> 'R'
12/12/12 09:02:44 [363] GAHP[419] -> 'S' '1'
12/12/12 09:02:44 [363] GAHP[419] -> '2' 'NULL' '/home/mmb/bosco/sandbox/13e7/13e72caf/uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#24.0#1355324556'
12/12/12 09:02:44 [363] (24.0) doEvaluateState called: gmState GM_TRANSFER_INPUT, remoteState 0
12/12/12 09:02:44 [363] (24.0) gm state change: GM_TRANSFER_INPUT -> GM_SUBMIT
12/12/12 09:02:44 [363] GAHP[392] <- 'BLAH_JOB_SUBMIT 2 [\ Out\ =\ "_condor_stdout";\ Environment\ =\ "";\ gridtype\ =\ "pbs";\ GridResource\ =\ "batch\ pbs\ mmb@midway-login2.rcc.uchicago.edu";\ Cmd\ =\ "/bin/echo";\ Args\ =\ "Hello";\ CurrentTime\ =\ time();\ Err\ =\ "_condor_stderr";\ In\ =\ "/dev/null";\ Iwd\ =\ "/home/mmb/bosco/sandbox/13e7/13e72caf/uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#24.0#1355324556"\ ]'
12/12/12 09:02:44 [363] GAHP[392] -> 'S'
12/12/12 09:02:46 [363] GAHP[392] <- 'RESULTS'
12/12/12 09:02:46 [363] GAHP[392] -> 'R'
12/12/12 09:02:46 [363] GAHP[392] -> 'S' '1'
12/12/12 09:02:46 [363] GAHP[392] -> '2' '0' 'No error' 'pbs/20121212/3369743'
12/12/12 09:02:46 [363] (24.0) doEvaluateState called: gmState GM_SUBMIT, remoteState 0
12/12/12 09:02:46 [363] directory_util::rec_touch_file: Creating directory /tmp 
12/12/12 09:02:46 [363] directory_util::rec_touch_file: Creating directory /tmp/condorLocks 
12/12/12 09:02:46 [363] directory_util::rec_touch_file: Creating directory /tmp/condorLocks/27 
12/12/12 09:02:46 [363] directory_util::rec_touch_file: Creating directory /tmp/condorLocks/27/19 
12/12/12 09:02:46 [363] FileLock object is updating timestamp on: /tmp/condorLocks/27/19/50634547879889.lockc
12/12/12 09:02:46 [363] WriteUserLog::initialize: opened /opt/bosco/local.uc3-bosco/bosco-test/tmp.MWflFJN353/logfile successfully
12/12/12 09:02:46 [363] (24.0) Writing grid submit record to user logfile
12/12/12 09:02:46 [363] FileLock::obtain(1) - @1355324566.457000 lock on /tmp/condorLocks/27/19/50634547879889.lockc now WRITE
12/12/12 09:02:46 [363] FileLock::obtain(2) - @1355324566.463418 lock on /tmp/condorLocks/27/19/50634547879889.lockc now UNLOCKED
12/12/12 09:02:46 [363] FileLock::obtain(1) - @1355324566.463495 lock on /tmp/condorLocks/27/19/50634547879889.lockc now WRITE
12/12/12 09:02:46 [363] directory_util::rec_clean_up: file /tmp/condorLocks/27/19/50634547879889.lockc has been deleted. 
12/12/12 09:02:46 [363] Lock file /tmp/condorLocks/27/19/50634547879889.lockc has been deleted. 
12/12/12 09:02:46 [363] FileLock::obtain(2) - @1355324566.463625 lock on /tmp/condorLocks/27/19/50634547879889.lockc now UNLOCKED
12/12/12 09:02:46 [363] (24.0) gm state change: GM_SUBMIT -> GM_SUBMIT_SAVE
12/12/12 09:02:49 [363] in doContactSchedd()
12/12/12 09:02:49 [363] SharedPortClient: sent connection request to schedd at <128.135.158.154:11000> for shared port id 23597_7536_3
12/12/12 09:02:49 [363] querying for removed/held jobs
12/12/12 09:02:49 [363] Using constraint ((Owner=?="uc3"&&JobUniverse==9)) && ((Managed =!= "ScheddDone")) && (JobStatus == 3 || JobStatus == 4 || (JobStatus == 5 && Managed =?= "External"))
12/12/12 09:02:49 [363] Fetched 0 job ads from schedd
12/12/12 09:02:49 [363] Updating classad values for 24.0:
12/12/12 09:02:49 [363]    GridJobId = "batch pbs uc3-bosco.uchicago.edu_11000_uc3-bosco.uchicago.edu#24.0#1355324556 pbs/20121212/3369743"
12/12/12 09:02:49 [363] leaving doContactSchedd()
12/12/12 09:02:49 [363] (24.0) doEvaluateState called: gmState GM_SUBMIT_SAVE, remoteState 0
12/12/12 09:02:49 [363] (24.0) gm state change: GM_SUBMIT_SAVE -> GM_SUBMITTED
12/12/12 09:03:36 [363] Received CHECK_LEASES signal
12/12/12 09:03:36 [363] in doContactSchedd()
12/12/12 09:03:36 [363] SharedPortClient: sent connection request to schedd at <128.135.158.154:11000> for shared port id 23597_7536_3
12/12/12 09:03:36 [363] querying for renewed leases
12/12/12 09:03:36 [363] querying for removed/held jobs
12/12/12 09:03:36 [363] Using constraint ((Owner=?="uc3"&&JobUniverse==9)) && ((Managed =!= "ScheddDone")) && (JobStatus == 3 || JobStatus == 4 || (JobStatus == 5 && Managed =?= "External"))
12/12/12 09:03:36 [363] Fetched 0 job ads from schedd
12/12/12 09:03:36 [363] leaving doContactSchedd()
12/12/12 09:03:40 [363] GAHP[392] <- 'RESULTS'
12/12/12 09:03:40 [363] GAHP[392] -> 'S' '0'
12/12/12 09:03:42 [363] GAHP[419] <- 'RESULTS'
12/12/12 09:03:42 [363] GAHP[419] -> 'S' '0'
12/12/12 09:03:42 [363] Evaluating staleness of remote job statuses.
12/12/12 09:03:49 [363] (24.0) doEvaluateState called: gmState GM_SUBMITTED, remoteState 0
12/12/12 09:03:49 [363] (24.0) gm state change: GM_SUBMITTED -> GM_POLL_ACTIVE
12/12/12 09:03:49 [363] GAHP[392] <- 'BLAH_JOB_STATUS 3 pbs/20121212/3369743'
12/12/12 09:03:49 [363] GAHP[392] -> 'S'
12/12/12 09:03:50 [363] GAHP[392] <- 'RESULTS'
12/12/12 09:03:50 [363] GAHP[392] -> 'R'
12/12/12 09:03:50 [363] GAHP[392] -> 'S' '1'
12/12/12 09:03:50 [363] GAHP[392] -> '3' '1' 'Error allocating memory' '0' 'N/A'
12/12/12 09:03:50 [363] (24.0) doEvaluateState called: gmState GM_POLL_ACTIVE, remoteState 0
12/12/12 09:03:50 [363] (24.0) blah_job_status() failed: Error allocating memory
12/12/12 09:03:50 [363] (24.0) gm state change: GM_POLL_ACTIVE -> GM_HOLD
12/12/12 09:03:50 [363] directory_util::rec_touch_file: Creating directory /tmp 
12/12/12 09:03:50 [363] directory_util::rec_touch_file: Creating directory /tmp/condorLocks 
12/12/12 09:03:50 [363] directory_util::rec_touch_file: Creating directory /tmp/condorLocks/27 
12/12/12 09:03:50 [363] directory_util::rec_touch_file: Creating directory /tmp/condorLocks/27/19 
12/12/12 09:03:50 [363] FileLock object is updating timestamp on: /tmp/condorLocks/27/19/50634547879889.lockc
12/12/12 09:03:50 [363] WriteUserLog::initialize: opened /opt/bosco/local.uc3-bosco/bosco-test/tmp.MWflFJN353/logfile successfully
12/12/12 09:03:50 [363] (24.0) Writing hold record to user logfile
12/12/12 09:03:50 [363] FileLock::obtain(1) - @1355324630.888747 lock on /tmp/condorLocks/27/19/50634547879889.lockc now WRITE
12/12/12 09:03:50 [363] FileLock::obtain(2) - @1355324630.891806 lock on /tmp/condorLocks/27/19/50634547879889.lockc now UNLOCKED
12/12/12 09:03:50 [363] FileLock::obtain(1) - @1355324630.891882 lock on /tmp/condorLocks/27/19/50634547879889.lockc now WRITE
12/12/12 09:03:50 [363] directory_util::rec_clean_up: file /tmp/condorLocks/27/19/50634547879889.lockc has been deleted. 
12/12/12 09:03:50 [363] Lock file /tmp/condorLocks/27/19/50634547879889.lockc has been deleted. 
12/12/12 09:03:50 [363] FileLock::obtain(2) - @1355324630.892015 lock on /tmp/condorLocks/27/19/50634547879889.lockc now UNLOCKED
12/12/12 09:03:50 [363] (24.0) gm state change: GM_HOLD -> GM_DELETE
12/12/12 09:03:50 [363] in doContactSchedd()
12/12/12 09:03:50 [363] SharedPortClient: sent connection request to schedd at <128.135.158.154:11000> for shared port id 23597_7536_3
12/12/12 09:03:50 [363] querying for removed/held jobs
12/12/12 09:03:50 [363] Using constraint ((Owner=?="uc3"&&JobUniverse==9)) && ((Managed =!= "ScheddDone")) && (JobStatus == 3 || JobStatus == 4 || (JobStatus == 5 && Managed =?= "External"))
12/12/12 09:03:50 [363] Fetched 0 job ads from schedd
12/12/12 09:03:50 [363] Updating classad values for 24.0:
12/12/12 09:03:50 [363]    EnteredCurrentStatus = 1355324630
12/12/12 09:03:50 [363]    HoldReason = "Error allocating memory"
12/12/12 09:03:50 [363]    HoldReasonCode = 0
12/12/12 09:03:50 [363]    HoldReasonSubCode = 0
12/12/12 09:03:50 [363]    JobStatus = 5
12/12/12 09:03:50 [363]    Managed = "Schedd"
12/12/12 09:03:50 [363]    NumSystemHolds = 1
12/12/12 09:03:50 [363]    ReleaseReason = undefined
12/12/12 09:03:50 [363] No jobs left, shutting down
12/12/12 09:03:50 [363] leaving doContactSchedd()
12/12/12 09:03:50 [363] Got SIGTERM. Performing graceful shutdown.
12/12/12 09:03:50 [363] Started timer to call main_shutdown_fast in 1800 seconds
12/12/12 09:03:50 [363] **** condor_gridmanager (condor_GRIDMANAGER) pid 363 EXITING WITH STATUS 0
</pre>
%ENDTWISTY%





\---\#\# BOSCO 1.1 beta

Summary of the BOSCO 1.1 beta tests

  - BOSCO version: 1.1 beta
  - Single/Multi user:
  - Platform:
  - Hosts:
  - Tester:

| Test                | Result | Date | Notes |
| :------------------ | :----- | :--- | :---- |
| **Install**         |        |      |       |
| **Condor add/test** |        |      |       |
| **LSF add/test**    |        |      |       |
| **PBS add/test**    |        |      |       |
| **SGE add/test**    |        |      |       |
| **SLURM add/test**  |        |      |       |
| **Remove resource** |        |      |       |
| **Grid Jobs**       |        |      |       |
| **Vanilla Jobs**    |        |      |       |
| **Flocking**        |        |      |       |
| **Querying**        |        |      |       |
| **Uninstall**       |        |      |       |

<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_OUTPUT% showlink="Click
to show the test notes"</span> Notes: \*
<span class="twiki-macro ENDTWISTY"></span>

\---\#\#\# Individual tests

\---\#\# Template

Test by NAME:

  - BOSCO version:
  - Single/Multi user:
  - Platform:
  - Hosts:
  - Tester:

| Test                | Result | Date | Notes |
| :------------------ | :----- | :--- | :---- |
| **Install**         |        |      |       |
| **Condor add/test** |        |      |       |
| **LSF add/test**    |        |      |       |
| **PBS add/test**    |        |      |       |
| **SGE add/test**    |        |      |       |
| **SLURM add/test**  |        |      |       |
| **Remove resource** |        |      |       |
| **Grid Jobs**       |        |      |       |
| **Vanilla Jobs**    |        |      |       |
| **Flocking**        |        |      |       |
| **Querying**        |        |      |       |
| **Uninstall**       |        |      |       |

<span class="twiki-macro TWISTY">%TWISTY\_OPTS\_OUTPUT% showlink="Click
to show the test notes"</span> Notes: \*
<span class="twiki-macro ENDTWISTY"></span>

<span class="twiki-macro INCLUDE" data-section="BoscoReferences">BoscoInstall</span>

# Comments

<span class="twiki-macro COMMENT" type="tableappend"></span>
