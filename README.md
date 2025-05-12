# tidyverse-blimp-docker

This repository contains a `Dockerfile` (and Apptainer/Singularity definition) based on `rocker:tidyverse` that:

- Downloads the static `blimp` binary archive from https://blimp-stats.github.io
- Extracts the `blimp` binary from that archive to `/usr/local/bin`
- Installs the latest version of `rblimp` from its [GitHub](https://github.com/blimp-stats/rblimp)

## Using the prebuilt container

A prebuilt version containing the latest tidyverse and blimp versions is available at [`maouw/tidyverse-blimp-docker`](https://hub.docker.com/r/maouw/tidyverse-blimp-docker/tags).

### Docker

To use the latest version on Docker, run:

```base
docker pull maouw/tidyverse-blimp-docker:latest
```

Or, for the version uploaded on May 12, 2025:

```bash
# The `rocker/tidyverse` and `blimp` versions are separated by an underscore:
docker pull maouw/tidyverse-blimp-docker:4.5_3.2.15
```

### Apptainer/Singularity

To use the latest version on Apptainer/Singularity, run:

```bash
# (Replace "apptainer" with "singularity" if necessary)
apptainer pull docker://maouw/tidyverse-blimp-docker:latest
```

Or, for the version uploaded on May 12, 2025:

```bash
# The `rocker/tidyverse` and `blimp` versions are separated by an underscore:
apptainer pull docker://maouw/tidyverse-blimp-docker:4.5_3.2.15
```

## Building the container from scratch

You may also build the container yourself using Docker or Apptainer/Singularity.

### Build arguments

The following build arguments can be specified to configure the container:

- `ROCKER_BASE`: The base image (default: `rocker/tidyverse:latest`)
- `BLIMP_BINARY_URL`: The URL of the static `blimp` binary archive (default: `https://blimp-stats.github.io/linux/src/blimp_binary.tar.gz`)
- `RBLIMP_GITHUB_REPO`: The repository specification, with optional tag or branch, for `rblimp` (default: `blimp-stats/rblimp@main`)

### Docker

To build the image with Docker, clone this repository and run the following
command inside the repository directory:

```bash
# (Replace "rocker-tidyverse-blimp" with a tag of your choice if you want)
docker build --tag rocker-tidyverse-blimp:latest -f Dockerfile .
```

To use a specific version of `rocker/tidyverse`, you can use `--build-arg`:

```bash
docker build --build-arg ROCKER_BASE=rocker/tidyverse:4.5 --tag rocker-tidyverse-blimp:4.5 -f Dockerfile .
```
### Apptainer/Singularity

An Apptainer/Singularity container can be built as
follows:

```bash
# (Replace "apptainer" with "singularity" if necessary)
apptainer build "$(basename "$PWD").sif" Singularity
```

Or, with build arguments:

```bash
apptainer build --build-arg ROCKER_BASE=rocker/tidyverse:4.5 "$(basename $PWD).sif" Singularity
```

## Extending the container

To extend this container, edit `Dockerfile` or `Singularity` as necessary or use `maouw/rocker-tidyverse-blimp` as the base image for a new container.

### Adding Ubuntu packages

Ubuntu packages can be added to the container like so. If you're using
Apptainer/Singularity, add these commands to the `%post` section, making sure to remove the prefix "`RUN`".

```bash
RUN export DEBIAN_FRONTEND=noninteractive && \
  apt-get update -qq && \
  apt-get install -yq --no-install-recommends build-essential cmake libssl-dev && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*
```

### Adding R packages

The `rocker` images come with the programs `install2.r` and `installGithub.r` to
install R packages from the command line. Here is an example of their use:

```bash
install2.r insight # Install "insight" from CRAN
installGithub.r easystats/datawizard # Install "datawizard" from GitHub
```

Refer to the [rocker documentation](https://rocker-project.org/use/extending.html) for more about extending the `rocker` images.

