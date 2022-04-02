FROM rocker/rstudio:4.1.3

ENV MRAN_SNAPSHOT_DATE=2022-03-23

# Install apt packages (including Japanese fonts)
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
     ssh \
     libxt-dev \
     libxml2-dev \
     libgit2-dev \
     fonts-ipaexfont \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# Install R packages
RUN install2.r -e -s \
  -n -1 \
  -r https://mran.microsoft.com/snapshot/${MRAN_SNAPSHOT_DATE} \
     renv \
     rmarkdown \
  && rm -rf /tmp/downloaded_packages/ /tmp/*.rds

# Create directories for renv package cache and renv library root
# Add github.com to known_hosts in SSH
RUN mkdir -p /home/rstudio/.local/share/renv/cache \
  && mkdir -p /home/rstudio/.ssh \
  && mkdir -p /home/rstudio/.renv/library \
  && ssh-keyscan -t ed25519 github.com > /home/rstudio/.ssh/known_hosts \
  && chown -R rstudio:rstudio /home/rstudio \
  && chmod 700 /home/rstudio/.ssh

# Copy the configuration files
COPY --chown=rstudio:rstudio .Renviron /home/rstudio/.Renviron
COPY --chown=rstudio:rstudio .ssh/config /home/rstudio/.ssh/config

# Copy the startup script
COPY startup.sh /startup

# Change the startup command
CMD [ "/startup" ]
