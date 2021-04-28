Clean transcripts
================
Nathaniel Olin
Fri Feb 12 12:05:04 2021

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

# Load data

## Metadata

``` r
dat <- read_csv("meta.csv")
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

``` r
# Remove locked transcript
# dat <- dat %>%
#   filter(filename != "2.0_06_heres_your_bribe_tip.txt")
```

## Transcripts

``` r
dat <- dat %>%
  filter(complete) %>%
  mutate(
    filename = file.path("transcripts", filename),
    raw_text = sapply(
      filename,
      readLines, encoding = "UTF-8")
  )

dat %>%
  filter(season_id == 4 & episode_id == 1) %>%
  select(raw_text) %>%
  unlist() %>%
  .[100:120] %>%
  .[. != ""] %>%
  paste(collapse = "\n") %>%
  writeLines()
```

    ## AUSTIN: And then your jam, which is— so your group will be playing a group of secret agents/secret police/investigators for a group that is entrusted with protecting the remaining cities and ships of the Divine Fleet. But besides that, you also have some free time where you do other stuff. I think of your group as, not on reserve necessarily, but there's definitely a Power Rangers-y like, "Oh shit, we gotta go do a thing," but not every day. So your jam is what you do when you're not doing that. So what is Signet's jam?
    ## JANINE: Signet's jam is basically attending to her duties as part of the religion. Because she used to pilot a Divine and that Divine is one of the ones that's no longer around, people like her are kind of in short supply. So she's put out as sort of a figure, not a figure head, but just a thing that people can connect to the faith through, because there aren't that many points of connection left.
    ## AUSTIN: Right. We didn't mention what playbook it was by mistake.
    ## JANINE: Oops. She is the Onomastic.
    ## AUSTIN: Which is kind of like the thing you are, which is you are the last of a religious order, basically. 
    ## JANINE: Yes.
    ## AUSTIN: And the religious order in question that we figured out was that you are the last Excerpt from the sort of Divine that you had, is that what we ended up on?

# Clean transcripts

Split text into groups:

1.  Whitespace
2.  Text until colon
3.  All following characters

``` r
split_pattern <- "^ *(.+?): *(.*)"
```

Turn raw text vector into dataframe with speaker, text, and raw
(original text) columns

``` r
dat <- dat %>%
  mutate(text = lapply(
    raw_text, function(x){
      x <- str_replace(x, "^$", "\n")
      x <- paste(x, collapse = "")
      x <- unlist(strsplit(x, "\n"))
      tibble(
        line = 1:length(x),
        speaker = toupper(str_replace_all(x, split_pattern, "\\1")),
        text = str_replace_all(x, split_pattern, "\\2"),
        raw = x) %>%
        # Remove lines with blank speaker and blank text
        filter(! (speaker %in% c("", " ") & text %in% c("", " "))) %>%
        # filter(! str_detect(speaker, "^ *\\[")) %>%
        mutate(speaker = if_else(text == raw, as.character(NA), speaker))
    }))
```

Collapse text dataframes

``` r
dat_line <- bind_rows(dat$text, .id = "filename") %>%
  # Remove rows without speaker / text
  filter(speaker != toupper(raw)) %>%
  # Remove timestamps
  filter(! str_detect(raw, "[0-9]: *[0-9]{2}[:.][0-9]{2}")) %>%
  filter(! str_detect(raw, "[0-9]+:[0-9]{2}")) %>%
  filter(! str_detect(raw, "TIMESTAMP")) %>%
  # Remove blank rows
  filter(! str_detect(raw, "^ *$"))
```

Remove “(…):” pattern which indicates who the speaker is impersonating

``` r
dat_line <- dat_line %>%
  mutate(speaker = str_remove(speaker, " +\\(.*\\)| +\\[.*\\]"))
```

## Real speakers

``` r
fix_name <- function(col, x) str_detect(col, x) ~ x
dat_line <- dat_line %>%
  mutate(speaker = case_when(
    fix_name(speaker, "AUSTIN"),
    fix_name(speaker, "KEITH"),
    fix_name(speaker, "JACK"),
    fix_name(speaker, "ALI"),
    fix_name(speaker, "ART"),
    fix_name(speaker, "DRE"),
    fix_name(speaker, "JANINE"),
    fix_name(speaker, "SYLVIA"), 
    fix_name(speaker, "NICK"),
    TRUE ~ speaker
  ))
