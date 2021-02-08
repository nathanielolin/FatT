Get FatT transcripts
================
Nathaniel Olin
Mon Feb 08 10:37:12 2021

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.3     v purrr   0.3.4
    ## v tibble  3.0.6     v dplyr   1.0.3
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.4.0     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(googledrive)
drive_deauth() # Public sheet does not require auth

if(! "transcripts" %in% list.files())
  dir.create("transcripts")
```

# Download transcripts

## Get episode URLs

``` r
dat <- read_csv("meta.csv") %>%
  filter(complete)
```

    ## 
    ## -- Column specification --------------------------------------------------------
    ## cols(
    ##   filename = col_character(),
    ##   season_id = col_double(),
    ##   season = col_character(),
    ##   episode_id = col_double(),
    ##   episode = col_character(),
    ##   doc = col_character(),
    ##   complete = col_logical()
    ## )

## Save transcripts to file

``` r
for(i in seq_along(dat$doc)){
  if(! dat$filename[i] %in% list.files("transcripts") & 
     !is.na(dat$doc[i]))
    drive_download(
      as_id(dat$doc[i]), 
      path = paste0("transcripts/", dat$filename[i])
    )
}
```

    ## File downloaded:
    ##   * PARTIZAN 27: Millennium Break: She said, "To Win We Must Play!"
    ## Saved locally as:
    ##   * transcripts/partizan_27_millennium_break_she_said_to_win_we_must_play.txt

    ## File downloaded:
    ##   * PARTIZAN 28: Millennium Break: The Storm Over Cruciat
    ## Saved locally as:
    ##   * transcripts/partizan_28_millennium_break_the_storm_over_cruciat.txt

    ## File downloaded:
    ##   * PARTIZAN 31: The Grand Premiere
    ## Saved locally as:
    ##   * transcripts/partizan_31_the_grand_premiere.txt

    ## File downloaded:
    ##   * PARTIZAN 33: A Single Shot
    ## Saved locally as:
    ##   * transcripts/partizan_33_a_single_shot.txt

    ## File downloaded:
    ##   * PARTIZAN 36: The Witch in the Glass Pt. 2
    ## Saved locally as:
    ##   * transcripts/partizan_36_the_witch_in_the_glass_pt_2.txt

    ## File downloaded:
    ##   * PARTIZAN 38: The Red Light
    ## Saved locally as:
    ##   * transcripts/partizan_38_the_red_light.txt

    ## File downloaded:
    ##   * PARTIZAN 39: Escape Velocity
    ## Saved locally as:
    ##   * transcripts/partizan_39_escape_velocity.txt

    ## File downloaded:
    ##   * PARTIZAN 40 - Engine Burn
    ## Saved locally as:
    ##   * transcripts/partizan_40_engine_burn.txt
