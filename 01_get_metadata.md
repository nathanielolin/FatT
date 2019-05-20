Get FatT transcript metadata
================
Nathaniel Olin
Mon May 20 17:55:58 2019

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------------------------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.1.0       v purrr   0.3.2  
    ## v tibble  2.1.1       v dplyr   0.8.0.1
    ## v tidyr   0.8.3       v stringr 1.4.0  
    ## v readr   1.3.1       v forcats 0.4.0

    ## -- Conflicts ------------------------------------------------------------------------------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(googledrive)
library(googlesheets4)
sheets_auth(email = Sys.getenv("email"))
```

Get list of episodes
====================

``` r
dat <- as_id("17P0ijdfHppGEkiq0zyMbJNVQrLAwRs9WhkDqA-dkBkk") %>%
  read_sheet(sheet = "Completed episode list")
```

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

Write out
=========

``` r
dat %>%
  select(season, episode_number, episode_name, minutes, url) %>%
  print() %>%
  write_csv("meta.csv")
```

    ## # A tibble: 152 x 5
    ##    season    episode_number episode_name       minutes url                 
    ##    <chr>              <dbl> <chr>                <dbl> <chr>               
    ##  1 Autumn i~              0 We're Not Calling~     176 https://drive.googl~
    ##  2 Autumn i~              1 We Have Not Yet B~      85 https://drive.googl~
    ##  3 Autumn i~              2 You Found Out Wha~     110 https://drive.googl~
    ##  4 Autumn i~              3 A Podcast About L~      87 https://drive.googl~
    ##  5 Autumn i~              4 Is It Time Alread~     145 https://drive.googl~
    ##  6 Autumn i~              5 What's a Good Nam~      94 https://drive.googl~
    ##  7 Autumn i~              8 On The Tip Of You~      98 https://drive.googl~
    ##  8 Autumn i~              9 I'm Not Happy Wit~      87 https://drive.googl~
    ##  9 Autumn i~             10 Chekhov's Torture~      56 https://drive.googl~
    ## 10 Autumn i~             NA I've Killed Monst~     198 https://drive.googl~
    ## # ... with 142 more rows
