Clean transcripts
================
Nathaniel Olin
Sun May 26 11:31:43 2019

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

    ## -- Attaching packages ----------------------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.1.0     v purrr   0.3.2
    ## v tibble  2.1.1     v dplyr   0.7.8
    ## v tidyr   0.8.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.3.0

    ## -- Conflicts -------------------------------------------------------------------------------- tidyverse_conflicts() --
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
# Remove locked transcript
dat <- dat %>%
  filter(filename != "2.0_06_heres_your_bribe_tip.txt")
```

Transcripts
-----------

``` r
dat <- dat %>%
  mutate(
    filename = file.path("transcripts", filename),
    raw_text = sapply(
      filename,
      readLines, encoding = "UTF-8"))

dat %>%
  filter(season_number == 4 & episode_number == 1) %>%
  select(raw_text) %>%
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

Split text into groups:

1.  whitespace or leading punctuation
2.  Text until new punctuation or space
3.  New punctuation or space and all following characters

``` r
split_pattern <- "(^[ *().]*)(.+?)([!?,: /\\[\\]().;—-].*)"
```

Turn raw text vector into dataframe with speaker, text, and raw (original text) columns

``` r
dat <- dat %>%
  mutate(text = lapply(
    raw_text, function(x){
      tibble(line = 1:length(x),
             speaker = toupper(str_replace_all(x, split_pattern, "\\2")),
             text = str_replace_all(x, split_pattern, "\\3"),
             raw = x) %>%
        filter(! (speaker %in% c("", " ") & text %in% c("", " "))) %>%
        filter(! str_detect(speaker, "^ *\\[")) %>%
        mutate(speaker = if_else(text == raw, as.character(NA), speaker))
    }))
```

Collapse text dataframes

``` r
dat_line <- bind_rows(dat$text, .id = "filename") %>%
  # Remove rows without speaker / text
  filter(speaker != toupper(raw)) %>%
  # Remove timestamps
  filter(! str_detect(raw, "^\\(*[0-9]: *[0-9]{2}[:.][0-9]{2}\\)*")) %>%
  # Remove blank rows
  filter(! str_detect(raw, "^ *$"))
```

Real speakers
-------------

