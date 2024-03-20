FutureTenseConstructionL2English
================
Daniel Crawford
03/20/2024

- [Correlation of Future Tense Construction Preference with Proficiency
  Scores for English L2
  Learners](#correlation-of-future-tense-construction-preference-with-proficiency-scores-for-english-l2-learners)
  - [Load Data](#load-data)
  - [Set the regex - these will likely
    change](#set-the-regex---these-will-likely-change)
  - [Format the Data](#format-the-data)
- [Session Info](#session-info)

# Correlation of Future Tense Construction Preference with Proficiency Scores for English L2 Learners

``` r
#Import Packages
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

### Load Data

``` r
#Read in Data from PELIC: 
data = as.tibble(read.csv(url("https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/PELIC_compiled.csv")))
```

    ## Warning: `as.tibble()` was deprecated in tibble 2.0.0.
    ## ℹ Please use `as_tibble()` instead.
    ## ℹ The signature and semantics have changed, see `?as_tibble`.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

``` r
data
```

    ## # A tibble: 46,204 × 15
    ##    answer_id anon_id L1      gender semester  placement_test course_id level_id
    ##        <int> <chr>   <chr>   <chr>  <chr>              <dbl>     <int>    <int>
    ##  1         1 eq0     Arabic  Male   2006_fall             NA       149        4
    ##  2         2 am8     Thai    Female 2006_fall             NA       149        4
    ##  3         3 dk5     Turkish Female 2006_fall             NA       115        4
    ##  4         4 dk5     Turkish Female 2006_fall             NA       115        4
    ##  5         5 ad1     Korean  Female 2006_fall             NA       115        4
    ##  6         6 ad1     Korean  Female 2006_fall             NA       115        4
    ##  7         7 eg5     Korean  Female 2006_fall             NA       115        4
    ##  8         8 eg5     Korean  Female 2006_fall             NA       115        4
    ##  9         9 ad1     Korean  Female 2006_fall             NA       115        4
    ## 10        10 ad1     Korean  Female 2006_fall             NA       115        4
    ## # ℹ 46,194 more rows
    ## # ℹ 7 more variables: class_id <chr>, question_id <int>, version <int>,
    ## #   text_len <int>, text <chr>, tokens <chr>, tok_lem_POS <chr>

``` r
#Need to get demographic and proficiency scores for each student

student_info = read.csv(url("https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/corpus_files/student_information.csv"))
```

### Set the regex - these will likely change

``` r
#RegEx for 'will' construction
RE_will = "will"

#RegEx for 'going to' construction
RE_goingto = "going to"
```

### Format the Data

``` r
data %>% 
  #Optional Slicer - can have long run time if too many rows are used
  #slice(1:10) %>% 
  #Get just the id and the text - this is preferable and equivalent to having to concat all the text form a user
  select(anon_id,text) %>% 
  #Count number of occurrences of will construction
  mutate(will_ct = 
           map_int(text,
               ~ .x %>%
                  str_count(RE_will)
           )
  ) %>% 
  #Count number of occurrences going to constructions
  mutate(goingto_ct = 
           map_int(text,
               ~ .x %>%
                  str_count(RE_goingto)
           )
  ) %>% 
  #Group By individual
  group_by(anon_id) %>% 
  #sum the construction counts
  summarise_at(c("will_ct", "goingto_ct"),sum) %>% 
  left_join(
    (
      student_info %>% 
        select(anon_id,gender,gender,birth_year,native_language,yrs_in_english_environment,yrs_of_english_learning)
    ),
      by = 'anon_id'
    
  )
```

    ## # A tibble: 1,177 × 8
    ##    anon_id will_ct goingto_ct gender  birth_year native_language
    ##    <chr>     <int>      <int> <chr>        <int> <chr>          
    ##  1 aa0           7          0 Male          1984 Spanish        
    ##  2 aa1          40          0 Male          1983 Chinese        
    ##  3 aa2          20          2 Male          1967 Arabic         
    ##  4 aa3           3          1 Unknown         NA Chinese        
    ##  5 aa5           2          0 Unknown         NA Chinese        
    ##  6 aa8           1         23 Female        1976 Korean         
    ##  7 aa9           4          4 Male          1987 Korean         
    ##  8 ab1           1          0 Male            NA Hebrew         
    ##  9 ab2          11          0 Female          NA Arabic         
    ## 10 ab6           4          0 Male            NA Chinese        
    ## # ℹ 1,167 more rows
    ## # ℹ 2 more variables: yrs_in_english_environment <chr>,
    ## #   yrs_of_english_learning <chr>

# Session Info

``` r
sessionInfo()
```

    ## R version 4.3.2 (2023-10-31 ucrt)
    ## Platform: x86_64-w64-mingw32/x64 (64-bit)
    ## Running under: Windows 11 x64 (build 22631)
    ## 
    ## Matrix products: default
    ## 
    ## 
    ## locale:
    ## [1] LC_COLLATE=English_United States.utf8 
    ## [2] LC_CTYPE=English_United States.utf8   
    ## [3] LC_MONETARY=English_United States.utf8
    ## [4] LC_NUMERIC=C                          
    ## [5] LC_TIME=English_United States.utf8    
    ## 
    ## time zone: America/New_York
    ## tzcode source: internal
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] lubridate_1.9.2 forcats_1.0.0   stringr_1.5.0   dplyr_1.1.3    
    ##  [5] purrr_1.0.2     readr_2.1.4     tidyr_1.3.0     tibble_3.2.1   
    ##  [9] ggplot2_3.4.3   tidyverse_2.0.0
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] gtable_0.3.4      crayon_1.5.2      compiler_4.3.2    tidyselect_1.2.0 
    ##  [5] scales_1.2.1      yaml_2.3.7        fastmap_1.1.1     R6_2.5.1         
    ##  [9] generics_0.1.3    knitr_1.44        munsell_0.5.0     pillar_1.9.0     
    ## [13] tzdb_0.4.0        rlang_1.1.1       utf8_1.2.3        stringi_1.7.12   
    ## [17] xfun_0.40         timechange_0.2.0  cli_3.6.1         withr_2.5.0      
    ## [21] magrittr_2.0.3    digest_0.6.33     grid_4.3.2        rstudioapi_0.15.0
    ## [25] hms_1.1.3         lifecycle_1.0.3   vctrs_0.6.3       evaluate_0.21    
    ## [29] glue_1.6.2        fansi_1.0.4       colorspace_2.1-0  rmarkdown_2.25   
    ## [33] tools_4.3.2       pkgconfig_2.0.3   htmltools_0.5.6
