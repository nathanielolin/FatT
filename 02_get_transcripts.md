Get FatT transcripts
================
Nathaniel Olin
Mon May 20 21:30:26 2019

``` r
library(tidyverse)
```

    ## Registered S3 methods overwritten by 'ggplot2':
    ##   method         from 
    ##   [.quosures     rlang
    ##   c.quosures     rlang
    ##   print.quosures rlang

    ## Registered S3 method overwritten by 'rvest':
    ##   method            from
    ##   read_xml.response xml2

    ## -- Attaching packages -------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.1.0     v purrr   0.3.2
    ## v tibble  2.1.1     v dplyr   0.7.8
    ## v tidyr   0.8.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.3.0

    ## -- Conflicts ----------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(googledrive)
library(googlesheets4)
sheets_auth(email = Sys.getenv("email"))
```

    ## Registered S3 method overwritten by 'openssl':
    ##   method      from
    ##   print.bytes Rcpp

``` r
if(! "transcripts" %in% list.files())
  dir.create("transcripts")
```

Download transcripts
====================

Get episode URLs
----------------

``` r
dat <- read_csv("meta.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   filename = col_character(),
    ##   season_number = col_double(),
    ##   season = col_character(),
    ##   episode_number = col_double(),
    ##   episode_name = col_character(),
    ##   minutes = col_double(),
    ##   url = col_character()
    ## )

Save transcripts to file
------------------------

``` r
# Remove locked file
dat$url[dat$url == "https://drive.google.com/open?id=19CSVitxMRtKKXr8m4swuczgAHtwcTT6sEEZaxSzQpgI"] <- NA
    
for(i in seq_along(dat$url)){
  if(! dat$filename[i] %in% list.files("transcripts") & !is.na(dat$url[i]))
    drive_download(as_id(dat$url[i]), path = paste0("transcripts/", dat$filename[i]))
}
```
