FutureTenseConstructionL2English
================
Daniel Crawford
04/02/2024
EXISTING

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

Raw data can be found
[here](https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/PELIC_compiled.csv).

``` r
#Read in Data from PELIC: 
PELIC_compiled = as_tibble(read.csv(url("https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/PELIC_compiled.csv"), fileEncoding = "ISO-8859-1"))
nrow(PELIC_compiled)
```

    ## [1] 46204

``` r
ncol(PELIC_compiled)
```

    ## [1] 15

``` r
head(PELIC_compiled)
```

    ## # A tibble: 6 × 15
    ##   answer_id anon_id L1      gender semester  placement_test course_id level_id
    ##       <int> <chr>   <chr>   <chr>  <chr>              <dbl>     <int>    <int>
    ## 1         1 eq0     Arabic  Male   2006_fall             NA       149        4
    ## 2         2 am8     Thai    Female 2006_fall             NA       149        4
    ## 3         3 dk5     Turkish Female 2006_fall             NA       115        4
    ## 4         4 dk5     Turkish Female 2006_fall             NA       115        4
    ## 5         5 ad1     Korean  Female 2006_fall             NA       115        4
    ## 6         6 ad1     Korean  Female 2006_fall             NA       115        4
    ## # ℹ 7 more variables: class_id <chr>, question_id <int>, version <int>,
    ## #   text_len <int>, text <chr>, tokens <chr>, tok_lem_POS <chr>

The data contains about 46,000 entries, and a vast about of background
data on the students. Of note will be L1, level, and profiency.

``` r
#Need to get demographic and proficiency scores for each student

student_info = as_tibble(read.csv(url("https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/corpus_files/student_information.csv")))
nrow(student_info)
```

    ## [1] 1313

``` r
ncol(student_info)
```

    ## [1] 21

``` r
head(student_info)
```

    ## # A tibble: 6 × 21
    ##   anon_id gender birth_year native_language language_used_at_home
    ##   <chr>   <chr>       <int> <chr>           <chr>                
    ## 1 ez9     Male         1978 Arabic          Arabic               
    ## 2 gm3     Male         1980 Arabic          Arabic               
    ## 3 fg5     Male         1938 Nepali          Nepali               
    ## 4 ce5     Female       1984 Korean          Korean               
    ## 5 fi7     Female       1982 Korean          Korean;Japanese      
    ## 6 ds7     Female       1985 Korean          Korean               
    ## # ℹ 16 more variables: non_native_language_1 <chr>, yrs_of_study_lang1 <chr>,
    ## #   study_in_classroom_lang1 <chr>, ways_of_study_lang1 <chr>,
    ## #   non_native_language_2 <chr>, yrs_of_study_lang2 <chr>,
    ## #   study_in_classroom_lang2 <chr>, ways_of_study_lang2 <chr>,
    ## #   non_native_language_3 <chr>, yrs_of_study_lang3 <chr>,
    ## #   study_in_classroom_lang3 <chr>, ways_of_study_lang3 <chr>,
    ## #   course_history <chr>, yrs_of_english_learning <chr>, …

The student information for the 1,313 students expands on the
demographics, and will be joined in later to the data.

### Set the regex - these will likely change

``` r
#RegEx for 'will' construction
RE_will = "will"

#RegEx for 'going to' construction
RE_goingto = "going to"
```

``` r
will_lemmas = PELIC_compiled %>% 
  filter(str_detect(text, RE_will)) %>% 
  select(tok_lem_POS) %>% 
  mutate(code = str_extract_all(tok_lem_POS, "'will',[^\\)]*")) %>% 
  unnest(code) %>% 
  mutate(code = str_split_i(code, ",",-1)) %>% 
  select(code) %>% 
  count(code)

will_lemmas
```

    ## # A tibble: 4 × 2
    ##   code         n
    ##   <chr>    <int>
    ## 1 " 'MD'"  17510
    ## 2 " 'VB'"      5
    ## 3 " 'VBD'"     4
    ## 4 " 'VBP'"     4

``` r
convert_to_list <- function(str) {
  return(gsub("[^[:alnum:]]","",unlist(strsplit(gsub("\\(|\\)", "", unlist(strsplit(gsub("^\\[|\\]$", "",str),","))), ",\\s*"))))
}


check_future <- function(vec){
  text = convert_to_list(vec)
  idx = match('going',text)
  
  
  if(!is.na(text[(idx+5)])){
    if(text[(idx+5)] == 'TO'){
      if(startsWith(text[(idx+8)],"V")){
        return('GoingToFuture')
      }else{
        return('GoingNoV')
      }
    }else{
      return('GoingNoTo')
    }
  }else{
   return('Error')
  }
    

}
```

