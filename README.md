# grothlab/configs

Shared institutional Nextflow configuration profiles for grothlab's Nextflow
pipelines, structured after [nf-core/configs](https://github.com/nf-core/configs).

## Using a config from grothlab/configs

Every grothlab or nf-core pipeline already has a `params.custom_config_base` pointed at
`nf-core/configs`. To instead use one of the institutional profiles from `grothlab/configs`just override it to point here instead, then select a profile with
`-profile`. For example:

From the CLI:

```bash
nextflow run <pipeline> \
    --custom_config_base https://raw.githubusercontent.com/grothlab/configs/master \
    -profile <profile_name>,<...> \
    <...>
```

Or via a `-params-file` (e.g., `params.yml`):

```yaml
custom_config_base: "https://raw.githubusercontent.com/grothlab/configs/master"
```

```bash
nextflow run <pipeline> \
    -params-file params.yml \
    -profile <profile_name>,<...> \
    <...>
```

These are the profiles available under `grothlab/configs`.
See the docs column below for examples of how to submit an SBATCH job in each cluster.

| Profile | Cluster | Docs |
| --- | --- | --- |
| `dcai_gefion` | DCAI GEFION | [docs/dcai_gefion.md](docs/dcai_gefion.md) |
| `ku_cpromegate` | CPROME (CPR HPC) | [docs/ku_cpromegate.md](docs/ku_cpromegate.md) |
| `ku_sund_danhead_mod` | KU SUND `danhead01fl` | [docs/ku_sund_danhead_mod.md](docs/ku_sund_danhead_mod.md) |