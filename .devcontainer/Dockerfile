# [Choice] R version: 4, 4.2, 4.1, 4.0
#ARG VARIANT=4.2
# # [Choice] Base image. Minimal (r-ver), tidyverse installed (tidyverse), or full image (binder): rocker/r-ver, rocker/tidyverse, rocker/binder
#ARG BASE_IMAGE=rocker/tidyverse
#FROM ${BASE_IMAGE}:${VARIANT}
# FROM eddelbuettel/r2u:jammy
FROM rstan/rstan:latest
RUN apt update -qq \
     && apt install --yes --no-install-recommends git

# [Option] Install zsh
ARG INSTALL_ZSH="true"
# [Option] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="true"

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

# [Optional] Uncomment this section to install additional R packages.

# RUN Rscript --vanilla -e ' devtools::install_version("rstan", version = "2.21.1", repos = "http://cran.us.r-project.org", dependencies = TRUE) '
# RUN Rscript --vanilla -e ' devtools::install_version("StanHeaders", version = "2.21.0.7", repos = "http://cran.us.r-project.org", dependencies = TRUE) '
# https://twitter.com/HenrikSingmann/status/1287032068053106688
# RUN Rscript --vanilla -e 'options(repos = c("CRAN" = "http://cran.us.r-project.org")) ; devtools::install_version("StanHeaders", version = "2.19.2", upgrade = "never", repos = c("https://mc-stan.org/r-packages/", getOption("repos") ) ) '
# RUN Rscript --vanilla -e 'options(repos = c("CRAN" = "http://cran.us.r-project.org")) ;  devtools::install_version("rstan",      version = "2.19.3", upgrade = "never", repos = c("https://mc-stan.org/r-packages/", getOption("repos") ) ) '
# sometimes runs but R session crashes at the end 
# RUN Rscript --vanilla -e 'options(repos = c("CRAN" = "http://cran.us.r-project.org")) ; devtools::install_version("StanHeaders", version = "2.30.0", upgrade = "never", repos = c("https://mc-stan.org/r-packages/", getOption("repos") ) ) '
# RUN Rscript --vanilla -e 'options(repos = c("CRAN" = "http://cran.us.r-project.org")) ;  devtools::install_version("rstan",      version = "2.30.0", upgrade = "never", repos = c("https://mc-stan.org/r-packages/", getOption("repos") ) ) '
# try 
# packageVersion(“rstan”)  [1] ‘2.19.3’
# install_version("StanHeaders", version = "2.19.0", repos = "http://cran.us.r-project.org")

# https://mc-stan.org/r-packages/
# RUN Rscript --vanilla -e ' install.packages("rstan", repos = c("https://mc-stan.org/r-packages/", getOption("repos"))) '
# RUN Rscript --vanilla -e ' install.packages(c("StanHeaders","rstan"), repos = c("https://mc-stan.org/r-packages/", getOption("repos")), type="source", lib = "/usr/lib/R/site-library") ' 


# RUN Rscript --vanilla -e ' install.packages("cmdstanr", repos = c("https://mc-stan.org/r-packages/", "http://cran.us.r-project.org")) '


RUN install2.r --error --skipinstalled --ncpus -2 \
    devtools V8 brms jsonlite \
    tidyverse here 
    # BiocManager 
    # posterior (bayesplot auto installed)
    # --deps StanHeaders rstan \
    # --deps brms 
    # pacman here XML RCurl furrr glue \
    # rstan brms
    # brms # \ # StanHeaders rstantools

USER rstudio
# savehistory is NA under radian ? 
#   WARNING: this might include sensitive data / passwords?
# perhaps save to ./.radian_history ?
#   ./.Rhistory (not ~/.Rhistory) so .Rhistory is stored
# ENV R_HISTFILE "./.Rhistory"
# USER rstudio saves to /home/rstudio/.cmdstan/ and is readable
# set_cmdstan_path is now in ./.Rprofile
# RUN Rscript --vanilla -e ' library(cmdstanr) ; install_cmdstan(cores = 1, overwrite=TRUE) '

