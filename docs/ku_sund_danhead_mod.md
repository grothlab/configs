# grothlab/configs: KU SUND danhead (ku_sund_danhead_mod) configuration

To use, run the pipeline with `-profile ku_sund_danhead_mod`. This will download and launch
the [`ku_sund_danhead_mod.config`](../conf/ku_sund_danhead_mod.config) which has been
pre-configured with a setup suitable for the `danhead01fl` cluster. It's a modified version
of the original config by Adrija Kalvisa (`adrija.kalvisa@sund.ku.dk`), disabling `cleanup`
so completed pipeline runs can still be `-resume`d.

GPU-labelled processes (label `process_gpu`) automatically get routed to the `gpuqueue`
partition with a GPU request - no extra flags needed.

## Node-pinning sub-profiles

Combine `ku_sund_danhead_mod` with one of these to pin execution to specific compute nodes:
`dancmpn01fl`, `dancmpn02fl`, `dan_allcmpnodes` (both), `dangpu01fl` (the GPU node).

## Running Nextflow workflow on danhead

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
module load java/21.0.7 nextflow/25.10.7 singularity/3.8.7

# Create an output directory for the pipeline run if it does not exist
mkdir -p <path_to_project_directory>/output/
cd <path_to_project_directory>/output/

# Run a public pipeline
nextflow run <pipeline_repo>/<pipeline_name> \
    -r <pipeline_version> \
    -profile ku_sund_danhead_mod \
    -params-file <path_to_project_directory>/<params_file_yaml> \
    -work-dir <path_to_project_directory>/output/work/ \
    -resume
```
