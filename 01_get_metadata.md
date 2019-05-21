Get FatT transcript metadata
================
Nathaniel Olin
Mon May 20 21:30:03 2019

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

Get list of episodes
====================

``` r
dat <- as_id("17P0ijdfHppGEkiq0zyMbJNVQrLAwRs9WhkDqA-dkBkk") %>%
  read_sheet(sheet = "Completed episode list")
```

    ## Auto-refreshing stale OAuth token.

    ## Reading from 'FATT TRANSCRIPTS'

    ## Range "'Completed episode list'"

    ## New names:
    ## * `` -> ...1
    ## * `` -> ...4
    ## * `` -> ...5
    ## * `` -> ...7

``` r
# Check consistent format of dataframe
stopifnot(ncol(dat) == 7)
```

Clean up list
=============

``` r
names(dat) <- c("episode_name", "minutes", "transcriber", "started", "complete", "url", "notes")
# Drop blank row and columns not used in analysis
dat <- dat %>% 
  filter(!is.na(episode_name)) %>%
  select(episode_name, minutes, url) %>%
  mutate(episode_name = str_replace_all(episode_name, "â€™", "'"))

dat
```

    ## # A tibble: 180 x 3
    ##    episode_name                     minutes url                            
    ##    <chr>                              <dbl> <chr>                          
    ##  1 An Introduction to Friends at t~      13 https://drive.google.com/open?~
    ##  2 Autumn in Hieron 00: We're Not ~     176 https://drive.google.com/open?~
    ##  3 Autumn in Hieron 01: We Have No~      85 https://drive.google.com/open?~
    ##  4 Autumn in Hieron 02: You Found ~     110 https://drive.google.com/open?~
    ##  5 Autumn in Hieron 03: A Podcast ~      87 https://drive.google.com/open?~
    ##  6 Autumn in Hieron 04: Is It Time~     145 https://drive.google.com/open?~
    ##  7 Autumn in Hieron 05: What's a G~      94 https://drive.google.com/open?~
    ##  8 Autumn in Hieron 08: On The Tip~      98 https://drive.google.com/open?~
    ##  9 Autumn in Hieron 09: I'm Not Ha~      87 https://drive.google.com/open?~
    ## 10 Autumn in Hieron 10: Chekhov's ~      56 https://drive.google.com/open?~
    ## # ... with 170 more rows

Code additional metadata
========================

Code season names
-----------------

``` r
dat <- dat %>%
  mutate(season = str_replace_all(
    episode_name, "^(.*?)( [0-9-]*):.*", "\\1"))

# Fix unnumbered episode
dat$season[dat$season == "Autumn in Hieron: Holiday Special"] <- "Autumn in Hieron"

# Stick to main seasons
dat <- dat %>%
  filter(season %in% c(
    "Autumn in Hieron", "COUNTER/Weight", 
    "Marielda", "Winter in Hieron", 
    "Twilight Mirage", "Spring in Hieron"))

dat %>% count(season)
```

    ## # A tibble: 6 x 2
    ##   season               n
    ##   <chr>            <int>
    ## 1 Autumn in Hieron    22
    ## 2 COUNTER/Weight      28
    ## 3 Marielda            12
    ## 4 Spring in Hieron    28
    ## 5 Twilight Mirage     41
    ## 6 Winter in Hieron    21

Code season numbers
-------------------

``` r
dat <- dat %>%
  mutate(season_number = recode(
    season,
    "Autumn in Hieron" = 1,
    "COUNTER/Weight" = 2,
    "Marielda" = 2.5,
    "Winter in Hieron" = 3,
    "Twilight Mirage" = 4,
    "Spring in Hieron" = 5))
```

Code episode numbers
--------------------

``` r
dat <- dat %>%
  mutate(episode_number = as.numeric(str_replace_all(
    episode_name, "^(.*? )([0-9-]*):.*", "\\2")))

dat$episode_number[str_detect(dat$episode_name, "Autumn in Hieron: Holiday Special")] <- NA
```

Code episode names
------------------

``` r
dat <- dat %>%
  mutate(episode_name = str_replace_all(
    episode_name, "^(.*? )([0-9-]*): (.*)", "\\3"))
```

Code filename (for later)
-------------------------

``` r
dat <- dat %>%
  mutate(filename = sprintf(
    "%02.1f_%02.0f_%s.txt", 
    season_number, 
    episode_number, 
    str_replace_all(
      tolower(episode_name), 
      pattern = c(" " = "_", "[:!?'(),.]" = ""))))
```

Write out
=========

``` r
dat %>%
  select(filename, season_number, season, episode_number, episode_name, minutes, url) %>%
  print() %>%
  write_csv("meta.csv")
```

    ## # A tibble: 152 x 7
    ##    filename  season_number season episode_number episode_name minutes url  
    ##    <chr>             <dbl> <chr>           <dbl> <chr>          <dbl> <chr>
    ##  1 1.0_00_w~             1 Autum~              0 We're Not C~     176 http~
    ##  2 1.0_01_w~             1 Autum~              1 We Have Not~      85 http~
    ##  3 1.0_02_y~             1 Autum~              2 You Found O~     110 http~
    ##  4 1.0_03_a~             1 Autum~              3 A Podcast A~      87 http~
    ##  5 1.0_04_i~             1 Autum~              4 Is It Time ~     145 http~
    ##  6 1.0_05_w~             1 Autum~              5 What's a Go~      94 http~
    ##  7 1.0_08_o~             1 Autum~              8 On The Tip ~      98 http~
    ##  8 1.0_09_i~             1 Autum~              9 I'm Not Hap~      87 http~
    ##  9 1.0_10_c~             1 Autum~             10 Chekhov's T~      56 http~
    ## 10 1.0_NA_i~             1 Autum~             NA I've Killed~     198 http~
    ## # ... with 142 more rows
