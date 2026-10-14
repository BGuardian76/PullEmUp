# Changelog

All notable changes to PullEmUp will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.12] - 2026-10-14
### Added
- Images by base: a registry's new button in the sources pane opens a window that reads the newest tag of each of its repositories and groups the images by the base image they were built on. Each image shows whether its base is up to date, has a rebuild with newer OS packages, or has a newer version, so you see which images need rebuilding. Filter by repository, tag, base or OS (type `python:3.11` to find every image on it), show only the images that are out of date, and copy the list as CSV or Markdown. The survey is saved and shows at once next time; **Survey again** reads the registry again.
- Approved base images: list the bases your team allows in Settings, under Base images, with `*` for any text (`python:3.12*`, `mcr.microsoft.com/dotnet/aspnet:8.0*`). Images by base marks images built on any other base as not approved.
### Changed
- Base image checks on Docker Hub and Microsoft's registry are kept on disk for a day, so they aren't asked again after a restart. **Clear cache** clears them too.
### Fixed
- A registry added by hand with repositories listed now shows only those repositories, without asking the registry for its full list. Adding `mcr.microsoft.com` with `dotnet/aspnet` showed all of Microsoft's nearly 3,900 repositories, and could stay on "Loading…" for a long time after saving Settings.

## [0.1.11] - 2026-10-13
### Added
- Base image: a tag's details show the image it was built on, its Dockerfile's last `FROM`. Images rarely record it, so PullEmUp recognizes it from what the image inherited (its version settings and build steps) and confirms it on Docker Hub or Microsoft's registry by comparing layers. Docker Hub is asked through its website API, which doesn't count toward the pull limit. Recognized: python, node, eclipse-temurin, nginx, php and ruby (each with its variants: slim or full, JRE or JDK, fpm or apache, Debian or Alpine...), Microsoft's .NET images (sdk, aspnet, runtime), and Debian, Ubuntu and Alpine themselves. A base recorded by the build is used when there is one.
- Base image updates: the base is compared with the tag that follows its newer builds (`python:3.11-slim-trixie` for `python:3.11.15-slim-trixie`), which says whether it's the current build, or whether a newer version or a rebuild with newer OS packages is out.
- The layers inspector marks where the image's own build steps start, and has two copy buttons: **Copy as Dockerfile**, `FROM` the base image and then only the image's own steps, close to the original Dockerfile, with comments where the history doesn't record something; and **Copy full build history**, every step as recorded.
- Images used by the build: for images built with BuildKit provenance, Signatures and provenance lists every image the build pulled, such as the SDK or compiler stage of a multi-stage build, which leaves no other trace in the image. The Dockerfile frontend and SBOM scanner are labeled as such, and each image can be opened.
- Raw tab: a tag's index, manifest and config exactly as the registry sent them, colored and copyable, including annotations no other tab shows. For local images, the engine's `docker image inspect`.
### Changed
- Settings replaces the Sources panel: a dialog with sections for Sources, Registries (AWS regions, Docker Hub, registries added by hand), Vulnerability scan, Base images and Data (cache), opened with the gear button or Ctrl+,. Changes apply when you click Save, and Cancel discards them.
### Fixed
- Provenance without Git information no longer shows one of the build's images as the source repository.

## [0.1.10] - 2026-10-13
### Added
- Help: a Help button in the header opens a window with PullEmUp's documentation: what each screen shows, where each piece of information comes from, what's kept on your computer and what's sent, how the vulnerability scanner works, troubleshooting, and these release notes. The vulnerability report, the Sources panel and error messages link to the page that explains them.
- Vulnerabilities in the tag list: a new Vulns column shows each tag's last scan by PullEmUp, with the worst severity found and how many (a check when nothing was found), dimmed when the scan is over 30 days old. Hover it for the counts per platform, how many have a fix and the scan date; click it to open the report. Sort by it to see the worst tags first. It only reads saved scans, so it downloads nothing.
- The scanner checks RPM-based images: Red Hat and UBI, AlmaLinux, Rocky Linux, SUSE and BCI, openSUSE Leap and Tumbleweed, Azure Linux (CBL-Mariner), openEuler and Mageia. For images whose OS packages OSV.dev doesn't cover (Fedora, Amazon Linux, CentOS, Oracle Linux), the report says so, and the rest of the image is still checked.
- The scanner checks Go programs (the Go version and modules built into every Go binary) and Rust programs built with `cargo auditable`. Rust programs without a list of their crates are counted in the report, so you know they weren't checked.
- The scanner checks .NET apps: the NuGet packages and runtime packs listed in their `.deps.json` files.
- The scanner checks Java runtimes (Temurin, Corretto, Zulu and others) by the version in their `release` file. Java built by the distribution is checked as an OS package instead, and a runtime's own jars are no longer reported as unidentified Java archives.
- Tag details can sit below the tag list instead of beside it: switch with the new button in the header. Each arrangement keeps its own pane sizes.
- Search offers to add an image reference you paste that isn't in your sources: it pins a Docker Hub image, or adds the repository to its registry in Sources, then opens it. References in GHCR, ECR or ACR registries already in the tree open directly.
### Fixed
- After a refresh, tag details no longer fail with "Unknown Azure source" while sources are still being detected.
- A thin strip of the Sources panel, search and the Copy menu could not be clicked where a pane divider ran behind it.

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
