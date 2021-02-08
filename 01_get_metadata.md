Get FatT transcript metadata
================
Nathaniel Olin
Mon Feb 08 10:36:42 2021

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
library(googlesheets4)
```

# Get list of episodes

``` r
gs4_deauth() # Public sheet does not require auth

seasons <- list(
  "Autumn in Hieron",
  "Marielda",
  "Winter in Hieron",
  "Spring in Hieron",
  "COUNTER/Weight",
  "Twilight Mirage",
  "Road to PARTIZAN",
  "PARTIZAN"
)

dat <- lapply(
  seasons,
  function(x)
    read_sheet(
      "1KZHwlSBvHtWStN4vTxOTrpv4Dp9WQrulwMCRocXeYcQ",
      sheet = x
    )
)
```

    ## Reading from "FatT Completed Transcripts"

    ## Range "'Autumn in Hieron'"

    ## Reading from "FatT Completed Transcripts"

    ## Range "'Marielda'"

    ## Reading from "FatT Completed Transcripts"

    ## Range "'Winter in Hieron'"

    ## Reading from "FatT Completed Transcripts"

    ## Range "'Spring in Hieron'"

    ## Reading from "FatT Completed Transcripts"

    ## Range "'COUNTER/Weight'"

    ## Reading from "FatT Completed Transcripts"

    ## Range "'Twilight Mirage'"

    ## Reading from "FatT Completed Transcripts"

    ## Range "'Road to PARTIZAN'"

    ## Reading from "FatT Completed Transcripts"

    ## Range "'PARTIZAN'"

``` r
# Stack sheets
names(dat) <- seasons
dat <- bind_rows(dat, .id = "season")
dat <- janitor::clean_names(dat)

# Check consistent format of dataframe
stopifnot(names(dat) == c("season", "episode", "complete", "doc"))
dat$complete <- dat$complete == "Yes" & !is.na(dat$complete)
```

# Clean List

``` r
remove_print <- function(d, remove) {
  d %>% 
    filter(str_detect(episode, remove)) %>% 
    select(episode) %>% 
    print(n = Inf)
  d %>% filter(! str_detect(episode, remove))
}

dat <- remove_print(dat, "[Mm]ortem")
```

    ## # A tibble: 6 x 1
    ##   episode                                            
    ##   <chr>                                              
    ## 1 Autumn in Hieron 29: Live Post Mortem              
    ## 2 Winter in Hieron & Marielda Post Mortem            
    ## 3 Spring in Hieron Post Mortem (Read the post!)      
    ## 4 COUNTER/Weight 44: Live Post-Mortem                
    ## 5 Twilight Mirage 68: The Twilight Mirage Post Mortem
    ## 6 PARTIZAN 48: Post Mortem

``` r
dat <- remove_print(dat, "Bonus")
```

    ## # A tibble: 1 x 1
    ##   episode                                  
    ##   <chr>                                    
    ## 1 Bonus Episode: Sports Are Just Numerology

``` r
dat <- remove_print(dat, "Introduction|A Message From|A Quick Announcement")
```

    ## # A tibble: 4 x 1
    ##   episode                                  
    ##   <chr>                                    
    ## 1 An Introduction to Friends at the Table  
    ## 2 A Message From Austin                    
    ## 3 A Message From Ali                       
    ## 4 A Quick Announcement and Some Thank Yous!

``` r
dat <- remove_print(dat, "The Months of Autumn")
```

    ## # A tibble: 2 x 1
    ##   episode                          
    ##   <chr>                            
    ## 1 Hieron: The Months of Autumn Pt 1
    ## 2 Hieron: The Months of Autumn Pt 2

``` r
dat <- remove_print(dat, "Takin’ a Snow Day")
```

    ## # A tibble: 1 x 1
    ##   episode          
    ##   <chr>            
    ## 1 Takin’ a Snow Day

``` r
dat <- remove_print(dat, "Patreon Announcement")
```

    ## # A tibble: 1 x 1
    ##   episode                                  
    ##   <chr>                                    
    ## 1 Twilight Mirage and Patreon Announcement!

``` r
dat <- remove_print(dat, "Gen Con")
```

    ## # A tibble: 2 x 1
    ##   episode                                                        
    ##   <chr>                                                          
    ## 1 Merch Available on Fangamer & Gen Con Tickets On Sale Sunday!!!
    ## 2 Friends at the Table @ Gen Con this week!!

``` r
dat <- remove_print(dat, "Announcing: PARTIZAN")
```

    ## # A tibble: 1 x 1
    ##   episode                                   
    ##   <chr>                                     
    ## 1 Announcing: PARTIZAN (And Some New Merch!)

## Code season numbers

``` r
dat <- dat %>%
  mutate(
    season_id = recode(
      season,
      "Autumn in Hieron" = 1,
      "COUNTER/Weight" = 2,
      "Marielda" = 2.5,
      "Winter in Hieron" = 3,
      "Twilight Mirage" = 4,
      "Spring in Hieron" = 5,
      "Road to PARTIZAN" = 5.5,
      "PARTIZAN" = 6
    )
  )
