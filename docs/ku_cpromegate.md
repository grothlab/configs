# grothlab/configs: CPROME (ku_cpromegate) configuration

The `ku_cpromegate` profile configures grothlab or nf-core pipelines to run on the [KU CPROME cluster](https://cprgpu.gitbook.io/cprgpu-user-guide/).

Since grothlab and nf-core pipelines use the `params.custom_config_base` from `nf-core/configs` by default, you must override it to pull the `ku_cpromegate` profile from `grothlab/configs` instead:

```bash
nextflow run <pipeline_repo>/<pipeline_name> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile ku_cpromegate,<...> \
    <...>
```

## Per-project sub-profiles

Combine `ku_cpromegate` with one of these to set the SLURM account for your project: `cpr_ag`, `cpr_mx`, `cpr_sbmm`, `cpr_mito`, `cpr_mln`, `cpr_nm`,
`cpr_duxin`, `cpr_nilsson`, `cpr_crc`, `cpr_nk`, `cpr_mann`, `cpr_jensen`, `cpr_share`.

```bash
nextflow run <pipeline_repo>/<pipeline_name> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile ku_cpromegate,cpr_<...> \
    <...>
```

## Using GPU resources

By default every process will only be able to request CPUs. Add `gpu` to `-profile` so that the processes that are able to use a GPU can also request one:

```bash
nextflow run <pipeline_repo>/<pipeline_name> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile ku_cpromegate,cpr_<...>,gpu \
    <...>
```

> [!IMPORTANT]
> Using the `gpu` profile only has an effect on processes that support GPU acceleration. CPU-only processes will not request GPUs even under this profile, so it is safe to add `gpu` when running CPU-only pipelines.

## Example SBATCH script to submit a pipeline run to CPROME

Nextflow should not run on the login/submission node, but on a compute node.

To ensure this, write a shell script with a structure similar to the following and submit it with `sbatch my_script.sh`. Remember to replace the <...> fields:

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
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile ku_cpromegate,cpr_<...>,gpu \
    -params-file <path_to_project_directory>/<params_file_yaml> \
    -work-dir <path_to_project_directory>/output/work/ \
    -resume
```