``` r
going_lemmas = PELIC_compiled %>% 
  slice(1:200) %>% 
  filter(str_detect(text, "going")) %>% 
  select(tok_lem_POS)

going_lemmas
```

    ## # A tibble: 18 × 1
    ##    tok_lem_POS                                                                  
    ##    <chr>                                                                        
    ##  1 "[('Ten', 'ten', 'CD'), ('years', 'year', 'NNS'), ('ago', 'ago', 'RB'), (','…
    ##  2 "[('In', 'in', 'IN'), ('Korea', 'Korea', 'NNP'), ('it', 'it', 'PRP'), ('is',…
    ##  3 "[('The', 'the', 'DT'), ('accident', 'accident', 'NN'), ('did', 'do', 'VBD')…
    ##  4 "[('When', 'when', 'WRB'), ('I', 'I', 'PRP'), ('was', 'be', 'VBD'), ('in', '…
    ##  5 "[('5T', '5T', 'CD'), ('22', '22', 'CD'), ('/', '/', 'JJ'), ('09', '09', 'CD…
    ##  6 "[('Some', 'some', 'DT'), ('people', 'people', 'NNS'), ('said', 'say', 'VBD'…
    ##  7 "[('``', '``', '``'), ('Not', 'not', 'RB'), ('all', 'all', 'DT'), ('learning…
    ##  8 "[('Neighbors', 'neighbor', 'NNS'), ('Neighbor', 'Neighbor', 'NNP'), ('is', …
    ##  9 "[('Gaining', 'gain', 'VBG'), ('knowledge', 'knowledge', 'NN'), ('comes', 'c…
    ## 10 "[('Gaining', 'gain', 'VBG'), ('knowledge', 'knowledge', 'NN'), ('comes', 'c…
    ## 11 "[('Each', 'each', 'DT'), ('person', 'person', 'NN'), ('has', 'have', 'VBZ')…
    ## 12 "[('The', 'the', 'DT'), ('expectations', 'expectation', 'NNS'), ('of', 'of',…
    ## 13 "[('Reflecting', 'reflect', 'VBG'), ('on', 'on', 'IN'), (',', ',', ','), ('t…
    ## 14 "[('Hello', 'Hello', 'NNP'), (',', ',', ','), ('welcome', 'welcome', 'NN'), …
    ## 15 "[('I', 'I', 'PRP'), ('believe', 'believe', 'VBP'), ('that', 'that', 'IN'), …
    ## 16 "[('Welcome', 'welcome', 'JJ'), ('to', 'to', 'TO'), ('read', 'read', 'VB'), …
    ## 17 "[('I', 'I', 'PRP'), ('was', 'be', 'VBD'), ('working', 'work', 'VBG'), ('as'…
    ## 18 "[('I', 'I', 'PRP'), ('grew', 'grow', 'VBD'), ('up', 'up', 'RP'), ('in', 'in…

``` r
map(map(going_lemmas$tok_lem_POS,convert_to_list),check_future)
```

    ## [[1]]
    ## [1] "GoingNoV"
    ## 
    ## [[2]]
    ## [1] "GoingNoTo"
    ## 
    ## [[3]]
    ## [1] "GoingToFuture"
    ## 
    ## [[4]]
    ## [1] "GoingNoV"
    ## 
    ## [[5]]
    ## [1] "GoingToFuture"
    ## 
    ## [[6]]
    ## [1] "GoingToFuture"
    ## 
    ## [[7]]
    ## [1] "GoingNoV"
    ## 
    ## [[8]]
    ## [1] "GoingToFuture"
    ## 
    ## [[9]]
    ## [1] "GoingNoTo"
    ## 
    ## [[10]]
    ## [1] "GoingNoTo"
    ## 
    ## [[11]]
    ## [1] "GoingNoTo"
    ## 
    ## [[12]]
    ## [1] "Error"
    ## 
    ## [[13]]
    ## [1] "GoingNoTo"
    ## 
    ## [[14]]
    ## [1] "GoingNoV"
    ## 
    ## [[15]]
    ## [1] "GoingToFuture"
    ## 
    ## [[16]]
    ## [1] "GoingNoV"
    ## 
    ## [[17]]
    ## [1] "GoingNoV"
    ## 
    ## [[18]]
    ## [1] "GoingToFuture"

### Format the Data

``` r
PELIC_compiled %>% 
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
    
  ) %>% 
  write.csv("working_data.csv")
```

My strategy for importing the data is to find construction in question
in the text, and then to join in the student demographic information.
The `working_data.csv` is the current end product of my data.

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
    ##  [1] gtable_0.3.4      compiler_4.3.2    tidyselect_1.2.0  scales_1.2.1     
    ##  [5] yaml_2.3.7        fastmap_1.1.1     R6_2.5.1          generics_0.1.3   
    ##  [9] knitr_1.44        munsell_0.5.0     pillar_1.9.0      tzdb_0.4.0       
    ## [13] rlang_1.1.1       utf8_1.2.3        stringi_1.7.12    xfun_0.40        
    ## [17] timechange_0.2.0  cli_3.6.1         withr_2.5.0       magrittr_2.0.3   
    ## [21] digest_0.6.33     grid_4.3.2        rstudioapi_0.15.0 hms_1.1.3        
    ## [25] lifecycle_1.0.3   vctrs_0.6.3       evaluate_0.21     glue_1.6.2       
    ## [29] fansi_1.0.4       colorspace_2.1-0  rmarkdown_2.25    tools_4.3.2      
    ## [33] pkgconfig_2.0.3   htmltools_0.5.6
