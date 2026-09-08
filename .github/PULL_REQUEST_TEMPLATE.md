---
name: New Config
about: A new cluster config
---

Please follow these steps before submitting your PR:

- [ ] If your PR is a work in progress, include `[WIP]` in its title
- [ ] Your PR targets the `main` branch
- [ ] You've included links to relevant issues, if any

Steps for adding a new config profile:

- [ ] Add your custom config file to the `conf/` directory
- [ ] Add your documentation file to the `docs/` directory
- [ ] Add your custom profile to the `nfcore_custom.config` file in the top-level directory
- [ ] Add your profile name to the `profile:` scope in `.github/workflows/main.yml`
- [ ] OPTIONAL: Add your custom profile path and GitHub user name to `.github/CODEOWNERS` (`**/<custom-profile>** @<github-username>`)
