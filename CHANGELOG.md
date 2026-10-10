# Changelog

All notable changes to PullEmUp will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]
### Added
- Browse container images across registries in one window: a tree of sources, registries and repositories, the tags of the selected repository (digest, date, size), and the selected tag's details (platforms, layers with the command that created each one, config, labels, and a link to the source code when the image declares one).
- AWS ECR: one source per AWS profile (access keys, SSO, `credential_process`, assumed roles) and for `AWS_ACCESS_KEY_ID`, showing the account; extra regions per profile in Sources.
- Azure ACR: one source per subscription from the Azure CLI, with the registries it contains; registries you can only pull from can be added by hand.
- GitHub Container Registry: your packages and your organizations' packages, using `gh`, a `GH_TOKEN`/`GITHUB_TOKEN`, or a `docker login ghcr.io`.
- Docker Hub: your repositories, plus organizations and pinned repositories (such as `nginx`) set in Sources; pinned ones show even without a login.
- Any other OCI registry from `docker login` or `podman login` (including Podman's temporary logins), or added by hand with the repositories to show.
- Copy the pull command by tag or by digest.
- Search (Ctrl+K): find a repository in any registry, or paste an image reference such as `ghcr.io/owner/app:1.2` to open that tag.
- Filter the tag list by name.
- Sources: show or hide each detected source, add AWS regions, Docker Hub organizations and pinned repositories, and registries by hand.
- When a login is missing, expired or lacks a permission, the source says so and shows the command that fixes it; providers that weren't found are listed with how to connect them.
- Light and dark themes following the system.