```

``` r
recode_speaker <- function(d, player, alias) {
  alias <- paste(alias, collapse = "|")
  d %>%
    mutate(speaker = if_else(
      str_detect(speaker, alias), 
      player, 
      speaker
    ))
}
```

``` r
dat_line <- dat_line %>%
  recode_speaker("KEITH", c("FERO", "MAKO", "GIG")) %>%
  mutate(speaker = if_else(
    speaker %in% c("KIETH", "KETH", "KETIH", "KETIH", "KEI"),
    "KEITH", speaker)) %>%
  recode_speaker("JACK", c(
    "LEM", "AUDY", "HITCHCOCK", "ETHAN", 
    "EDMUND", "FOURTEEN")) %>%
  mutate(speaker = if_else(
    speaker %in% c("JAC", "JAKC", "JAKE", "JCK"),
    "JACK", speaker)) %>%
  recode_speaker("ALI", c("HELLA", "ARIA", "CASTILLE", "TENDER", "BROUN")) %>%
  mutate(speaker = if_else(
    speaker %in% c("ADI", "AII", "AL", "ARI"),
    "ALI", speaker)) %>%
  recode_speaker("DRE", c("THRONDIR", "SIGE", "EVEN", "VALENCE")) %>%
  mutate(speaker = if_else(speaker %in% c("DE"), "DRE", speaker)) %>%
  recode_speaker("SYLVIA", c("AUBREY", "EPHRIM", "ECHO", "SYLVI", "SYLIVA")) %>%
  mutate(speaker = if_else(
    speaker %in% c("ANSDI", "SILVIA", "SYLVA", "SYVIA"), 
    "SYLVIA", speaker)) %>%
  recode_speaker("JANINE", c("ADAIRE", "SIGNET", "THISBE")) %>%
  mutate(speaker = if_else(speaker %in% c("JAININE"), "JANINE", speaker)) %>%
  recode_speaker("NICK", c("PHANTASMO", "FANTASMO")) %>%
  recode_speaker("ART", c("HADRIAN", "CASS")) %>%
  mutate(speaker = if_else(speaker %in% c("T", "ASRT", "RT"), "ART", speaker))
```

``` r
# recode exact:
dat_line <- dat_line %>%
  mutate(speaker = if_else(
    speaker %in% c(
      "A", "AU", "AUS", "AUS.", "AUSIN", "AUTIN", "AUDTIN", 
      "AUD", "AUSINT", "AUSITN", "AUST", "AUSTI"), 
    "AUSTIN", 
    speaker))
# recode str_detect
dat_line <- dat_line %>%
  recode_speaker("AUSTIN", c(
    "SAMOL",
    "SAMOT",
    "BLUE J",
    "SEVEREA",
    "TED",
    "CASCARA",
    "ARRELL",
    "CARLAY", 
    "MAELGWYN",
    "PRIMO",
    "RIX",
    "ARIN TILL",
    "JAZELLA",
    "SNITCH",
    "SAMOTHES",
    "MARYLAND",
    "MAXINE",
    "MARITIME",
    "UKLAN",
    "PRETENSE",
    "REBECCA",
    "LENNY",
    "VULTURE",
    "GALENICA",
    "BURGLAR",
    "FAUL",
    "TOWER",
    "BENJAMIN",
    "CELADOR",
    "CALLER",
    "CORAL",
    "CADENT",
    "FENTIL",
    "ISAAC",
    "MAN",
    "TAVIRA",
    "CORSICA",
    "HEDY",
    "SILAS",
    "LARDWULF",
    "SOL",
    "URRN",
    "BLAKE",
    "IRIS",
    "PEG",
    "AVORA",
    "ADELTON",
    "HIGHWATER",
    "ALYOSHA",
    "SOL",
    "GLORY",
    "SAM",
    "WES",
    "WYNTER",
    "FENOLO",
    "JESSET",
    "DUKE",
    "INVESTIGATOR",
    "COREY",
    "ORTH",
    "ROSANA",
    "THE GIRL",
    "WEAVER",
    "BIRDS",
    "ADDAX",
    "KODIAK",
    "PORTO PORLO PINTA",
    "ROE",
    "SHO",
    "SOMEONE",
    "AVAR",
    "INN-KEEPER",
    "CALHOUN",
    "SPRINGE",
    "ORDENNAN",
    "ADLETON",
    "BEN",
    "ICE LADY",
    "JAMIL",
    "KALL",
    "MORBASH",
    "OPENING NARRATION",
    "ORCHID",
    "SMOLDER",
    "THE KILLER",
    "THE OGRE",
    "ZAHN",
    "ALEJANDRO",
    "BLOOMING",
    "ZHAN",
    "MEE KOSH",
    "OORN",
    "RANDOM PERSON",
    "RAPHOA",
    "RYRIRA",
    "SABEEHA",
    "\\[SLIDE",
    "ADELAIDE",
    "GRAY",
    "MOTION",
    "RECEPTIONIST",
    "RETAIL DRONE",
    "RODENTS",
    "SKELETON", # RIP
    "UNNAMED TROOP PILOT",
    "CHRISTOPHER",
    "ELF",
    "FESTER",
    "JACQUI",
    "MAYLAND",
    "MOURNING BRIDE",
    "SUNDER",
    "THE PRINCEPT",
    "ZEALOT SAYS",
    "A LETTER FROM KENT BRIGHTON TO THE LINEAGE’S MATRIARCH, THE LADY LYME",
    "AND ALL OF THIS IS WHY I HATE THAT OLD DEBATE",
    "YOU HAVE SERVED ME WELL YET AGAIN, GRAND MARSHAL. YET I SENSE IN YOU SOMETHING SHAMEFUL"
  ))