```

## Code episode numbers

``` r
# Consistent format
dat <- dat %>%
  mutate(episode = if_else(
    episode == "PARTIZAN 09 - DESERT SQUIRE - VANTAGE",
    "PARTIZAN 09: DESERT SQUIRE - VANTAGE",
    episode
  ))


dat <- dat %>%
  mutate(
    episode_id = as.numeric(str_replace_all(
      episode, "^(.*? )([0-9-]*):.*", "\\2"))
  )

# Holiday episodes
dat %>% filter(str_detect(episode, "Holiday"))
```

    ## # A tibble: 4 x 6
    ##   season   episode             complete doc                 season_id episode_id
    ##   <chr>    <chr>               <lgl>    <chr>                   <dbl>      <dbl>
    ## 1 Autumn ~ Autumn in Hieron: ~ FALSE    <NA>                        1          1
    ## 2 Autumn ~ Autumn in Hieron: ~ TRUE     https://drive.goog~         1          2
    ## 3 Winter ~ Winter in Hieron: ~ TRUE     https://drive.goog~         3          1
    ## 4 Winter ~ Winter in Hieron: ~ TRUE     https://docs.googl~         3          2

``` r
dat <- dat %>%
  mutate(episode_id = if_else(
    str_detect(episode, "Holiday"),
    as.numeric(NA),
    episode_id
  ))
```

## Code filename (for later)

``` r
dat <- dat %>%
  mutate(filename = sprintf(
    "%s.txt", 
    str_replace_all(
      tolower(episode), 
      pattern = c(" " = "_", "[:!?'’(),./]" = "", "\"" = ""))))
```

# Write out

``` r
dat %>%
  select(filename, season_id, season, episode_id, episode, doc, complete) %>%
  print() %>%
  write_csv("meta.csv")
```

    ## # A tibble: 293 x 7
    ##    filename       season_id season  episode_id episode      doc         complete
    ##    <chr>              <dbl> <chr>        <dbl> <chr>        <chr>       <lgl>   
    ##  1 autumn_in_hie~         1 Autumn~          0 Autumn in H~ https://dr~ TRUE    
    ##  2 autumn_in_hie~         1 Autumn~          1 Autumn in H~ https://dr~ TRUE    
    ##  3 autumn_in_hie~         1 Autumn~          2 Autumn in H~ https://dr~ TRUE    
    ##  4 autumn_in_hie~         1 Autumn~          3 Autumn in H~ https://dr~ TRUE    
    ##  5 autumn_in_hie~         1 Autumn~          4 Autumn in H~ https://dr~ TRUE    
    ##  6 autumn_in_hie~         1 Autumn~          5 Autumn in H~ https://dr~ TRUE    
    ##  7 autumn_in_hie~         1 Autumn~          6 Autumn in H~ https://dr~ TRUE    
    ##  8 autumn_in_hie~         1 Autumn~          7 Autumn in H~ <NA>        FALSE   
    ##  9 autumn_in_hie~         1 Autumn~          8 Autumn in H~ https://dr~ TRUE    
    ## 10 autumn_in_hie~         1 Autumn~          9 Autumn in H~ https://dr~ TRUE    
    ## # ... with 283 more rows
