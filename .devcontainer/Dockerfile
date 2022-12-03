FROM rocker/r-ver:4.0.3

RUN apt-get update \
	&& apt-get install -y --no-install-recommends \
  	apt-utils \
  	ed \
  	libnlopt-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/

# Install rstan
RUN install2.r --error --deps TRUE rstan

RUN apt-get update && apt-get install -y  --no-install-recommends\
   git-core \
   libcurl4-openssl-dev \
   libgit2-dev \
   libssh2-1-dev \
   libicu-dev \
   libpng-dev \
   libudunits2-dev \
   zlib1g-dev \
   libgdal-dev \
   libproj-dev \
   libgeos-dev \
   libssl-dev \
   libxml2-dev \
   make pandoc \
   pandoc-citeproc \
   sqlite3 \
   libsqlite3-dev \
   libnode-dev \
   && rm -rf /var/lib/apt/lists/*


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
RUN Rscript -e 'remotes::install_version("leafgl",upgrade="never", version = "0.1.1")'
RUN Rscript -e 'remotes::install_version("tidyr",upgrade="never", version = "1.1.2")'