```

Remove non-players

``` r
dat_line <- dat_line %>%
  filter(! str_detect(speaker, "\\[*MUSIC")) %>%
  filter(! str_detect(
    speaker, 
    "HIERON|HEIRON|MARIELDA|TWILIGHT MIRAGE|COUNTER/WEIGHT|PARTIZAN|LIVE AT THE TABLE|TRANSCR")) %>%
  filter(! speaker %in% c(
    "__",
    "[T/N",
    "* NOTE",
    "[A]NOTE FOR NEW LISTENERS",
    "[A WHIRLWIND OF INSTRUCTIONS FROM EVERYONE ABOUT HOW THIS WORKS, INCLUDING",
    "ALL",
    "EVERYONE",
    "[EVERYONE MAKES NOISES OF UNDERSTANDING, EG",
    "[INTRO THEME",
    "[BACKGROUND NOISE",
    "(OVERLAPPED)",
    "(OVERLAPPING)",
    "??? ",
    "[???]",
    "[EVERYONE MAKES SURPRISED NOISES. EG",
    "[UNKNOWN]",
    "CW",
    "________________[1] FELAN PARKER HTTPS",
    "“TELL ME” LYRICS",
    "FEATURING",
    "PLEASE NOTE",
    "ROBOT",
    "ROBOT SYNTHESIZER VOICE",
    "ROBOT VOICE",
    "SEVERAL PEOPLE",
    "SEVERAL PEOPLE IN UNISON"
  ))
```

Remaining lines coded as speaker above

``` r
# Confirm all first-line speakers are valid
valid_speaker <- dat_line %>%
  count(speaker) %>%
  filter(n > 100) %>%
  select(speaker) %>%
  unlist()

dat_line <- dat_line %>% 
  group_by(filename) %>% 
  mutate(first_line = line == min(line)) %>%
  ungroup()

dat_line %>% filter(first_line) %>% count(speaker)
```

    ## # A tibble: 9 x 2
    ##   speaker     n
    ## * <chr>   <int>
    ## 1 ALI        12
    ## 2 ART        25
    ## 3 AUSTIN    137
    ## 4 DRE         6
    ## 5 JACK        2
    ## 6 JANINE      4
    ## 7 KEITH      15
    ## 8 NICK        4
    ## 9 SYLVIA      2

``` r
stopifnot(dat_line$speaker[dat_line$first_line] %in% valid_speaker)

# Remove invalid
dat_line$speaker[! dat_line$speaker %in% valid_speaker] <- NA
  
while(! all(dat_line$speaker %in% valid_speaker)){
  # Replace with line above
  invalid <- is.na(dat_line$speaker)
  dat_line$speaker[invalid] <- dat_line$speaker[which(invalid) - 1]
}
dat_line %>% count(speaker)
```

    ## # A tibble: 9 x 2
    ##   speaker     n
    ## * <chr>   <int>
    ## 1 ALI     16862
    ## 2 ART     14077
    ## 3 AUSTIN  91649
    ## 4 DRE     11053
    ## 5 JACK    22125
    ## 6 JANINE  10187
    ## 7 KEITH   24082
    ## 8 NICK     3000
    ## 9 SYLVIA   9918

``` r
rm(invalid)
```

# Write out

``` r
dat_line %>%
  print() %>%
  write_csv("line.csv")
```

    ## # A tibble: 202,953 x 6
    ##    filename             line speaker text            raw              first_line
    ##    <chr>               <int> <chr>   <chr>           <chr>            <lgl>     
    ##  1 transcripts/autumn~     7 KEITH   "[cross] Yeah,~ "KEITH: [cross]~ TRUE      
    ##  2 transcripts/autumn~     9 ALI     "[cross] Yeah.~ "ALI: [cross] Y~ FALSE     
    ##  3 transcripts/autumn~    11 NICK    "[cross] Okay,~ "NICK: [cross] ~ FALSE     
    ##  4 transcripts/autumn~    13 KEITH   "Everybody— "   "KEITH: Everybo~ FALSE     
    ##  5 transcripts/autumn~    15 AUSTIN  "[cross] Whoa,~ "AUSTIN: [cross~ FALSE     
    ##  6 transcripts/autumn~    17 KEITH   "[cross] Tell ~ "KEITH: [cross]~ FALSE     
    ##  7 transcripts/autumn~    19 JACK    "I'm ready. "   "JACK: I'm read~ FALSE     
    ##  8 transcripts/autumn~    21 NICK    "Ready."        "NICK: Ready."   FALSE     
    ##  9 transcripts/autumn~    23 KEITH   "Are we ready?~ "KEITH: Are we ~ FALSE     
    ## 10 transcripts/autumn~    27 AUSTIN  "Okay."         "AUSTIN: Okay."  FALSE     
    ## # ... with 202,943 more rows