``` r
speaker_recode <- list(
  AUSTIN = c(
    "SAMOL",
    "SAMOT",
    "SEVEREA",
    "TED",
    "ARRELL",
    "MAELGWYN",
    "PRIMO",
    "RIX",
    "MARYLAND",
    "SNITCH",
    "JAZELLA",
    "SAMOTHES",
    "MAXINE",
    "MARITIME",
    "UKLAN",
    "EDMUND",
    "REBECCA",
    "VULTURE",
    "GALENICA",
    "TOWER",
    "BENJAMIN",
    "CELADOR",
    "CALLER",
    "CORAL",
    "CASCARA",
    "FENTIL",
    "AUS",
    "CORSICA",
    "HEDY",
    "SILAS",
    "URRN",
    "IRIS",
    "PEG",
    "ADELTON",
    "HIGHWATER",
    "JACQUI",
    "ORCHID",
    "AL",
    "EMMANUEL",
    "SAM",
    "ADDAX",
    "ORTH",
    "ROSANA",
    "WEAVER",
    "ARR",
    "CENE",
    "DEMANI",
    "KODIAK",
    "ROE",
    "ADLETON",
    "BALION",
    "BEN",
    "CLANK",
    "CRYSTAL",
    "ECHO",
    "IBEX",
    "ICE",
    "JAMIL",
    "KALL",
    "MORBASH",
    "OORN",
    "RED",
    "VOLITION",
    "ZHAN",
    "JULIA",
    "ABDICATOR",
    "ALEXIS",
    "ASTIN",
    "AUST",
    "AUSITN",
    "AUSTI",
    "AUSTINS",
    "AUSTN",
    "AUTIN",
    "AXIOMS",
    "BALLAD",
    "BLACKFORD",
    "BLAKE",
    "BLUEBERRI",
    "BRIGHTON",
    "CAPTAIN",
    "CHORUS",
    "CHRISTOPHER",
    "CLARINENTS",
    "CLARINETS",
    "CODA",
    "CORRECTIVE",
    "COWBOY",
    "CURIOSITY",
    "DETACHMENT",
    "ELGASH",
    "INDIANA",
    "LIBERTY",
    "LULAV",
    "MASSALIA",
    "MAYLAND",
    "MOONLOCK",
    "NATALIA",
    "NATALYA",
    "OBELDAY",
    "PRIMARY",
    "SENESCHAL'S",
    "STORNRAS",
    "TAMSIN",
    "TANNER",
    "THYRSUS",
    "KATIE",
    "JERRY",
    "WALTZ",
    "KADAKNATH",
    "KADAKNATH'S",
    "JORNE",
    "JORAS",
    "KARAS",
    "SMOLDER",
    "SHO",
    "AMAYA",
    # Hitchcock's friend = snitch nightly
    "HITCHCOCK’S",
    "AU",
    "BLUE"
  ),
  KEITH = c(
    "FERO", 
    "MAKO",
    "GIG",
    "KETH",
    "KEI",
    "KETIH"
  ),
  JACK = c(
    "HITCHCOCK",
    "LEM",
    "AUDY",
    "ETHAN",
    "LEMME",
    "AUD",
    "FOURTEEN",
    "JACKK",
    "JACK’S"
  ),
  ALI = c(
    "HELA",
    "HELLA",
    "CASTILLE",
    "ARIA",
    "TENDER",
    "ALII",
    "ALIS",
    "ARIANA"
  ),
  ART = c(
    "HADRIAN",
    "CASS",
    "GRAND"
  ),
  ANDI = c(
    "EPHRIM",
    "AUBREY",
    "ANDJ",
    "ANID",
    "JANDI",
    "ANSDI"
  ),
  DRE = c(
    "ANDREW",
    "THRONDIR",
    "SIGE",
    "DERL"
  ),
  JANINE = c(
    "ADAIRE",
    "SIGNET",
    "JANIN",
    "JAINE"
  ),
  NICK = c(),
  TRANSCRIBER = c(
    "TRANSCRIPTION",
    "TRANSCRIBER’S",
    "TRANSCRIBERS"
  )
)
```

Recode speakers

