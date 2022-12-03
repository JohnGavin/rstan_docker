FROM rocker/r-ver:4.0.3

RUN apt-get update \
	&& apt-get install -y --no-install-recommends \
  	apt-utils \
  	ed \
  	libnlopt-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG USERNAME=rstudio
ARG USER_UID=1001
ARG USER_GID=$USER_UID
USER root
COPY library-scripts/*.sh /tmp/library-scripts/
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true" \
    && usermod -a -G staff ${USERNAME} \
    && apt-get -y install \
        python3-pip \
        libgit2-dev \
        libcurl4-openssl-dev \
        libssl-dev \
        libxml2-dev \
        libxt-dev \
        libfontconfig1-dev \
        libcairo2-dev \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts \
    && python3 -m pip --no-cache-dir install radian \
    && install2.r --error --skipinstalled --ncpus -1 \
        devtools \
        languageserver \
        httpgd \
    && rm -rf /tmp/downloaded_packages

# VSCode R Debugger dependency. Install the latest release version from GitHub without using GitHub API.
# See https://github.com/microsoft/vscode-dev-containers/issues/1032
RUN export TAG=$(git ls-remote --tags --refs --sort='version:refname' https://github.com/ManuelHentschel/vscDebugger v\* | tail -n 1 | cut --delimiter='/' --fields=3) \
    && Rscript -e "remotes::install_git('https://github.com/ManuelHentschel/vscDebugger.git', ref = '"${TAG}"', dependencies = FALSE)"

# R Session watcher settings.
# See more details: https://github.com/REditorSupport/vscode-R/wiki/R-Session-watcher
RUN echo 'if (interactive() && Sys.getenv("TERM_PROGRAM") == "vscode") source(file.path(Sys.getenv("HOME"), ".vscode-R", "init.R"))' >>"${R_HOME}/etc/Rprofile.site"

# [Optional] Enable vscode-jupyter support
ARG ENABLE_JUPYTER="false"
RUN if [ "${ENABLE_JUPYTER}" = "true" ]; then \
        if [ -z "$(dpkg --get-selections | grep libzmq3-dev)" ]; then \
            apt-get update \
            && export DEBIAN_FRONTEND=noninteractive \
            && apt-get -y install --no-install-recommends libzmq3-dev \
            && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/*; \
        fi \
        && install2.r --error --skipinstalled --ncpus -1 IRkernel \
        && rm -rf /tmp/downloaded_packages \
        && python3 -m pip --no-cache-dir install jupyter \
        && R --vanilla -s -e 'IRkernel::installspec(user = FALSE)'; \
    fi

# [Optional] Uncomment this section to install additional OS packages.

# https://github.com/stan-dev/rstan/wiki/Configuring-C-Toolchain-for-Linux#debian-testing
# https://github.com/jeroen/V8#getting-started
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        libgdal-dev libopenblas-dev libnode-dev libudunits2-dev  \
        pandoc-citeproc pandoc make \
        libv8-dev \
        libglpk-dev 
        # r-cran-rstan
        # libgfortran4

RUN apt-get update && apt-get install -y  --no-install-recommends\
   git-core \
   libssh2-1-dev \
   libicu-dev \
   libpng-dev \
   libudunits2-dev \
   zlib1g-dev \
   libproj-dev \
   make pandoc \
   pandoc-citeproc \
   libnode-dev \
   && rm -rf /var/lib/apt/lists/*
   # libgit2-dev \
   # libcurl4-openssl-dev \
   # libxml2-dev \
   # libssl-dev \
   #   libgeos-dev \
   # sqlite3 \
   # libsqlite3-dev \
   # libgdal-dev \


# Install rstan
RUN install2.r --error --deps TRUE rstan


RUN echo "options(repos = c(CRAN = 'https://packagemanager.rstudio.com/all/latest'), download.file.method = 'libcurl')" >> /usr/local/lib/R/etc/Rprofile.site
RUN R -e 'install.packages("remotes")'
    
RUN Rscript -e 'remotes::install_version("glue",upgrade="never", version = "1.4.2")'
RUN Rscript -e 'remotes::install_version("htmltools",upgrade="never", version = "0.5.1.1")'
RUN Rscript -e 'remotes::install_version("testthat",upgrade="never", version = "3.0.0")'
RUN Rscript -e 'remotes::install_version("tidyverse",upgrade="never", version = "1.3.0")'
RUN Rscript -e 'remotes::install_version("ggplot2",upgrade="never", version = "3.3.2")'
RUN Rscript -e 'remotes::install_version("RCurl",upgrade="never", version = "1.98-1.2")'
RUN Rscript -e 'remotes::install_version("rmarkdown",upgrade="never", version = "2.5")'
RUN Rscript -e 'remotes::install_version("knitr",upgrade="never", version = "1.30")'
RUN Rscript -e 'remotes::install_version("tidyr",upgrade="never", version = "1.1.2")'
RUN Rscript -e 'remotes::install_version("brms",upgrade="never", version = "2.18.0")'
RUN Rscript -e 'remotes::install_version("here",upgrade="never", version = "1.0.1")'

RUN install2.r --error --skipinstalled --ncpus -2 \
   V8 jsonlite 
     # tidyverse here 