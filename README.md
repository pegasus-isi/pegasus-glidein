# pegasus-glidein - a simple HTCondor glidein for HPC systems

This is a sample script which can be used to show how to quickly set
up HTCondor pool overlays on top of other compute resources. A common
use case is having your own submit host, with HTCondor installed and
configured as a central manager, and then submitting jobs to a campus
cluster, with the jobs executing this script. The compute nodes will be
registered into your central manager as HTCondor compute nodes.


## Quickstart

To start the glidein on a general host (for example, a cloud instance):

    $ curl -O pegasus-glidein https://raw.githubusercontent.com/pegasus-isi/pegasus-glidein/main/pegasus-glidein
    $ chmod a+x pegasus-glidein
    $ ./pegasus-glidein -c pegasus.access-ci.org \\
                        -t mytoken \\
                        -s 'RemoteOwner == "myusername"'

Sample Slurm job:

    #!/bin/bash
    #
    #SBATCH --job-name=glidein
    #SBATCH --ntasks=4
    #SBATCH --time=24:00:00
    
    curl -O pegasus-glidein https://raw.githubusercontent.com/pegasus-isi/pegasus-glidein/main/pegasus-glidein
    chmod a+x pegasus-glidein
    ./pegasus-glidein -c pegasus.access-ci.org \
                      -t mytoken \
                      -s 'RemoteOwner == "myusername"'


## Usage

    Usage: pegasus-glidein [-h] [-w PATH] [-h HOURS] [-s START] [-t TOKEN] -c HOST
    
      -h        Show this help message.
      -c HOST   The hostname of the HTCondor central manager.
      -t TOKEN  The HTCondor token used for authentication.
      -s START  Start expression for the STARTD.
      -h HOURS  Number of hours to accept new jobs (defaults to 12)
      -w PATH   Use PATH as work directory. If not specified, \$TMP is used.
      -u URL    URL to a HTCondor tarball, if you do not want the default.


## Whole vs shared nodes / Walltime

The jobs should preferably be whole node jobs, with a wall time matching
the glidein and the user workload. For example, if the workload has jobs
that are a maximum 10 hours long, you can submit a job with a 24 hour
walltime, with a glidein with -t 14. The glidein will accept jobs for
14 hours, and will then allow any jobs to finish for the maximum of 10
hours.


## Generate tokens

If you are an ACCESS Pegasus user, pre-generated tokens are provided under
`~/.condor/pilot.token`.

For other systems, you can geneate a token with the following command:

    condor_token_create -identity someusername@somedomain \
                        -authz ADVERTISE_STARTD \
                        -authz ADVERTISE_MASTER \
                        -authz READ

