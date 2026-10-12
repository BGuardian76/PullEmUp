# Changelog

All notable changes to PullEmUp will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.7] - 2026-10-12
### Added
- Vulnerabilities: PullEmUp scans images itself, with no other tool to install. It reads the packages installed in the image's layers and checks them on OSV.dev, Google's free vulnerability database: Debian, Ubuntu, Alpine, Wolfi and Chainguard packages, Python, npm, Ruby and PHP packages, the Python interpreter of the official python images, and the Java libraries inside jar, war and ear files. Only package names and versions are sent, after you allow it once. Each finding shows its severity, score and the version that fixes it; layers already read aren't downloaded again, so rescans are quick. Works for registry and local images.
- Java archives without Maven information can be identified on Maven Central by their SHA-1 hash, after you allow it separately; only the hash is sent.
- ECR images show the registry's own scan findings (basic scanning or Amazon Inspector), next to PullEmUp's.
- The vulnerability report opens in its own window, like the layers inspector, with filters by severity, fix available and package. A summary stays in the tag's Overview.
- Helm charts and other OCI artifacts show as what they are: a chart's version, app version, dependencies and links, and the Copy menu offers `helm pull`, `helm show values` and `helm install` (`oras pull` for other artifacts) instead of `docker pull`.
- The sources pane can be hidden (button or Ctrl+B), with the selected registry, repository and tag shown in the header. Pane sizes are kept between runs.
### Changed
- The tag details header is shorter: the pull and run commands are in one Copy menu next to the title, and fields an image doesn't set are left out.
- The tag list keeps one line per tag: shorter dates (the full date on hover), and the digest and platforms columns appear only when the pane is wide enough.
### Fixed
- Downloading a large layer no longer fails with "error decoding response body" when it takes more than 30 seconds; only a download that stalls fails.

## [0.1.5] - 2026-10-12
### Added
- Images on this machine: a "This machine" source for the local Docker Desktop, Docker Engine or Podman, with no registry login needed. Tags show their date, size and platform; details, history and the layers inspector (files, previews, Save as, Compare) work as for registry images, and a `docker run` line replaces the pull commands. Untagged images are listed under `<none>`. When the engine isn't running, the socket needs permission, or Podman's API is off, the source says so and shows how to fix it.
- Where a local image came from: its details list the registries it was pulled from, whether each registry's tag still points to the same image, and a button to open that tag. Registries none of your sources cover are checked publicly (or with that host's `docker login`), reading only the tag's digest, so Docker Hub's pull limit isn't used.
- Compare a local image with its registry tag: layers with the same contents count as the same layer, and their file lists aren't downloaded again.
- Compare lines up layers by build step: a step only one tag has no longer shifts the rows after it, a step whose command changed shows both commands, and a replaced base image shows as one row.
- Compare checks file contents: listing a layer now records a hash of each file and its owner, so a file counts as changed only when its contents, size, permissions or owner differ, and "Same contents" marks files a rebuild only wrote again. Rebuilt layers say whether they hold the same files.
- Binary files open in a side-by-side hex and ASCII comparison marking the bytes that differ, with a jump to the first difference and an option to show only the rows that differ.
- Compare names files by the tag that has them ("Only in v3") instead of Added and Deleted, and a Swap button puts the other tag on the left.
### Fixed
- Docker Hub repository and tag lists are sorted newest first again; Docker Hub had changed what its sort order means.
- Files with the same permissions no longer count as changed when one image's builder records the file type in the mode and the other's doesn't.
- The tag list scrolls to a tag opened from search, favorites or a local image's origin, loading more of the list when needed.

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