``` r
for(i in seq_along(speaker_recode)){
  dat_line$speaker[dat_line$speaker %in% speaker_recode[[i]]] <- names(speaker_recode)[i]
}

dat_line %>%
  count(speaker, sort = T) %>%
  select(speaker) %>%
  unlist() %>% unname()
```

    ##   [1] "AUSTIN"         "KEITH"          "JACK"           "ALI"           
    ##   [5] "ART"            "DRE"            "ANDI"           "JANINE"        
    ##   [9] "NICK"           "AND"            "SO"             "OVERLAPPED"    
    ##  [13] "THE"            "TRANSCRIBER"    "BUT"            "AS"            
    ##  [17] "I"              "OVERLAPPING"    "UM"             "YOU"           
    ##  [21] "<U+FEFF>TWILIGHT" "IN"             "TRANSCRIBED"    "ALL"           
    ##  [25] "00"             "LIKE"           "<U+FEFF>SPRING" "-"             
    ##  [29] "<U+FEFF>COUNTER" "OKAY"           "LAUGHTER"       "<U+FEFF>AUTUMN"
    ##  [33] "<U+FEFF>WINTER" "DA"             "EVERYONE"       "YEAH"          
    ##  [37] "LAUGHING"       "MUSIC"          "IT"             "THIS"          
    ##  [41] "THERE"          "IT’S"           "PAUSE"          "UH"            
    ##  [45] "ALRIGHT"        "HE"             "I’M"            "WE"            
    ##  [49] "WHICH"          "A"              "MAN"            "MISSPOKEN"     
    ##  [53] "<U+FEFF>MARIELDA" "FOR"            "OH"             "THAT"          
    ##  [57] "WAKING"         "OR"             "ON"             "SOMEONE"       
    ##  [61] "THEY"           "AT"             "CROSSTALK"      "RECAP"         
    ##  [65] "THAT’S"         "WHAT"           "IF"             "NOW"           
    ##  [69] "TWO"            "\"I"            "BECAUSE"        "NO"            
    ##  [73] "ONE"            "RIGHT"          "SHE"            "WHEN"          
    ##  [77] "ANOMALY"        "IS"             "THERE'S"        "TO"            
    ##  [81] "TURN"           "WRONG"          "“"              "ANYWAY"        
    ##  [85] "DO"             "END"            "I’LL"           "THERE’S"       
    ##  [89] "THEY’RE"        "TODAY"          "WES"            "AFTER"         
    ##  [93] "ALSO"           "EVEN"           "FIRST"          "HERE’S"        
    ##  [97] "HM"             "HOW"            "LET"            "LET’S"         
    ## [101] "LOUD"           "MORE"           "THEN"           "TYPING"        
    ## [105] "WELCOME"        "WHERE"          "WHO"            "—AND"          
    ## [109] "—I"             "“I"             "AGAIN"          "AH"            
    ## [113] "FINALLY"        "FOUR"           "HE’S"           "JUST"          
    ## [117] "LET'S"          "MAYBE"          "OF"             "SEVERAL"       
    ## [121] "SIMULTANEOUSLY" "THAT'S"         "THINGS"         "THOUGH"        
    ## [125] "WE’RE"          "WHEEZING"       "YOUR"           "—IN"           
    ## [129] "—YOU"           "\"A"            "\"DOCTOR"       "\"SO"          
    ## [133] "\"THE"          "\"WHAT"         "{1"             "{LAUGHING"     
    ## [137] "‘CAUSE"         "“AND"           "“OH"            "“WHEN"         
    ## [141] "…"              "…AND"           "1"              "15"            
    ## [145] "3"              "4"              "5"              "6"             
    ## [149] "ANOTHER"        "CHUCKLING"      "COOL"           "DON’T"         
    ## [153] "DOWN"           "EACH"           "EVERY"          "EVERYBODY"     
    ## [157] "FUCK"           "I’VE"           "INSIDE"         "JOINING"       
    ## [161] "LAUGHS"         "LIGHT"          "MY"             "NOT"           
    ## [165] "OFF"            "OK"             "OPENING"        "OUR"           
    ## [169] "POST"           "ROLL"           "SHE'S"          "SLIGHT"        
    ## [173] "SNORTING"       "SNORTS"         "SOME"           "SOMETHING"     
    ## [177] "SOUNDS"         "THEIR"          "THEME"          "THESE"         
    ## [181] "VARIOUS"        "WAIT"           "WE’LL"          "WHAT’S"        
    ## [185] "WHEREAS"        "WHO’S"          "WITH"           "YES"           
    ## [189] "—AS"            "—BEEN"          "—GLOVE"         "—GOD"          
    ## [193] "—IF"            "—JUMP"          "—NAME"          "—OR"           
    ## [197] "—PERFORM"       "—POP"           "—READ"          "—SAYS"         
    ## [201] "—SHE"           "—SHOOTING"      "—START"         "—TENDER"       
    ## [205] "—THE"           "—TO"            "—TOO"           "—TURNED"       
    ## [209] "—WHO"           "\"AND"          "\"AT"           "\"AW"          
    ## [213] "\"BUT"          "\"CARGO"        "\"CENTRA"       "\"FOR"         
    ## [217] "\"GODDAMMIT"    "\"IN"           "\"IT"           "\"IT'S"        
    ## [221] "\"READY"        "\"SEVEN"        "\"THEN"         "\"THEY"        
    ## [225] "\"THOSE"        "\"WE'LL"        "\"YOUR"         ":"             
    ## [229] "?"              "{11"            "{16"            "{2"            
    ## [233] "{26"            "{3"             "{33"            "{44"           
    ## [237] "{48"            "{54"            "{58"            "{EPISODE"      
    ## [241] "~"              "‘IN"            "’"              "“A"            
    ## [245] "“BREAKFAST”"    "“CAN"           "“DOES"          "“I’VE"         
    ## [249] "“IN"            "“INSTEAD"       "“IT’S"          "“JULIA"        
    ## [253] "“LEMME"         "“OBJECTION"     "“OUR"           "“RIGHTEOUSNESS"
    ## [257] "“SIX"           "“SUCH"          "“TELL"          "“TO"           
    ## [261] "“WE’RE"         "“WHAT"          "“YO"            "“YOU"          
    ## [265] "“YOUR"          "<U+200B>"       "<U+300C>S"      "<U+FEFF>`COUNTER"
    ## [269] "…WATCHING"      "0"              "01"             "10"            
    ## [273] "100%"           "11112"          "11895"          "13"            
    ## [277] "17"             "2"              "20"             "25"            
    ## [281] "26622"          "28"             "285"            "29"            
    ## [285] "30"             "31"             "42"             "45"            
    ## [289] "46…TOMB"        "47"             "53"             "7"             
    ## [293] "8"              "822"            "98"             "ABOUT"         
    ## [297] "ABOVE"          "ACTIVITY"       "ACTUALLY"       "ADDED"         
    ## [301] "ALTAR"          "AMONG"          "AN"             "AND…"          
    ## [305] "AND…LIKE"       "AND…YOU"        "ANGELS”"        "ANY"           
    ## [309] "ANYBODY"        "ANYTHING"       "ARE"            "ART’S"         
    ## [313] "ASKS"           "AUDIBLE"        "AWESOME"        "BASICALLY"     
    ## [317] "BELL"           "BIG"            "BY"             "C’MON"         
    ## [321] "CHANGED"        "CHUCKLES"       "CLOCK"          "CLOSING"       
    ## [325] "CLOUDS"         "COME"           "COMING"         "COOKIE"        
    ## [329] "CROWN"          "CW"             "DARK"           "DAY"           
    ## [333] "DAYS"           "DE"             "DEALIN"         "DEAR"          
    ## [337] "DID"            "DOES"           "DOESN'T"        "DUHDUH"        
    ## [341] "EITHER"         "ENDING"         "EPISODE"        "EVERYONE'S"    
    ## [345] "EVERYTHING"     "EXCEPT"         "EXTREMELY"      "FAIR"          
    ## [349] "FAKE"           "FALLING"        "FEATURING"      "FELLOW"        
    ## [353] "FESTER"         "FILLER"         "FIVE"           "FORGET"        
    ## [357] "FRANK"          "FRANKLY"        "FROM"           "FRONTIER"      
    ## [361] "FUNNY"          "FURTHER"        "FUTURE"         "GASP"          
    ## [365] "GIFT"           "GIGGLES"        "GIVE"           "GO"            
    ## [369] "GOD"            "GOOD"           "GRAY"           "GROUP"         
    ## [373] "GUARANTEED"     "GUESS"          "HAVE…"          "HER"           
    ## [377] "HERE"           "HIDDEN"         "HIS"            "HISSING"       
    ## [381] "HOOWEE"         "HUH"            "I'LL"           "I'M"           
    ## [385] "IMAGINE"        "INTO"           "INTRO"          "JA"            
    ## [389] "JA'MY"          "JCK"            "JEWELRY"        "KEEP"          
    ## [393] "KOBUS"          "KORRIN"         "LOADED"         "LONE"          
    ## [397] "LONG"           "LONGER"         "LOOK"           "LOOKING"       
    ## [401] "MARIELDA"       "MESSAGE"        "METHOD"         "MIRAGE"        
    ## [405] "MMM"            "MONTHS"         "MOTHERFUCK"     "NEED"          
    ## [409] "NEW"            "NEXT"           "NORMALLY"       "NOTE"          
    ## [413] "O’HARE"         "OTHER"          "OUTRO"          "OVER"          
    ## [417] "P"              "PAY"            "PEOPLE"         "POLY"          
    ## [421] "POVERTY"        "PROBABLY"       "QUESTION"       "QUESTIONS"     
    ## [425] "QUI"            "QUICK"          "QUIET"          "REAL"          
    ## [429] "RECOVER"        "REPEATED"       "REVISED"        "ROOM"          
    ## [433] "RT"             "SAYS"           "SCATTERED"      "SCRAP"         
    ## [437] "SECOND"         "SET"            "SHAKES"         "SHE’D"         
    ## [441] "SHE’S"          "SHRUGGING"      "SIGNET…I"       "SINCERELY"     
    ## [445] "SITTING"        "SKEIN"          "SNICKERING"     "SNORT"         
    ## [449] "SNOW"           "SO…"            "SO…LIKE…OKAY"   "SOFT"          
    ## [453] "SOL"            "SOMETIME"       "SPACE"          "START"         
    ## [457] "STEP"           "STORMS"         "SUCH"           "SUNDER"        
    ## [461] "SYNOPSIS"       "TEASES"         "TECH"           "TECHNICALLY"   
    ## [465] "TELL"           "TEMPORARY"      "THAN"           "THANK"         
    ## [469] "THAT’S…"        "THAT…ADELAIDE"  "THEIRS"         "THEY'LL"       
    ## [473] "THIRD"          "THIS…"          "THOSE"          "UGH"           
    ## [477] "UH…702"         "UH…ALL"         "UH…AND"         "UH…WHO’S"      
    ## [481] "UHH"            "UHHUH"          "UM…"            "UNBRAKED"      
    ## [485] "UNDERSTAND"     "UNLESS"         "UNTIL"          "UP"            
    ## [489] "URM"            "VELAS"          "VISCOUS"        "VISIONS"       
    ## [493] "WALK"           "WANT"           "WAS"            "WATCHED"       
    ## [497] "WATCHING"       "WE'LL"          "WE’VE"          "WELL"          
    ## [501] "WHILE"          "WHOOPS"         "WHY"            "WILL"          
    ## [505] "WITHOUT"        "WOOP"           "WREATHE"        "Y’ALL"         
    ## [509] "YOU’VE"         "ZEBRAS"

