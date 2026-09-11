# grothlab/configs:  KU SUND danhead (ku_sund_danhead_mod) configuration

The `ku_sund_danhead_mod` profile configures grothlab or nf-core pipelines to run on the [KU DAN System cluster](https://sgn102.pages.ku.dk/a-not-long-tour-of-dangpu/).

Since grothlab and nf-core pipelines use the `params.custom_config_base` from `nf-core/configs` by default, you must override it to pull the `ku_sund_danhead_mod` profile from `grothlab/configs` instead:

```bash
nextflow run <pipeline_repo>/<pipeline_name> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile ku_sund_danhead_mod,<...> \
    <...>
```

## Using GPU resources

By default every process will only be able to request CPUs. Add `gpu` to `-profile` so that the processes that are able to use a GPU can also request one:

```bash
nextflow run <pipeline_repo>/<pipeline_name> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile ku_sund_danhead_mod,gpu \
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

# Load the required modules
module purge
module load java/21.0.7 nextflow/25.10.7 singularity/3.8.7

# Create an output directory for the pipeline run if it does not exist
mkdir -p <path_to_project_directory>/output/
cd <path_to_project_directory>/output/

# Run a public pipeline
nextflow run <pipeline_repo>/<pipeline_name> \
    -r <pipeline_version> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile ku_sund_danhead_mod,gpu \
    -params-file <path_to_project_directory>/<params_file_yaml> \
    -work-dir <path_to_project_directory>/output/work/ \
    -resume
```
