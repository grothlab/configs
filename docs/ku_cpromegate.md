# grothlab/configs: CPROME (ku_cpromegate) configuration

To use, run the pipeline with `-profile ku_cpromegate,<account>` (where `<account>` is one
of the per-project sub-profiles below, e.g. `cpr_ag`). This will download and launch the
[`ku_cpromegate.config`](../conf/ku_cpromegate.config) which has been pre-configured with a
setup suitable for the CPROME cluster (see the
[CPROME user guide](https://cprgpu.gitbook.io/cprgpu-user-guide/)).

GPU-labelled processes (label `process_gpu`) automatically get routed to the `a100`
partition with a GPU request - no extra flags needed.

## Per-project sub-profiles

Combine `ku_cpromegate` with one of these to set the SLURM account and scratch bind mounts
for your project: `cpr_ag`, `cpr_mx`, `cpr_sbmm`, `cpr_mito`, `cpr_mln`, `cpr_nm`,
`cpr_duxin`, `cpr_nilsson`, `cpr_crc`, `cpr_nk`, `cpr_mann`, `cpr_jensen`, `cpr_share`.

Add `long_queue` as well for a larger executor queue size on long-running submissions.

## Running Nextflow workflow on CPROME

Nextflow shouldn't run directly on the login/submission node but on a compute node.

To do so make a shell script with a similar structure to the following code and submit with `sbatch my_script.sh`

```bash
#!/bin/bash

#SBATCH --job-name=<job_name>       # specify a name for the job
#SBATCH --mail-type=END,FAIL        # mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --mail-user=NONE            # email address to receive the notifications
#SBATCH -c 1                        # number of requested cores for the Nextflow head job (1 should be enough)
#SBATCH --mem=4gb                   # total requested RAM for the Nextflow head job (4 GB should be enough)
#SBATCH --time=2-00:00:00           # max. running time of the pipeline job, format in D-HH:MM:SS
#SBATCH --output=<job_name>.%j.log  # standard output and error log, '%j' gives the job ID
#SBATCH --account=<slurm_account>   # slurm account to submit this job with

# Load the required modules
module purge
module load openjdk/20.0.0 nextflow/25.10.4 singularity/3.8.7

# Create an output directory for the pipeline run if it does not exist
mkdir -p <path_to_project_directory>/output/
cd <path_to_project_directory>/output/

# Run a public pipeline
nextflow run <pipeline_repo>/<pipeline_name> \
    -r <pipeline_version> \
    -profile ku_cpromegate,<slurm_account> \
    -params-file <path_to_project_directory>/<params_file_yaml> \
    -work-dir <path_to_project_directory>/output/work/ \
    -resume
```
