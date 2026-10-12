# Changelog

All notable changes to PullEmUp will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0] - 2026-10-12
### Added
- Browse container images across registries in one window: a tree of sources, registries and repositories, the tags of the selected repository (digest, date, size), and the selected tag's details (platforms, layers with the command that created each one, config, labels, and a link to the source code when the image declares one).
- AWS ECR: one source per AWS profile (access keys, SSO, `credential_process`, assumed roles) and for `AWS_ACCESS_KEY_ID`, showing the account; extra regions per profile in Sources. ECR registries you reach only with `docker login` (for example in an account none of your profiles covers) show as sources of their own, and a registry uses its `docker login` when the AWS route fails.
- Azure ACR: one source per subscription from the Azure CLI, with the registries it contains; registries you can only pull from can be added by hand. Registries you `docker login` to (admin user, repository tokens) work without the Azure CLI, and a registry uses its `docker login` when the Azure CLI route fails.
- GitHub Container Registry: your packages and your organizations' packages, using `gh`, a `GH_TOKEN`/`GITHUB_TOKEN`, or a `docker login ghcr.io`.
- Docker Hub: your repositories, plus organizations and pinned repositories (such as `nginx`) set in Sources; pinned ones show even without a login.
- Any other OCI registry from `docker login` or `podman login` (including Podman's temporary logins), or added by hand with the repositories to show. On quay.io, tags are listed newest first with their push date and digest.
- Copy the pull command by tag or by digest.
- Search (Ctrl+K): find a repository in any registry, or paste an image reference such as `ghcr.io/owner/app:1.2` to open that tag.
- Filter the tag list by name, and sort it by name, date or size by clicking the column headers. Names sort by number, so `v1.10` comes after `v1.9`.
- Sources: show or hide each detected source, add AWS regions, Docker Hub organizations and pinned repositories, and registries by hand. It opens on its own at first start when no registry login is found, with how to log in or browse public images.
- When a login is missing, expired or lacks a permission, the source says so and shows the command that fixes it; providers that weren't found are listed with how to connect them.
- Image details are kept in a disk cache, so viewing a tag again is instant and doesn't count against Docker Hub's pull limit. Multi-platform images load the other platforms only when you pick one, for the same reason. The cache is capped at 100 MB and can be cleared in Sources.
- Busy registries are handled gently: at most 6 requests at a time per registry, and short-lived errors (rate limits, server errors, dropped connections) are retried a couple of times before an error is shown. When Docker Hub's pull limit is used up, the message shows the limit and suggests `docker login`, which raises it.
- Old Docker schema 1 images show their digest and build date, and their details explain the old format instead of failing.
- Tags show their platforms (such as `linux/amd64, linux/arm64`); details are read only for the rows on screen, so long tag lists stay fast. Tags pointing at the same image can be grouped into one row, and a tag's digest can be copied.
- Favorites: star a repository to keep it in a Favorites group at the top of the tree. A filter box narrows the tree by name.
- Open in the registry's website: the AWS console, the Azure portal, GitHub, Docker Hub or Quay, at the repository or tag where the site allows it.
- Signatures, SBOMs and provenance: the tag's details list what's attached to the image (OCI referrers, cosign signatures and attestations, BuildKit attestations) and, from the build provenance, the source repository, commit and builder. Signatures are reported as found, not verified.
- Lists are saved between runs, so they show at once after a restart and while offline; an offline bar offers to try again. Each list has a refresh button, and errors offer Retry.
- Azure: Show all / Hide all for a tenant's subscriptions in Sources; hidden tenants aren't checked.
- Layers inspector, a window of its own per image:
  - **History**: the build steps as Dockerfile lines with each step's layer size, and the whole Dockerfile to copy.
  - **Files**: the image's filesystem, merged from all layers or one layer at a time, with what each layer added, changed or deleted and the space wasted by files later overwritten. Preview text files, save any file with Save as, and search for a file across all layers. Layers up to 50 MB load on their own; larger ones wait for a click. File lists are saved (up to 200 MB), so reopening an image downloads nothing.
  - **Compare**: diff the image against another tag of the same repository by layers, config settings and files, with the biggest changes listed, and a side-by-side text diff of a file's two versions.
- Light and dark themes following the system.
- About dialog: click **PullEmUp** in the toolbar to see the version.
