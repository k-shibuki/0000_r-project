options(
  warn = 1, 
  warnPartialMatchArgs = TRUE, 
  warnPartialMatchAttr = TRUE, 
  warnPartialMatchDollar = TRUE, 
  showWarnCalls = TRUE, 
  showErrorCalls = TRUE
)

tryCatch({ renv::load(); renv::restore(prompt = FALSE); }, error = function(e) { source("renv/activate.R") })
