# grothlab/configs: DCAI GEFION configuration

The `dcai_gefion` profile configures grothlab or nf-core pipelines to run on the [DCAI GEFION cluster](https://dcai.dk/gefion).

Since grothlab and nf-core pipelines use the `params.custom_config_base` from `nf-core/configs` by default, you must override it to pull the `dcai_gefion` profile from `grothlab/configs` instead:

```bash
nextflow run <pipeline_repo>/<pipeline_name> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile dcai_gefion,<...> \
    <...>
```

## Using GPU resources

By default every process will only be able to request CPUs. Add `gpu` to `-profile` so that the processes that are able to use a GPU can also request one:

```bash
nextflow run <pipeline_repo>/<pipeline_name> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile dcai_gefion,gpu \
    <...>
```

> [!IMPORTANT]
> Using the `gpu` profile only has an effect on processes that support GPU acceleration. CPU-only processes will not request GPUs even under this profile, so it is safe to add `gpu` when running CPU-only pipelines.


## Example SBATCH script to submit a pipeline run to GEFION

Nextflow should not run on the login/submission node, but on a compute node.

To ensure this, write a shell script with a structure similar to the following and submit it with `sbatch my_script.sh`. Remember to replace the <...> fields:

```bash
#!/bin/bash

#SBATCH --job-name=<job_name>              # specify a name for the job
#SBATCH --mail-type=END,FAIL               # mail events (NONE, BEGIN, END, FAIL, ALL)
#SBATCH --mail-user=NONE                   # email address to receive the notifications
#SBATCH -c 1                               # number of requested cores for the Nextflow head job (1 should be enough)
#SBATCH --mem=15gb                         # total requested RAM for the Nextflow head job (15 GB should be enough)
#SBATCH --time=0-02:00:00                  # max. running time of the pipeline job, format in D-HH:MM:SS
#SBATCH --output=<job_name>.%j.log         # standard output and error log, '%j' gives the job ID
#SBATCH --account=<slurm_account>          # slurm account to submit this job with
##SBATCH --reservation=<slurm_reservation> # slurm reservation to submit this job with (remove one '#' from this line to enable)


# Set the SBATCH_ACCOUNT to your corresponding account in DCAI GEFION
export SBATCH_ACCOUNT=$(sacctmgr show association where users=$USER format=account -n -P)

# Uncomment the line below and set the SBATCH_RESERVATION if you have one
#export SBATCH_RESERVATION=<slurm_reservation>

# Set memory limits for the Nextflow head job
export NXF_OPTS="-Xms2g -Xmx4g"
export NXF_JVM_ARGS='-Xms2g -Xmx4g'

# Load the required modules
module purge
module load Java/25.36 Nextflow/25.10.2 Apptainer/1.3.6

# Create an output directory for the pipeline run if it does not exist
mkdir -p <path_to_project_directory>/output/
cd <path_to_project_directory>/output/

# Run a public pipeline
nextflow run <pipeline_repo>/<pipeline_name> \
    -r <pipeline_version> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile dcai_gefion,gpu \
    -params-file <path_to_project_directory>/<params_file_yaml>
```
