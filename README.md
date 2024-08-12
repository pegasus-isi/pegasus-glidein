# pegasus-glidein - a simple HTCondor glidein for HPC systems

This is a simple script to start HTCondor glideins on HPC systems. Users will
start Slurm jobs under their own allocation. The jobs will start a HTCondor
execution point instance, which will connect back to a HTCondor central
manager, and start running jobs. Once there are no more jobs, the glidein
will exit.


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


## Generate tokens

If you are an ACCESS Pegasus user, pre-generated tokens are provided under
`~/.condor/pilot.token`.

For other systems, you can geneate a token with the following command:

    condor_token_create -identity someusername@somedomain \
                        -authz ADVERTISE_STARTD \
                        -authz ADVERTISE_MASTER \
                        -authz READ

