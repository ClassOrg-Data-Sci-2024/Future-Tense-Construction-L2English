FutureTenseConstructionL2English
================
Daniel Crawford
04/02/2024

- [Correlation of Future Tense Construction Preference with Proficiency
  Scores for English L2
  Learners](#correlation-of-future-tense-construction-preference-with-proficiency-scores-for-english-l2-learners)
  - [Load Data](#load-data)
  - [Format the Data](#format-the-data)
- [Session Info](#session-info)

EXISTING

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

``` r
#A function to create a tokenized df with columns: token | lemma | POS
#Input is a string
tlp_text_to_df = function(x){
  text_df = x %>% 
    #Remove "[" character (at beginning of string)
    str_remove("^\\[\\(") %>% 
    #Remove "]" character (at end of string)
    str_remove("\\)\\]$") %>% 
    #split tuples
    str_split_1("\\), \\(") %>% 
    #remove 's
    str_remove_all("'") %>% 
    #convert to tibble
    as.tibble() %>% 
    #remove rows of commas - these are not relevant or impactful on analysis
    filter(!startsWith(value, ",,")) %>% 
    #separate into columns
    separate(value, into = c('token','lemma','POS'), sep = ',')
  
  return(text_df)
  
}
```

``` r
#Create tokenized_df, each row is a token
# tokenized_df_unnested = PELIC_compiled %>% 
#   #create tokenized df from the tok_lem_POS string
#   mutate(tokenized_nested_df = map(tok_lem_POS, tlp_text_to_df)) %>% 
#   #keep the unique identifier of answer id
#   select(answer_id, tokenized_nested_df) %>% 
#   #make the df longer and unndest
#   unnest(tokenized_nested_df) %>% 
#   
  
#tokenized_df_unnested %>% write.csv('data/tokenized_df_unnested.csv')

tokenized_df_unnested = read.csv('data/tokenized_df_unnested.csv')
```

### Format the Data

``` r
#Create counts_data, each row is a token
counts_data =  tokenized_df_unnested %>% 
  #if there is a 'will' token that is a modal, put 1 in new column, else 0
  mutate(is_will_construction = if_else(token == 'will' & trimws(POS) == 'MD', 1,0)) %>% 
  #if there is a 'going' token followed by 'to' then a verb within 2 words, put 1 in new column
  mutate(is_goingTo_construction = 
            if_else(
              token == "going" & lead(token) == "to" & (
                #lead allows to 'look ahead'
                startsWith(trimws(lead(POS, 2)), 'V') | 
                startsWith(trimws(lead(POS, 3)), 'V')
              ),1,0)) %>% 
  #get the answer id and counts
  select(answer_id, is_will_construction, is_goingTo_construction) %>%
  group_by(answer_id) %>% 
  #sum counts by answer ID
  summarise(
    count_will_construction = sum(is_will_construction), 
    count_goingTo_construction = sum(is_goingTo_construction)
  )

#this is the final data frame with the students info
countruction_counts_and_student_info  = PELIC_compiled %>% 
  #joing in the tokenized df with counts, on answer ID
  left_join(counts_data, by = "answer_id") %>% 
  #get the anon_id and counts, we do not need the text info
  select(anon_id, count_will_construction, count_goingTo_construction) %>% 
  group_by(anon_id) %>% 
  #count construction usage by student
  summarise(
    count_will_construction = sum(count_will_construction), 
    count_goingTo_construction = sum(count_goingTo_construction)
  ) %>% 
  #bring in student info
  left_join(student_info, by = "anon_id")
```

``` r
#score Data
score_data = as_tibble(read.csv(url('https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/corpus_files/test_scores.csv')))

#Find students who took the test more than once
n_occur <- data.frame(table(score_data$anon_id))
multiple_ids = n_occur[n_occur$Freq > 1,]$Var1

#join in scores of students
countruction_counts_and_student_info_with_scores = countruction_counts_and_student_info %>% 
  inner_join(score_data %>% filter(!(trimws(anon_id) %in% multiple_ids)), by = 'anon_id')
```

``` r
countruction_counts_and_student_info_with_scores %>% 
  write.csv('FINAL_DATA_countruction_counts_and_student_info_with_scores.csv')
```

My strategy for importing the data is to find construction in question
in the text, and then to join in the student demographic information.
The `FINAL_DATA_countruction_counts_and_student_info_with_scores.csv` is
the end product of my data.

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
