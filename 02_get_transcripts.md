Get FatT transcripts
================
Nathaniel Olin
Tue Sep 03 21:03:15 2019

``` r
library(tidyverse)
```

    ## Registered S3 methods overwritten by 'ggplot2':
    ##   method         from 
    ##   [.quosures     rlang
    ##   c.quosures     rlang
    ##   print.quosures rlang

    ## -- Attaching packages ----------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.1.1     v purrr   0.3.2
    ## v tibble  2.1.2     v dplyr   0.8.1
    ## v tidyr   0.8.3     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.4.0

    ## -- Conflicts -------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(googledrive)
library(googlesheets4)
sheets_auth(email = Sys.getenv("email"))

if(! "transcripts" %in% list.files())
  dir.create("transcripts")
```

# Download transcripts

## Get episode URLs

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

## Save transcripts to file

``` r
# Remove locked file
# dat$url[dat$url == "https://drive.google.com/open?id=19CSVitxMRtKKXr8m4swuczgAHtwcTT6sEEZaxSzQpgI"] <- NA
    
for(i in seq_along(dat$url)){
  if(! dat$filename[i] %in% list.files("transcripts") & !is.na(dat$url[i]))
    drive_download(as_id(dat$url[i]), path = paste0("transcripts/", dat$filename[i]))
}
```

    ## File downloaded:
    ##   * Autumn in Hieron 00: We’re Not Calling It Duckberg
    ## Saved locally as:
    ##   * transcripts/1.0_00_were_not_calling_it_duckberg.txt

    ## File downloaded:
    ##   * Autumn in Hieron 01: We Have Not Yet Begun To Be Pompous
    ## Saved locally as:
    ##   * transcripts/1.0_01_we_have_not_yet_begun_to_be_pompous.txt

    ## File downloaded:
    ##   * Autumn in Hieron 02: You Found Out What Was Magical
    ## Saved locally as:
    ##   * transcripts/1.0_02_you_found_out_what_was_magical.txt

    ## File downloaded:
    ##   * Autumn in Hieron 03: A Podcast About Listening
    ## Saved locally as:
    ##   * transcripts/1.0_03_a_podcast_about_listening.txt

    ## File downloaded:
    ##   * Autumn in Hieron 04: Is It Time Already?
    ## Saved locally as:
    ##   * transcripts/1.0_04_is_it_time_already.txt

    ## File downloaded:
    ##   * Autumn in Hieron 05: What’s a Good Name For A Ship?
    ## Saved locally as:
    ##   * transcripts/1.0_05_whats_a_good_name_for_a_ship.txt

    ## File downloaded:
    ##   * Autumn in Hieron 06: A Bad Trip
    ## Saved locally as:
    ##   * transcripts/1.0_06_a_bad_trip.txt

    ## File downloaded:
    ##   * Autumn in Hieron 08: On The Tip Of Your Tongue
    ## Saved locally as:
    ##   * transcripts/1.0_08_on_the_tip_of_your_tongue.txt

    ## File downloaded:
    ##   * Autumn in Hieron 09: I’m Not Happy With This Decision At All
    ## Saved locally as:
    ##   * transcripts/1.0_09_im_not_happy_with_this_decision_at_all.txt

    ## File downloaded:
    ##   * Autumn in Hieron 10: Chekhov’s Torture Elf
    ## Saved locally as:
    ##   * transcripts/1.0_10_chekhovs_torture_elf.txt

    ## File downloaded:
    ##   * Autumn in Hieron 12: Knowledge and Ignorance
    ## Saved locally as:
    ##   * transcripts/1.0_12_knowledge_and_ignorance.txt

    ## File downloaded:
    ##   * Autumn in Hieron 13: What A Surprise
    ## Saved locally as:
    ##   * transcripts/1.0_13_what_a_surprise.txt

    ## File downloaded:
    ##   * Autumn in Hieron: Holiday Special 02: I’ve Killed Monsters
    ## Saved locally as:
    ##   * transcripts/1.0_NA_ive_killed_monsters.txt

    ## File downloaded:
    ##   * Autumn in Hieron 15: Have You Ever Swung A Sword At A Ghost Before?
    ## Saved locally as:
    ##   * transcripts/1.0_15_have_you_ever_swung_a_sword_at_a_ghost_before.txt

    ## File downloaded:
    ##   * Autumn in Hieron 16: Those Woods You Chose
    ## Saved locally as:
    ##   * transcripts/1.0_16_those_woods_you_chose.txt

    ## File downloaded:
    ##   * Autumn in Hieron 17: As Death’s Eyes Look Over The World
    ## Saved locally as:
    ##   * transcripts/1.0_17_as_deaths_eyes_look_over_the_world.txt

    ## File downloaded:
    ##   * Autumn in Hieron 19: Violence Mulligan Mulligan
    ## Saved locally as:
    ##   * transcripts/1.0_19_violence_mulligan_mulligan.txt

    ## File downloaded:
    ##   * Autumn in Hieron 20: Do I Feel Followed?
    ## Saved locally as:
    ##   * transcripts/1.0_20_do_i_feel_followed.txt

    ## File downloaded:
    ##   * Autumn in Hieron 21: The Storm Over Tristero
    ## Saved locally as:
    ##   * transcripts/1.0_21_the_storm_over_tristero.txt

    ## File downloaded:
    ##   * Autumn in Hieron 22: Closing the Window
    ## Saved locally as:
    ##   * transcripts/1.0_22_closing_the_window.txt

    ## File downloaded:
    ##   * Autumn in Hieron 23: Spinning Straw Out Of Gold
    ## Saved locally as:
    ##   * transcripts/1.0_23_spinning_straw_out_of_gold.txt

    ## File downloaded:
    ##   * Autumn in Hieron 24: A Paladin is a Tool
    ## Saved locally as:
    ##   * transcripts/1.0_24_a_paladin_is_a_tool.txt

    ## File downloaded:
    ##   * Autumn in Hieron 25: Work Off Your Sins
    ## Saved locally as:
    ##   * transcripts/1.0_25_work_off_your_sins.txt

    ## File downloaded:
    ##   * Autumn in Hieron 26: Why Are You Here, Again?
    ## Saved locally as:
    ##   * transcripts/1.0_26_why_are_you_here_again.txt

    ## File downloaded:
    ##   * Autumn in Hieron 27: The Shores of Ordenna
    ## Saved locally as:
    ##   * transcripts/1.0_27_the_shores_of_ordenna.txt

    ## File downloaded:
    ##   * Autumn in Hieron 28: A Choice About What You Believe
    ## Saved locally as:
    ##   * transcripts/1.0_28_a_choice_about_what_you_believe.txt

    ## File downloaded:
    ##   * COUNTER/Weight -01: Secret World Gen Episode
    ## Saved locally as:
    ##   * transcripts/2.0_-1_secret_world_gen_episode.txt

    ## File downloaded:
    ##   * COUNTER/Weight 01: I Would Like A Bribe
    ## Saved locally as:
    ##   * transcripts/2.0_01_i_would_like_a_bribe.txt

    ## File downloaded:
    ##   * COUNTER/Weight 02: Soldier Game
    ## Saved locally as:
    ##   * transcripts/2.0_02_soldier_game.txt

    ## File downloaded:
    ##   * COUNTER/Weight 03: It’s Fine, We’re Criminals
    ## Saved locally as:
    ##   * transcripts/2.0_03_its_fine_were_criminals.txt

    ## File downloaded:
    ##   * COUNTER/Weight 04: The Bells Are Ringing
    ## Saved locally as:
    ##   * transcripts/2.0_04_the_bells_are_ringing.txt

    ## File downloaded:
    ##   * COUNTER/Weight 06: Here’s Your Bribe Tip
    ## Saved locally as:
    ##   * transcripts/2.0_06_heres_your_bribe_tip.txt

    ## File downloaded:
    ##   * COUNTER/Weight 07: I Am Not Allowed
    ## Saved locally as:
    ##   * transcripts/2.0_07_i_am_not_allowed.txt

    ## File downloaded:
    ##   * COUNTER/Weight 08: Our Hostage Below
    ## Saved locally as:
    ##   * transcripts/2.0_08_our_hostage_below.txt

    ## File downloaded:
    ##   * COUNTER/Weight 10: Drawing Clocks
    ## Saved locally as:
    ##   * transcripts/2.0_10_drawing_clocks.txt

    ## File downloaded:
    ##   * COUNTER/Weight 11: A Thing You Know About Nightclubs
    ## Saved locally as:
    ##   * transcripts/2.0_11_a_thing_you_know_about_nightclubs.txt

    ## File downloaded:
    ##   * COUNTER/Weight 15: A Candle in the Sun
    ## Saved locally as:
    ##   * transcripts/2.0_15_a_candle_in_the_sun.txt

    ## File downloaded:
    ##   * COUNTER/Weight 17: Metal That Has Died Twice
    ## Saved locally as:
    ##   * transcripts/2.0_17_metal_that_has_died_twice.txt

    ## File downloaded:
    ##   * COUNTER/Weight 18: The Order of Things
    ## Saved locally as:
    ##   * transcripts/2.0_18_the_order_of_things.txt

    ## File downloaded:
    ##   * COUNTER/Weight 19: The Road You Take And Who You Take It With
    ## Saved locally as:
    ##   * transcripts/2.0_19_the_road_you_take_and_who_you_take_it_with.txt

    ## File downloaded:
    ##   * COUNTER/Weight 20: The Glimmer Incident: The Executive Enters!
    ## Saved locally as:
    ##   * transcripts/2.0_20_the_glimmer_incident_the_executive_enters.txt

    ## File downloaded:
    ##   * COUNTER/Weight 21: Crisis! (A Solemn Vow Above The Sea of Counterweight)
    ## Saved locally as:
    ##   * transcripts/2.0_21_crisis_a_solemn_vow_above_the_sea_of_counterweight.txt

    ## File downloaded:
    ##   * COUNTER/Weight 22: The Broken Branch
    ## Saved locally as:
    ##   * transcripts/2.0_22_the_broken_branch.txt

    ## File downloaded:
    ##   * COUNTER/Weight 23: An Astonishingly Illegal Ship
    ## Saved locally as:
    ##   * transcripts/2.0_23_an_astonishingly_illegal_ship.txt

    ## File downloaded:
    ##   * COUNTER/Weight 24: It’s Still Dark Out
    ## Saved locally as:
    ##   * transcripts/2.0_24_its_still_dark_out.txt

    ## File downloaded:
    ##   * COUNTER/Weight 26: Do You Have Room For Me?
    ## Saved locally as:
    ##   * transcripts/2.0_26_do_you_have_room_for_me.txt

    ## File downloaded:
    ##   * COUNTER/Weight 28: A Special Kind of Warmth
    ## Saved locally as:
    ##   * transcripts/2.0_28_a_special_kind_of_warmth.txt

    ## File downloaded:
    ##   * COUNTER/Weight 31: Expertize with a Z
    ## Saved locally as:
    ##   * transcripts/2.0_31_expertize_with_a_z.txt

    ## File downloaded:
    ##   * COUNTER/Weight 33: A Fractal Garden and an Intercepted Message
    ## Saved locally as:
    ##   * transcripts/2.0_33_a_fractal_garden_and_an_intercepted_message.txt

    ## File downloaded:
    ##   * COUNTER/Weight 34: Reaching Out
    ## Saved locally as:
    ##   * transcripts/2.0_34_reaching_out.txt

    ## File downloaded:
    ##   * COUNTER/Weight 35: A Knock at the Door
    ## Saved locally as:
    ##   * transcripts/2.0_35_a_knock_at_the_door.txt

    ## File downloaded:
    ##   * COUNTER/Weight 36: Everything is Temporary
    ## Saved locally as:
    ##   * transcripts/2.0_36_everything_is_temporary.txt

    ## File downloaded:
    ##   * COUNTER/Weight 37: Visions from Windows, Or: The Last Time the Bomb Dropped
    ## Saved locally as:
    ##   * transcripts/2.0_37_visions_from_windows_or_the_last_time_the_bomb_dropped.txt

    ## File downloaded:
    ##   * COUNTER/Weight 38: Found Footage
    ## Saved locally as:
    ##   * transcripts/2.0_38_found_footage.txt

    ## File downloaded:
    ##   * COUNTER/Weight 39: Just Another Machine
    ## Saved locally as:
    ##   * transcripts/2.0_39_just_another_machine.txt

    ## File downloaded:
    ##   * COUNTER/Weight 40: The Storm Over September
    ## Saved locally as:
    ##   * transcripts/2.0_40_the_storm_over_september.txt

    ## File downloaded:
    ##   * COUNTER/Weight 41: A Splintered Branch, A Ringing Bell Pt. 1
    ## Saved locally as:
    ##   * transcripts/2.0_41_a_splintered_branch_a_ringing_bell_pt_1.txt

    ## File downloaded:
    ##   * COUNTER/Weight 42: A Splintered Branch, A Ringing Bell Pt. 2
    ## Saved locally as:
    ##   * transcripts/2.0_42_a_splintered_branch_a_ringing_bell_pt_2.txt

    ## File downloaded:
    ##   * COUNTER/Weight 43: A Splintered Branch, A Ringing Bell Pt. 3
    ## Saved locally as:
    ##   * transcripts/2.0_43_a_splintered_branch_a_ringing_bell_pt_3.txt

    ## File downloaded:
    ##   * Marielda 01: The City of Light Pt. 1
    ## Saved locally as:
    ##   * transcripts/2.5_01_the_city_of_light_pt_1.txt

    ## File downloaded:
    ##   * Marielda 02: The City of Light Pt. 2
    ## Saved locally as:
    ##   * transcripts/2.5_02_the_city_of_light_pt_2.txt

    ## File downloaded:
    ##   * Marielda 03: The Crosstown Job Pt. 1
    ## Saved locally as:
    ##   * transcripts/2.5_03_the_crosstown_job_pt_1.txt

    ## File downloaded:
    ##   * Marielda 04: The Crosstown Job Pt. 2
    ## Saved locally as:
    ##   * transcripts/2.5_04_the_crosstown_job_pt_2.txt

    ## File downloaded:
    ##   * Marielda 05: War and Azaleas
    ## Saved locally as:
    ##   * transcripts/2.5_05_war_and_azaleas.txt

    ## File downloaded:
    ##   * Marielda 06: The Valentine Affair Pt. 1
    ## Saved locally as:
    ##   * transcripts/2.5_06_the_valentine_affair_pt_1.txt

    ## File downloaded:
    ##   * Marielda 07: The Valentine Affair Pt. 2
    ## Saved locally as:
    ##   * transcripts/2.5_07_the_valentine_affair_pt_2.txt

    ## File downloaded:
    ##   * Marielda 09: The Valentine Affair Pt. 4
    ## Saved locally as:
    ##   * transcripts/2.5_09_the_valentine_affair_pt_4.txt

    ## File downloaded:
    ##   * Marielda 10: Four Conversations
    ## Saved locally as:
    ##   * transcripts/2.5_10_four_conversations.txt

    ## File downloaded:
    ##   * Marielda 12: The Killing of the King-God Samothes By The Traitor Prince Maelgwyn Pt. 2
    ## Saved locally as:
    ##   * transcripts/2.5_12_the_killing_of_the_king-god_samothes_by_the_traitor_prince_maelgwyn_pt_2.txt

    ## File downloaded:
    ##   * Marielda 13: The Killing of the King-God Samothes By The Traitor Prince Maelgwyn Pt. 3
    ## Saved locally as:
    ##   * transcripts/2.5_13_the_killing_of_the_king-god_samothes_by_the_traitor_prince_maelgwyn_pt_3.txt

    ## File downloaded:
    ##   * Marielda 14: The Killing of the King-God Samothes By The Traitor Prince Maelgwyn Pt. 4
    ## Saved locally as:
    ##   * transcripts/2.5_14_the_killing_of_the_king-god_samothes_by_the_traitor_prince_maelgwyn_pt_4.txt

    ## File downloaded:
    ##   * Winter in Hieron 01: An Arrow Let Loose
    ## Saved locally as:
    ##   * transcripts/3.0_01_an_arrow_let_loose.txt

    ## File downloaded:
    ##   * Winter in Hieron 02: A Museum in the Distance
    ## Saved locally as:
    ##   * transcripts/3.0_02_a_museum_in_the_distance.txt

    ## File downloaded:
    ##   * Winter in Hieron 03: You Haven’t Kept Up, Have You?
    ## Saved locally as:
    ##   * transcripts/3.0_03_you_havent_kept_up_have_you.txt

    ## File downloaded:
    ##   * Winter in Hieron 04: A Combination of Notes
    ## Saved locally as:
    ##   * transcripts/3.0_04_a_combination_of_notes.txt

    ## File downloaded:
    ##   * Winter in Hieron 05: Blood Doesn’t Run
    ## Saved locally as:
    ##   * transcripts/3.0_05_blood_doesnt_run.txt

    ## File downloaded:
    ##   * Winter in Hieron 06: All Violence, All Brains
    ## Saved locally as:
    ##   * transcripts/3.0_06_all_violence_all_brains.txt

    ## File downloaded:
    ##   * Winter in Hieron 07: Two Hands
    ## Saved locally as:
    ##   * transcripts/3.0_07_two_hands.txt

    ## File downloaded:
    ##   * Winter in Hieron 08: The Meeting House
    ## Saved locally as:
    ##   * transcripts/3.0_08_the_meeting_house.txt

    ## File downloaded:
    ##   * Winter in Hieron 10: Fire and Blood
    ## Saved locally as:
    ##   * transcripts/3.0_10_fire_and_blood.txt

    ## File downloaded:
    ##   * Winter in Hieron 11: The Importance of Names
    ## Saved locally as:
    ##   * transcripts/3.0_11_the_importance_of_names.txt

    ## File downloaded:
    ##   * Winter in Hieron 12: A Chaotic Cataloging
    ## Saved locally as:
    ##   * transcripts/3.0_12_a_chaotic_cataloging.txt

    ## File downloaded:
    ##   * Winter in Hieron 13: Rosemerrow, Oh My Rosemerrow
    ## Saved locally as:
    ##   * transcripts/3.0_13_rosemerrow_oh_my_rosemerrow.txt

    ## File downloaded:
    ##   * Winter in Hieron 14: What Do You Want Here?
    ## Saved locally as:
    ##   * transcripts/3.0_14_what_do_you_want_here.txt

    ## File downloaded:
    ##   * Winter in Hieron 16: Is There Anyone Else?
    ## Saved locally as:
    ##   * transcripts/3.0_16_is_there_anyone_else.txt

    ## File downloaded:
    ##   * Winter in Hieron 17: Undelivered Resignations
    ## Saved locally as:
    ##   * transcripts/3.0_17_undelivered_resignations.txt

    ## File downloaded:
    ##   * Winter in Hieron 18: Try to Live Normally
    ## Saved locally as:
    ##   * transcripts/3.0_18_try_to_live_normally.txt

    ## File downloaded:
    ##   * Winter in Hieron 19: Make The Spring Last Forever
    ## Saved locally as:
    ##   * transcripts/3.0_19_make_the_spring_last_forever.txt

    ## File downloaded:
    ##   * Winter in Hieron 20: Pulling You Further Down
    ## Saved locally as:
    ##   * transcripts/3.0_20_pulling_you_further_down.txt

    ## File downloaded:
    ##   * Winter in Hieron 21: The Shield of Our Will
    ## Saved locally as:
    ##   * transcripts/3.0_21_the_shield_of_our_will.txt

    ## File downloaded:
    ##   * Winter in Hieron 23: Make the Library Smile
    ## Saved locally as:
    ##   * transcripts/3.0_23_make_the_library_smile.txt

    ## File downloaded:
    ##   * Winter in Hieron 24: An Open Mind
    ## Saved locally as:
    ##   * transcripts/3.0_24_an_open_mind.txt

    ## File downloaded:
    ##   * Winter in Hieron 25: Twenty Two Brown Birds
    ## Saved locally as:
    ##   * transcripts/3.0_25_twenty_two_brown_birds.txt

    ## File downloaded:
    ##   * Winter in Hieron 26: A Shattering Note
    ## Saved locally as:
    ##   * transcripts/3.0_26_a_shattering_note.txt

    ## File downloaded:
    ##   * Winter in Hieron 27: A Good Metaphor
    ## Saved locally as:
    ##   * transcripts/3.0_27_a_good_metaphor.txt

    ## File downloaded:
    ##   * Winter in Hieron 28: What Life Looks Like
    ## Saved locally as:
    ##   * transcripts/3.0_28_what_life_looks_like.txt

    ## File downloaded:
    ##   * Winter in Hieron 29: Slow Justice
    ## Saved locally as:
    ##   * transcripts/3.0_29_slow_justice.txt

    ## File downloaded:
    ##   * Twilight Mirage 00: The Final Eight Divines
    ## Saved locally as:
    ##   * transcripts/4.0_00_the_final_eight_divines.txt

    ## File downloaded:
    ##   * Twilight Mirage 01: The Beloved Dust
    ## Saved locally as:
    ##   * transcripts/4.0_01_the_beloved_dust.txt

    ## File downloaded:
    ##   * Twilight Mirage 02: The Last Divine
    ## Saved locally as:
    ##   * transcripts/4.0_02_the_last_divine.txt

    ## File downloaded:
    ##   * Twilight Mirage 04: Birds of Prey
    ## Saved locally as:
    ##   * transcripts/4.0_04_birds_of_prey.txt

    ## File downloaded:
    ##   * Twilight Mirage 05: The Sunlight of Knowing
    ## Saved locally as:
    ##   * transcripts/4.0_05_the_sunlight_of_knowing.txt

    ## File downloaded:
    ##   * Twilight Mirage 06: We Want You To Come Home
    ## Saved locally as:
    ##   * transcripts/4.0_06_we_want_you_to_come_home.txt

    ## File downloaded:
    ##   * Twilight Mirage 07: Second Street Drifting
    ## Saved locally as:
    ##   * transcripts/4.0_07_second_street_drifting.txt

    ## File downloaded:
    ##   * Twilight Mirage 08: We are the River, And it is the Sea
    ## Saved locally as:
    ##   * transcripts/4.0_08_we_are_the_river_and_it_is_the_sea.txt

    ## File downloaded:
    ##   * Twilight Mirage 09: The Sound of Strings
    ## Saved locally as:
    ##   * transcripts/4.0_09_the_sound_of_strings.txt

    ## File downloaded:
    ##   * Twilight Mirage 10: Looking To Land
    ## Saved locally as:
    ##   * transcripts/4.0_10_looking_to_land.txt

    ## File downloaded:
    ##   * Twilight Mirage 11: What We're Capable Of
    ## Saved locally as:
    ##   * transcripts/4.0_11_what_were_capable_of.txt

    ## File downloaded:
    ##   * Twilight Mirage 12: The Promise of Presence
    ## Saved locally as:
    ##   * transcripts/4.0_12_the_promise_of_presence.txt

    ## File downloaded:
    ##   * Twilight Mirage 14: The Value of Distance
    ## Saved locally as:
    ##   * transcripts/4.0_14_the_value_of_distance.txt

    ## File downloaded:
    ##   * Twilight Mirage 15: A Life and A History
    ## Saved locally as:
    ##   * transcripts/4.0_15_a_life_and_a_history.txt

    ## File downloaded:
    ##   * Twilight Mirage 16: An Approximation of a Connection
    ## Saved locally as:
    ##   * transcripts/4.0_16_an_approximation_of_a_connection.txt

    ## File downloaded:
    ##   * Twilight Mirage 20: Bits Left Behind
    ## Saved locally as:
    ##   * transcripts/4.0_20_bits_left_behind.txt

    ## File downloaded:
    ##   * Twilight Mirage 21: One's Own Right
    ## Saved locally as:
    ##   * transcripts/4.0_21_ones_own_right.txt

    ## File downloaded:
    ##   * Twilight Mirage 22: godspeed, glory Pt. 1
    ## Saved locally as:
    ##   * transcripts/4.0_22_godspeed_glory_pt_1.txt

    ## File downloaded:
    ##   * Twilight Mirage 23: godspeed, glory Pt. 2
    ## Saved locally as:
    ##   * transcripts/4.0_23_godspeed_glory_pt_2.txt

    ## File downloaded:
    ##   * Twilight Mirage 24: godspeed, glory Pt. 3
    ## Saved locally as:
    ##   * transcripts/4.0_24_godspeed_glory_pt_3.txt

    ## File downloaded:
    ##   * Twilight Mirage 25: The Miracle of the Mirage: The Dragon
    ## Saved locally as:
    ##   * transcripts/4.0_25_the_miracle_of_the_mirage_the_dragon.txt

    ## File downloaded:
    ##   * Twilight Mirage 27: The Miracle of the Mirage: The Siege
    ## Saved locally as:
    ##   * transcripts/4.0_27_the_miracle_of_the_mirage_the_siege.txt

    ## File downloaded:
    ##   * Twilight Mirage 30: This Year of Ours: The Muscle
    ## Saved locally as:
    ##   * transcripts/4.0_30_this_year_of_ours_the_muscle.txt

    ## File downloaded:
    ##   * Twilight Mirage 32: This Year of Ours: The Stitch
    ## Saved locally as:
    ##   * transcripts/4.0_32_this_year_of_ours_the_stitch.txt

    ## File downloaded:
    ##   * Twilight Mirage 33: This Year of Ours: The Speaker
    ## Saved locally as:
    ##   * transcripts/4.0_33_this_year_of_ours_the_speaker.txt

    ## File downloaded:
    ##   * Twilight Mirage 38: The Good Decision
    ## Saved locally as:
    ##   * transcripts/4.0_38_the_good_decision.txt

    ## File downloaded:
    ##   * Twilight Mirage 39: Staying Neutral
    ## Saved locally as:
    ##   * transcripts/4.0_39_staying_neutral.txt

    ## File downloaded:
    ##   * Twilight Mirage 40: Like What We Used To Do
    ## Saved locally as:
    ##   * transcripts/4.0_40_like_what_we_used_to_do.txt

    ## File downloaded:
    ##   * Twilight Mirage 41: No Value in a Closed Door
    ## Saved locally as:
    ##   * transcripts/4.0_41_no_value_in_a_closed_door.txt

    ## File downloaded:
    ##   * Twilight Mirage 42: Look the Other Way
    ## Saved locally as:
    ##   * transcripts/4.0_42_look_the_other_way.txt

    ## File downloaded:
    ##   * Twilight Mirage 43: The Center of the Storm
    ## Saved locally as:
    ##   * transcripts/4.0_43_the_center_of_the_storm.txt

    ## File downloaded:
    ##   * Twilight Mirage 44: The Pathways in the Woods
    ## Saved locally as:
    ##   * transcripts/4.0_44_the_pathways_in_the_woods.txt

    ## File downloaded:
    ##   * Twilight Mirage 45: Downtime on Gift-3
    ## Saved locally as:
    ##   * transcripts/4.0_45_downtime_on_gift-3.txt

    ## File downloaded:
    ##   * Twilight Mirage 46: Every Time We Leave, It Changes
    ## Saved locally as:
    ##   * transcripts/4.0_46_every_time_we_leave_it_changes.txt

    ## File downloaded:
    ##   * Twilight Mirage 47: Uncontrolled Fires
    ## Saved locally as:
    ##   * transcripts/4.0_47_uncontrolled_fires.txt

    ## File downloaded:
    ##   * Twilight Mirage 48: This Place Pushes Back
    ## Saved locally as:
    ##   * transcripts/4.0_48_this_place_pushes_back.txt

    ## File downloaded:
    ##   * Twilight Mirage 49: Bona Fide, Good Faith
    ## Saved locally as:
    ##   * transcripts/4.0_49_bona_fide_good_faith.txt

    ## File downloaded:
    ##   * Twilight Mirage 51: Open As Possible
    ## Saved locally as:
    ##   * transcripts/4.0_51_open_as_possible.txt

    ## File downloaded:
    ##   * Twilight Mirage 52: The Second Miracle
    ## Saved locally as:
    ##   * transcripts/4.0_52_the_second_miracle.txt

    ## File downloaded:
    ##   * Twilight Mirage 53: Our Flaws In A Vacuum, or the Promise We Made To Each Other
    ## Saved locally as:
    ##   * transcripts/4.0_53_our_flaws_in_a_vacuum_or_the_promise_we_made_to_each_other.txt

    ## File downloaded:
    ##   * Twilight Mirage 54: Downtime on Skein
    ## Saved locally as:
    ##   * transcripts/4.0_54_downtime_on_skein.txt

    ## File downloaded:
    ##   * Twilight Mirage 55: The Vault of Anticipation Pt. 1
    ## Saved locally as:
    ##   * transcripts/4.0_55_the_vault_of_anticipation_pt_1.txt

    ## File downloaded:
    ##   * Twilight Mirage 56: The Vault of Anticipation Pt. 2
    ## Saved locally as:
    ##   * transcripts/4.0_56_the_vault_of_anticipation_pt_2.txt

    ## File downloaded:
    ##   * Twilight Mirage 57: The Siege of the Lineage Brighton Pt
    ## Saved locally as:
    ##   * transcripts/4.0_57_the_siege_of_the_lineage_brighton_pt_1.txt

    ## File downloaded:
    ##   * Twilight Mirage 58: The Siege of the Lineage Brighton Pt. 2
    ## Saved locally as:
    ##   * transcripts/4.0_58_the_siege_of_the_lineage_brighton_pt_2.txt

    ## File downloaded:
    ##   * Twilight Mirage 59: The Siege of the Lineage Brighton Pt. 3
    ## Saved locally as:
    ##   * transcripts/4.0_59_the_siege_of_the_lineage_brighton_pt_3.txt

    ## File downloaded:
    ##   * Twilight Mirage 60: The Restitution Of All Things Pt. 1
    ## Saved locally as:
    ##   * transcripts/4.0_60_the_restitution_of_all_things_pt_1.txt

    ## File downloaded:
    ##   * Twilight Mirage 61: The Restitution of All Things Pt. 2
    ## Saved locally as:
    ##   * transcripts/4.0_61_the_restitution_of_all_things_pt_2.txt

    ## File downloaded:
    ##   * Twilight Mirage 62: The Feast of Patina
    ## Saved locally as:
    ##   * transcripts/4.0_62_the_feast_of_patina.txt

    ## File downloaded:
    ##   * Twilight Mirage 63: Guaranteed Events, Or: An Accounting of the Time When We Built The Machine
    ## Saved locally as:
    ##   * transcripts/4.0_63_guaranteed_events_or_an_accounting_of_the_time_when_we_built_the_machine.txt

    ## File downloaded:
    ##   * Twilight Mirage 64: Futura Free Pt. 1
    ## Saved locally as:
    ##   * transcripts/4.0_64_futura_free_pt_1.txt

    ## File downloaded:
    ##   * Twilight Mirage 67- Futura Free Pt 4
    ## Saved locally as:
    ##   * transcripts/4.0_67_futura_free_pt_4.txt

    ## File downloaded:
    ##   * Spring in Hieron 01: The Touch of Others
    ## Saved locally as:
    ##   * transcripts/5.0_01_the_touch_of_others.txt

    ## File downloaded:
    ##   * Spring in Hieron 02: Why Are You Here?
    ## Saved locally as:
    ##   * transcripts/5.0_02_why_are_you_here.txt

    ## File downloaded:
    ##   * Spring in Hieron 03: Hospitable to You
    ## Saved locally as:
    ##   * transcripts/5.0_03_hospitable_to_you.txt

    ## File downloaded:
    ##   * Spring in Hieron 04: Material Effort
    ## Saved locally as:
    ##   * transcripts/5.0_04_material_effort.txt

    ## File downloaded:
    ##   * Spring in Hieron 05: Try To Live a Good Life
    ## Saved locally as:
    ##   * transcripts/5.0_05_try_to_live_a_good_life.txt

    ## File downloaded:
    ##   * Spring in Hieron 06: Something You've Practiced
    ## Saved locally as:
    ##   * transcripts/5.0_06_something_youve_practiced.txt

    ## File downloaded:
    ##   * Spring in Hieron 07: Structure and Purpose
    ## Saved locally as:
    ##   * transcripts/5.0_07_structure_and_purpose.txt

    ## File downloaded:
    ##   * Spring in Hieron 08: Sympathetic Objects
    ## Saved locally as:
    ##   * transcripts/5.0_08_sympathetic_objects.txt

    ## File downloaded:
    ##   * Spring in Hieron 09: A Certain Way
    ## Saved locally as:
    ##   * transcripts/5.0_09_a_certain_way.txt

    ## File downloaded:
    ##   * Spring in Hieron 10: Who Vouches
    ## Saved locally as:
    ##   * transcripts/5.0_10_who_vouches.txt

    ## File downloaded:
    ##   * Spring in Hieron 11: Cut Off the Head
    ## Saved locally as:
    ##   * transcripts/5.0_11_cut_off_the_head.txt

    ## File downloaded:
    ##   * Spring in Hieron 12: Long Promises
    ## Saved locally as:
    ##   * transcripts/5.0_12_long_promises.txt

    ## File downloaded:
    ##   * Spring in Hieron 13: The Time of Gods
    ## Saved locally as:
    ##   * transcripts/5.0_13_the_time_of_gods.txt

    ## File downloaded:
    ##   * Spring in Hieron 14: Neighbors To Be Made
    ## Saved locally as:
    ##   * transcripts/5.0_14_neighbors_to_be_made.txt

    ## File downloaded:
    ##   * Spring in Hieron 15: The Breath of Hieron
    ## Saved locally as:
    ##   * transcripts/5.0_15_the_breath_of_hieron.txt

    ## File downloaded:
    ##   * Spring in Hieron 16: Music Through A Wall
    ## Saved locally as:
    ##   * transcripts/5.0_16_music_through_a_wall.txt

    ## File downloaded:
    ##   * Spring in Hieron 17: A Desire To Live
    ## Saved locally as:
    ##   * transcripts/5.0_17_a_desire_to_live.txt

    ## File downloaded:
    ##   * Spring in Hieron 18: A Simple Answer
    ## Saved locally as:
    ##   * transcripts/5.0_18_a_simple_answer.txt

    ## File downloaded:
    ##   * Spring in Hieron 19: A Night Like Tonight
    ## Saved locally as:
    ##   * transcripts/5.0_19_a_night_like_tonight.txt

    ## File downloaded:
    ##   * Spring in Hieron 20: Passing of Voices
    ## Saved locally as:
    ##   * transcripts/5.0_20_passing_of_voices.txt

    ## File downloaded:
    ##   * Spring in Hieron 21: A Fountain of Information
    ## Saved locally as:
    ##   * transcripts/5.0_21_a_fountain_of_information.txt

    ## File downloaded:
    ##   * Spring in Hieron 22: Better Every Time
    ## Saved locally as:
    ##   * transcripts/5.0_22_better_every_time.txt

    ## File downloaded:
    ##   * Spring in Hieron 23: The Heat Will Warm Your Bones
    ## Saved locally as:
    ##   * transcripts/5.0_23_the_heat_will_warm_your_bones.txt

    ## File downloaded:
    ##   * Spring in Hieron 24: Closer To Kin
    ## Saved locally as:
    ##   * transcripts/5.0_24_closer_to_kin.txt

    ## File downloaded:
    ##   * Spring in Hieron 25: The Second Sun
    ## Saved locally as:
    ##   * transcripts/5.0_25_the_second_sun.txt

    ## File downloaded:
    ##   * Spring in Hieron 26: An Attractive Offer
    ## Saved locally as:
    ##   * transcripts/5.0_26_an_attractive_offer.txt

    ## File downloaded:
    ##   * Spring in Hieron 27: A Place and a Time
    ## Saved locally as:
    ##   * transcripts/5.0_27_a_place_and_a_time.txt

    ## File downloaded:
    ##   * Spring in Hieron 28: Significant Concern
    ## Saved locally as:
    ##   * transcripts/5.0_28_significant_concern.txt

    ## File downloaded:
    ##   * Spring in Hieron 29: Deeper Than Flesh
    ## Saved locally as:
    ##   * transcripts/5.0_29_deeper_than_flesh.txt

    ## File downloaded:
    ##   * Spring in Hieron 30: A Safer Second
    ## Saved locally as:
    ##   * transcripts/5.0_30_a_safer_second.txt

    ## File downloaded:
    ##   * Spring in Hieron 31: A Handful of People
    ## Saved locally as:
    ##   * transcripts/5.0_31_a_handful_of_people.txt

    ## File downloaded:
    ##   * Spring in Hieron 32: Insurmountable Odds
    ## Saved locally as:
    ##   * transcripts/5.0_32_insurmountable_odds.txt

    ## File downloaded:
    ##   * Spring in Hieron 33: Particular Malice
    ## Saved locally as:
    ##   * transcripts/5.0_33_particular_malice.txt

    ## File downloaded:
    ##   * Spring in Hieron 36: Terrible and Necessary
    ## Saved locally as:
    ##   * transcripts/5.0_36_terrible_and_necessary.txt

    ## File downloaded:
    ##   * Spring in Hieron 37: Ingenuity and Love
    ## Saved locally as:
    ##   * transcripts/5.0_37_ingenuity_and_love.txt

    ## File downloaded:
    ##   * Spring in Hieron 38: See Where You're Standing
    ## Saved locally as:
    ##   * transcripts/5.0_38_see_where_youre_standing.txt

    ## File downloaded:
    ##   * Spring in Hieron 39: The Second Spring Pt 1
    ## Saved locally as:
    ##   * transcripts/5.0_39_the_second_spring_pt_1.txt

    ## File downloaded:
    ##   * Spring in Hieron 40: The Second Spring Pt. 2
    ## Saved locally as:
    ##   * transcripts/5.0_40_the_second_spring_pt_2.txt
