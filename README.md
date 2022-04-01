R projects to reproduce research results
==================================================

This is an R project to reproduce the research results in R using Git/GitHub, Docker, RStudio, and R package `renv`.

The DOI of the research article that has relevance to this repository is [10.1000/xyz123](https://).

## Features of this R project
- Git/GitHub
   - [Git](https://git-scm.com/) for source code version control
   - [GitHub](https://github.com/) for publish datasets and source code
- Docker/Docker Compose
   - Using Docker container image [`rocker/rstudio`](https://hub.docker.com/r/rocker/rstudio) containing R and RStudio Server
   - Reproduce the same working environment, including Linux distribution and libraries, R and RStudio versions, etc.
   - Manage container startup settings in the `compose.yml` file.
- Make
   - Customize the startup commands with the `Makefile`, to start and stop the working environment with just `make up` and `make down` commands.
- renv
   - [renv](https://rstudio.github.io/renv/index.html) for creating project-specific libraries and managing dependencies
   - Fix the package version in the `renv.lock` file
   - Mount package cache from Docker hosts to reduce storage usage and installation time
- dotfiles
   - Manage RStudio and Git global configuration files in the dotfiles directory
   - Mount and use configuration files from Docker hosts to containers
   - Settings are maintained even when the container is stopped

## Usage
### Initial settings
1. Install Docker, Git, and Make.
2. Set up a connection to GitHub.
3. Clone the GitHub repository of this R project to an appropriate location in your local environment.

### From startup to shutdown of the work environment
The following steps assume that the Docker daemon is running.

1. When you start your work, Run the `make up` command in the directory of this R project.
2. Open <http://localhost:8787> in a browser and connect to RStudio Server.
3. Open the [`research.Rproj`](./research.Rproj) file in RStudio Server.
4. Open the code folder on RStudio Server and perform analysis.
5. When you finish your work, run the `make down` command in this R project directory.

Note: To see a list of make commands, run the `make` command in the directory of this R project.

### Package management with renv
- Project-specific library (private library) is created by renv.
- To record the status of the library, use the `renv::snapshot()` function. At this time, meta-information on packages installed in the library is stored in [`renv.lock`](./renv.lock) file.
- The `renv::restore()` function can be used to restore the library based on the meta-information recorded in the `renv.lock` file.
- The entity files of installed packages are stored in the package cache on the Docker host (outside the container). When the container is stopped and restarted, the files stored in the package cache are reused.

## Change environment variables (optional)
### Create a `.env` file
To set the environment variables, you need to copy the [`.env.example`](./.env.example) file and create a `.env` file based on it.

Note: A `.env` file has been excluded from Git management by a [`.gitignore`](./.gitignore) file in advance.

### Sharing the renv package cache between projects

By default, [renv's package cache](https://rstudio.github.io/renv/articles/renv.html#cache-1) is located in [`dev/renv/cache/`](./dev/renv/cache).
However, this package cache is used on a project-specific basis, so saved package files are not reused in another project.

To reuse downloaded packages in other projects, create one renv package cache directory on your machine that is universal between projects. 

Once the universal package cache is created, specify its path in the environment variable `RENV_PATHS_CACHE_HOST` in the `.env` file.

### Sharing settings between projects via universal dotfiles
By default, RStudio and Git global settings are configured in [`dev/dotfiles/`](./dev/dotfiles) folder, such as `.config/rstudio/rstudio-prefs.json` or `.gitconfig`.
However, this folder is used on a project-specific basis, so the settings are not reflected in other projects.

If you want to share your settings among multiple projects, copy the project-specific [`dev/dotfiles/`](./dev/dotfiles) and create a universal dotfiles directory (e.g. `~/rproject-dotfiles`) on your machine based on it.
Once the universal dotfiles directory is created, specify its path in the environment variable `DOTFILES_ROOT` in the `.env` file.

In addition, dotfiles can be managed as a GitHub repository to synchronize settings among multiple machines.

### Git and GitHub
If you want to use Git on RStudio in a container, configure the following settings.

#### Setting up your username and email address
Open the Terminal pane in RStudio and run the following commands to set up a user name and email address for Git in the container:

```sh
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

This configuration is written to the `.gitconfig` file in `dotfiles`.

#### Setting up SSH private key
Store the SSH private key for connecting to GitHub on the Docker host machine. Specify the file path of the private key (e.g. `~/.ssh/id_ed25519`) in the `GITHUB_SSH_IDENTITY` environment variable in `.env` to mount the private key in the container and allow SSH connections to GitHub from inside the container.

### path to mount your project.
By default, this project folder is mounted in `/home/rstudio/project` in the container.
You can change the path to the mount location with the `.env` environment variable `PROJECT_PATH_CONTAINER`.