Remaining lines coded as speaker above

``` r
dat_line$speaker[dat_line$line == 1] <- NA
for(i in seq_along(dat_line$speaker)){
  # If you're not on the list of "real" speakers
  if(! dat_line$speaker[i] %in% names(speaker_recode) & dat_line$line[i] > 1){
    # You're a carryover from the line above, and
    dat_line$speaker[i] <- dat_line$speaker[i - 1]
    # Your text should stay as written
    dat_line$text <- dat_line$raw
  }
}

dat_line %>% count(speaker)
```

    ## # A tibble: 11 x 2
    ##    speaker         n
    ##    <chr>       <int>
    ##  1 ALI         16124
    ##  2 ANDI         8559
    ##  3 ART         11997
    ##  4 AUSTIN      79795
    ##  5 DRE          8806
    ##  6 JACK        19316
    ##  7 JANINE       7349
    ##  8 KEITH       21462
    ##  9 NICK         2848
    ## 10 TRANSCRIBER   108
    ## 11 <NA>          196

Write out
=========

``` r
dat_line %>%
  print() %>%
  write_csv("line.csv")
```

    ## # A tibble: 176,560 x 5
    ##    filename            line speaker text                raw                
    ##    <chr>              <int> <chr>   <chr>               <chr>              
    ##  1 transcripts/1.0_0~     1 <NA>    <U+FEFF>Autumn in Hieron ~  <U+FEFF>Autumn in Hieron ~ 
    ##  2 transcripts/1.0_0~     4 TRANSC~ Transcribers: Etha~ Transcribers: Etha~
    ##  3 transcripts/1.0_0~     9 KEITH   "KEITH: [cross] Ye~ "KEITH: [cross] Ye~
    ##  4 transcripts/1.0_0~    12 ALI     "ALI: [cross] Yeah~ "ALI: [cross] Yeah~
    ##  5 transcripts/1.0_0~    15 NICK    "NICK: [cross] Oka~ "NICK: [cross] Oka~
    ##  6 transcripts/1.0_0~    18 KEITH   "KEITH: Everybody—~ "KEITH: Everybody—~
    ##  7 transcripts/1.0_0~    21 AUSTIN  AUSTIN: Whoa, whoa~ AUSTIN: Whoa, whoa~
    ##  8 transcripts/1.0_0~    24 KEITH   KEITH: Tell me whe~ KEITH: Tell me whe~
    ##  9 transcripts/1.0_0~    27 AUSTIN  AUSTIN: Okay, I'm ~ AUSTIN: Okay, I'm ~
    ## 10 transcripts/1.0_0~    30 JACK    "JACK: I'm ready. " "JACK: I'm ready. "
    ## # ... with 176,550 more rows
