
# TODO: mv ~/.Rprofile . # to store .Rprofile inside github repo
# WARNING: save to /home/vscode/.Rprofile 
#   but it will NOT be save by git to the github repo?




# - If you are in a container environment, please consider adding the
#  following to your configuration to silence this warning
#   options(bspm.sudo = TRUE)
# WARNING: put options into ./.Rprofile else they wont persist between R sessions

options(
# https://community.rstudio.com/t/not-able-to-install-brms-rstan-package-on-linux-r-server/96249/2
  # bspm: Bridge to System Package Manager
  # https://cran.r-project.org/web/packages/bspm/bspm.pdf
  # if you want to fall back to sudo in a non-interactive session, 
  # you need to set options(bspm.sudo=TRUE).
  bspm.sudo = TRUE, # options()$bspm.sudo
  # For execution on a _local_, multicore CPU with excess RAM we recommend calling
  mc.cores = parallel::detectCores(), 
  # or rstan_options(auto_write = TRUE) ?
  auto_write = FALSE # TRUE ?
) 
# options(bspm.sudo = NULL, mc.cores = NULL, auto_write = NULL)
# options()$auto_write ; options()$mc.cores ; options()$bspm.sudo

eval({
  r = getOption("repos") 
  r["CRAN"] = "http://cran.us.r-project.org" 
  options(repos = r)
})
## options(repos = c(c("CRAN" = "http://cran.us.r-project.org"), getOption("repos"))
