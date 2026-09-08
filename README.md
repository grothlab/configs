# grothlab/configs

Shared institutional Nextflow configuration profiles for grothlab's nf-core-template
pipelines, structured after [nf-core/configs](https://github.com/nf-core/configs) but kept
to just what the lab actually needs.

## Using a config

Any nf-core-template pipeline already knows how to load profiles from this repository - it's
the same `params.custom_config_base`/`nfcore_custom.config` mechanism the template uses for
`nf-core/configs` itself, just pointed here instead. Run with:

```bash
nextflow run <pipeline> -profile <profile_name>,<...>
```

| Profile | Cluster | Docs |
| --- | --- | --- |
| `dcai_gefion` | DCAI GEFION | [docs/dcai_gefion.md](docs/dcai_gefion.md) |
| `ku_cpromegate` | CPROME (CPR HPC) | [docs/ku_cpromegate.md](docs/ku_cpromegate.md) |
| `ku_sund_danhead_mod` | KU SUND `danhead01fl` | [docs/ku_sund_danhead_mod.md](docs/ku_sund_danhead_mod.md) |

Each profile combines with a container engine profile (e.g. `singularity`) and, for
`ku_cpromegate`, a per-project sub-profile (e.g. `cpr_ag`) - see each profile's docs page.

## Pointing a pipeline here

A pipeline's `nextflow.config` needs its own `params.custom_grothlab_config_base` plus a
matching `includeConfig`, kept separate from the nf-core-template's own
`params.custom_config_base`/nf-core/configs mechanism so both can load independently:

```groovy
params {
    custom_grothlab_config_version = 'main'
    custom_grothlab_config_base    = "https://raw.githubusercontent.com/grothlab/configs/${params.custom_grothlab_config_version}"
}

includeConfig params.custom_grothlab_config_base && (!System.getenv('NXF_OFFLINE') || !params.custom_grothlab_config_base.startsWith('http')) ? "${params.custom_grothlab_config_base}/nfcore_custom.config" : "/dev/null"
```

## Adding a new config

1. Add `conf/<name>.config`, with a `params` block setting `config_profile_description` and
   `config_profile_contact` so anyone using it knows where it came from and who to ask.
2. Add `docs/<name>.md` describing how to use it (see the existing docs for the template).
3. Add an entry to `nfcore_custom.config`'s `profiles {}` block.
4. Add a row to the table above.

## License

MIT - see [LICENSE](LICENSE).
