Clean transcripts
================
Nathaniel Olin
Mon May 20 21:49:52 2019

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

Load data
=========

Metadata
--------

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

``` r
dat <- dat %>%
  filter(filename != "2.0_06_heres_your_bribe_tip.txt")
```

Transcripts
-----------

``` r
dat <- dat %>%
  mutate(text = sapply(
    file.path("transcripts", filename), 
    readLines, encoding = "UTF-8"))

dat %>%
  filter(season_number == 4 & episode_number == 1) %>%
  select(text) %>%
  unlist() %>%
  .[100:120] %>%
  paste(collapse = "\n") %>%
  writeLines()
```

    ## 
    ## 
    ## AUSTIN: And then your jam, which is— so your group will be playing a group of secret agents/secret police/investigators for a group that is entrusted with protecting the remaining cities and ships of the Divine Fleet. But besides that, you also have some free time where you do other stuff. I think of your group as, not on reserve necessarily, but there's definitely a Power Rangers-y like, "Oh shit, we gotta go do a thing," but not every day. So your jam is what you do when you're not doing that. So what is Signet's jam?
    ## 
    ## 
    ## JANINE: Signet's jam is basically attending to her duties as part of the religion. Because she used to pilot a Divine and that Divine is one of the ones that's no longer around, people like her are kind of in short supply. So she's put out as sort of a figure, not a figure head, but just a thing that people can connect to the faith through, because there aren't that many points of connection left.
    ## 
    ## 
    ## AUSTIN: Right. We didn't mention what playbook it was by mistake.
    ## 
    ## 
    ## JANINE: Oops. She is the Onomastic.
    ## 
    ## 
    ## AUSTIN: Which is kind of like the thing you are, which is you are the last of a religious order, basically. 
    ## 
    ## 
    ## JANINE: Yes.
    ## 
    ## 
    ## AUSTIN: And the religious order in question that we figured out was that you are the last Excerpt from the sort of Divine that you had, is that what we ended up on?

Clean transcripts
=================
