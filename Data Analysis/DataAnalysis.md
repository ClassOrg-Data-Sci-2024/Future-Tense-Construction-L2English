FutureTenseConstructionL2English
================
Daniel Crawford
04/24/2024

- [Pull in final data](#pull-in-final-data)
  - [Pull in Final Data from Data
    Processing](#pull-in-final-data-from-data-processing)
  - [Pull in Student Info](#pull-in-student-info)
  - [Pull in PELIC Compiled](#pull-in-pelic-compiled)
- [Summary Statistics](#summary-statistics)
  - [Distributions of Scores](#distributions-of-scores)
  - [Scale Proficiency Scores](#scale-proficiency-scores)
- [Correlation Analysis](#correlation-analysis)
- [Random Intercepts Analysis](#random-intercepts-analysis)
  - [Token Level Analysis](#token-level-analysis)
    - [Predict ‘will’ with Mean of Prof Scores using Random Intercepts
      and Random
      Slopes](#predict-will-with-mean-of-prof-scores-using-random-intercepts-and-random-slopes)
    - [Predict ‘will’ with Level using Random
      Intercepts](#predict-will-with-level-using-random-intercepts)

``` r
knitr::opts_chunk$set(fig.path = paste0(dirname(getwd()),'/images/DataAnalysis-'))
```

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

``` r
library(lme4)
```

    ## Loading required package: Matrix
    ## 
    ## Attaching package: 'Matrix'
    ## 
    ## The following objects are masked from 'package:tidyr':
    ## 
    ##     expand, pack, unpack

# Pull in final data

## Pull in Final Data from Data Processing

``` r
if (file.exists(paste0(dirname(getwd()),'/Data Files/FINAL_DATA_countruction_counts_and_student_info_with_scores.csv'))){
  print('Reading in File from directory')
  construction_counts = as_tibble(read.csv(paste0(dirname(getwd()),'/Data Files/FINAL_DATA_countruction_counts_and_student_info_with_scores.csv')))
}else{
 print('The final data dile you are looking for (FINAL_DATA_countruction_counts_and_student_info_with_scores.csv) does not appear to be in a directory.') 
}
```

    ## [1] "Reading in File from directory"

## Pull in Student Info

``` r
student_info = as_tibble(read.csv(url("https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/corpus_files/student_information.csv")))
```

## Pull in PELIC Compiled

``` r
if (file.exists(paste0(dirname(getwd()),'/large_data/pelic_compiled.csv'))){
  print('Reading in File from directory')
  PELIC_compiled = read.csv(paste0(dirname(getwd()),'/large_data/pelic_compiled.csv'))
}else{
  print('Reading in File from url')
  PELIC_compiled = as_tibble(read.csv(url("https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/PELIC_compiled.csv"), fileEncoding = "ISO-8859-1"))
}
```

    ## [1] "Reading in File from directory"

# Summary Statistics

``` r
#Counts of Each Construction
construction_counts %>% 
  select(count_will_construction, count_goingTo_construction) %>% 
  summarise_all(sum)
```

    ## # A tibble: 1 × 2
    ##   count_will_construction count_goingTo_construction
    ##                     <int>                      <int>
    ## 1                   16762                       1728

``` r
#Get proportions
construction_props = construction_counts %>% 
  mutate(prop_goingTo_construction = count_goingTo_construction/(count_will_construction+count_goingTo_construction), .after = anon_id) %>% 
  mutate(prop_will_construction = count_will_construction/(count_will_construction+count_goingTo_construction), .after = anon_id)

construction_props
```

    ## # A tibble: 1,078 × 35
    ##        X anon_id prop_will_construction prop_goingTo_construction
    ##    <int> <chr>                    <dbl>                     <dbl>
    ##  1     1 aa0                     1                         0     
    ##  2     2 aa1                     1                         0     
    ##  3     3 aa2                     0.913                     0.0870
    ##  4     4 aa3                     0.75                      0.25  
    ##  5     5 aa5                     1                         0     
    ##  6     6 aa8                     0.0833                    0.917 
    ##  7     7 aa9                     0.833                     0.167 
    ##  8     8 ab2                     1                         0     
    ##  9     9 ab6                     1                         0     
    ## 10    10 ab8                     1                         0     
    ## # ℹ 1,068 more rows
    ## # ℹ 31 more variables: count_will_construction <int>,
    ## #   count_goingTo_construction <int>, gender <chr>, birth_year <int>,
    ## #   native_language <chr>, language_used_at_home <chr>,
    ## #   non_native_language_1 <chr>, yrs_of_study_lang1 <chr>,
    ## #   study_in_classroom_lang1 <chr>, ways_of_study_lang1 <chr>,
    ## #   non_native_language_2 <chr>, yrs_of_study_lang2 <chr>, …

## Distributions of Scores

``` r
#Visualize Raw Distribution of Scores

construction_props %>% 
  #only use scores
  select(LCT_Score, MTELP_I, MTELP_II, MTELP_III, MTELP_Conv_Score, Writing_Sample) %>% 
  pivot_longer(everything()) %>% 
  ggplot(aes(x = value)) +
  geom_histogram() +
  facet_wrap(~name)+
  labs(title = "Proficiency Score Distributions", x = "Value (Raw)", y = "Frequency") +
  theme_minimal()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-distScores-1.png)<!-- -->

``` r
construction_props %>% 
  #only check writing samples
  select(Writing_Sample) %>% 
  pivot_longer(everything()) %>% 
  ggplot(aes(x = value)) +
  geom_histogram() +
  facet_wrap(~name)+
  labs(title = "Proficiency Score Distributions", x = "Value (Raw)", y = "Frequency") +
  theme_minimal()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-distWritingScores-1.png)<!-- -->

## Scale Proficiency Scores

``` r
#Scale Scores

scaled_construction_props = construction_props %>% 
  select(anon_id, prop_will_construction, prop_goingTo_construction, LCT_Score, MTELP_I, MTELP_II, MTELP_III, MTELP_Conv_Score, Writing_Sample) %>% 
  mutate(across(-c(anon_id,prop_will_construction, prop_goingTo_construction), ~ scale(.x))) 
  
  
scaled_construction_props
```

    ## # A tibble: 1,078 × 9
    ##    anon_id prop_will_construction prop_goingTo_construction LCT_Score[,1]
    ##    <chr>                    <dbl>                     <dbl>         <dbl>
    ##  1 aa0                     1                         0              1.51 
    ##  2 aa1                     1                         0             -1.10 
    ##  3 aa2                     0.913                     0.0870        -0.613
    ##  4 aa3                     0.75                      0.25           1.51 
    ##  5 aa5                     1                         0             -0.940
    ##  6 aa8                     0.0833                    0.917          0.855
    ##  7 aa9                     0.833                     0.167         -2.08 
    ##  8 ab2                     1                         0             -0.613
    ##  9 ab6                     1                         0              0.529
    ## 10 ab8                     1                         0             -0.940
    ## # ℹ 1,068 more rows
    ## # ℹ 5 more variables: MTELP_I <dbl[,1]>, MTELP_II <dbl[,1]>,
    ## #   MTELP_III <dbl[,1]>, MTELP_Conv_Score <dbl[,1]>, Writing_Sample <dbl[,1]>

``` r
#Get Scaled values, but only for students who used future tense at least 30 times. 

scaled_construction_props_30plus = construction_counts %>% 
  filter(count_will_construction+count_goingTo_construction >= 30) %>% 
  mutate(prop_goingTo_construction = count_goingTo_construction/(count_will_construction+count_goingTo_construction), .after = anon_id) %>% 
  mutate(prop_will_construction = count_will_construction/(count_will_construction+count_goingTo_construction), .after = anon_id) %>% 
  select(anon_id, prop_will_construction, prop_goingTo_construction, LCT_Score, MTELP_I, MTELP_II, MTELP_III, MTELP_Conv_Score, Writing_Sample) %>% 
  mutate(across(-c(anon_id,prop_will_construction, prop_goingTo_construction), ~ scale(.x)))
  

scaled_construction_props_30plus
```

    ## # A tibble: 187 × 9
    ##    anon_id prop_will_construction prop_goingTo_construction LCT_Score[,1]
    ##    <chr>                    <dbl>                     <dbl>         <dbl>
    ##  1 aa1                      1                        0             -0.821
    ##  2 ad8                      0.830                    0.170          1.24 
    ##  3 ae3                      0.875                    0.125         -0.684
    ##  4 ae9                      1                        0              1.51 
    ##  5 af0                      0.885                    0.115         -0.136
    ##  6 ag9                      0.745                    0.255          0.413
    ##  7 ah1                      0.703                    0.297          0.413
    ##  8 ai1                      0.971                    0.0288         0.276
    ##  9 ai3                      0.706                    0.294         -1.51 
    ## 10 aj9                      0.95                     0.05           0.824
    ## # ℹ 177 more rows
    ## # ℹ 5 more variables: MTELP_I <dbl[,1]>, MTELP_II <dbl[,1]>,
    ## #   MTELP_III <dbl[,1]>, MTELP_Conv_Score <dbl[,1]>, Writing_Sample <dbl[,1]>

# Correlation Analysis

``` r
scaled_construction_props_30plus %>% 
  select(prop_will_construction, LCT_Score, MTELP_I, MTELP_II, MTELP_III, MTELP_Conv_Score, Writing_Sample) %>% 
  pivot_longer(-prop_will_construction) %>% 
  ggplot(aes(prop_will_construction, value))+
  geom_point()+
  facet_wrap(~name)+
  labs(title = "Proficiency Score Distributions (more than 30 uses of future tense)", x = "Proportion of 'will' construction", y = "Proficinecy Test Score")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-corrAn-1.png)<!-- -->

# Random Intercepts Analysis

## Token Level Analysis

``` r
if (file.exists(paste0(dirname(getwd()),'/large_data/tokenized_df_unnested.csv'))){
  print('Reading in File from directory')
  tokenized_df_unnested = read.csv(paste0(dirname(getwd()),'/large_data/tokenized_df_unnested.csv'))
}else{
  
  print('Creating tokenized_df_unnested - about 10 minute runtime.')
  # !!! WARNING - this will take about 10 - 15 !!!
  
  #Create tokenized_df, each row is a token
  tokenized_df_unnested = PELIC_compiled %>%
   #create tokenized df from the tok_lem_POS string
   mutate(tokenized_nested_df = map(tok_lem_POS, tlp_text_to_df)) %>%
   #keep the unique identifier of answer id
   select(answer_id, tokenized_nested_df) %>%
   #make the df longer and unnest
   unnest(tokenized_nested_df)

  # un-comment if you want to save
  #tokenized_df_unnested %>% write.csv('large_data/tokenized_df_unnested.csv')
}
```

    ## [1] "Reading in File from directory"

``` r
#Join in information for each token
all_data = tokenized_df_unnested %>% 
  left_join(PELIC_compiled, by = 'answer_id') %>% 
  left_join(student_info, by = 'anon_id') %>% 
  left_join(scaled_construction_props, by = 'anon_id')
```

There were some problems when knitting, so I saved
`future_tokens_data.csv` in my `large_data` folder.

``` r
if (file.exists(paste0(dirname(getwd()),'/large_data/future_tokens_data.csv'))){
  print('Reading in File from directory')
  future_tokens_data = read.csv(paste0(dirname(getwd()),'/large_data/future_tokens_data.csv'))
}else{
  
  print('Creating future_tokens_data')
  # !!! WARNING - this can have long runtime!!!
  
  future_tokens_data = all_data %>% 
  mutate(is_will_construction = if_else(trimws(lemma) == 'will' & trimws(POS) == 'MD', 1,0)) %>% 
  #if there is a 'going' token followed by 'to' then a verb within 2 words, put 1 in new column
  mutate(is_goingTo_construction = 
            if_else(
              token == "going" & lead(token) == "to" & (
                #lead allows to 'look ahead'
                startsWith(trimws(lead(POS, 2)), 'V') | 
                startsWith(trimws(lead(POS, 3)), 'V')
              ),1,0)) %>% 
  filter(is_goingTo_construction == 1 | is_will_construction == 1) %>% 
  mutate(mean_prof_score = rowMeans(select(.,LCT_Score, MTELP_Conv_Score, Writing_Sample), na.rm = T)) 

  future_tokens_data %>% write.csv(paste0(dirname(getwd()),'/large_data/future_tokens_data.csv'))

}
```

    ## [1] "Reading in File from directory"

### Predict ‘will’ with Mean of Prof Scores using Random Intercepts and Random Slopes

``` r
mean_prof_score.random_slope_and_intercepts_model <- lmer(is_will_construction ~ mean_prof_score + (1 + mean_prof_score | anon_id), data = future_tokens_data)

summary(mean_prof_score.random_slope_and_intercepts_model)
```

    ## Linear mixed model fit by REML ['lmerMod']
    ## Formula: is_will_construction ~ mean_prof_score + (1 + mean_prof_score |  
    ##     anon_id)
    ##    Data: future_tokens_data
    ## 
    ## REML criterion at convergence: 5395.1
    ## 
    ## Scaled residuals: 
    ##     Min      1Q  Median      3Q     Max 
    ## -3.6373  0.0830  0.1708  0.3532  2.5719 
    ## 
    ## Random effects:
    ##  Groups   Name            Variance Std.Dev. Corr
    ##  anon_id  (Intercept)     0.01294  0.11378      
    ##           mean_prof_score 0.00244  0.04939  0.02
    ##  Residual                 0.07323  0.27061      
    ## Number of obs: 18490, groups:  anon_id, 987
    ## 
    ## Fixed effects:
    ##                 Estimate Std. Error t value
    ## (Intercept)     0.898292   0.004789 187.588
    ## mean_prof_score 0.017255   0.006011   2.871
    ## 
    ## Correlation of Fixed Effects:
    ##             (Intr)
    ## men_prf_scr 0.023

``` r
mean_prof_score.random_slope_and_intercepts_model.coefs = coef(mean_prof_score.random_slope_and_intercepts_model)$anon_id %>% 
  rename(Intercept = `(Intercept)`) %>% 
  rownames_to_column("anon_id")
```

``` r
mean_prof_score.random_slope_and_intercepts_model.coefs %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id, native_language) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>%
  distinct() %>%
  ggplot()+
  geom_point(aes(Intercept, mean_prof_score, color = level_id))+
  facet_wrap(~level_id)+
  labs(title = 'Intercept and Coefficient of Mean Proficency Score by Level')
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntAndSlopesByLevel-1.png)<!-- -->

``` r
mean_prof_score.random_slope_and_intercepts_model.coefs %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>% 
  distinct() %>% 
  ggplot(aes(level_id, Intercept))+
  geom_boxplot()+
  labs(x = 'Level', title = 'Intercepts by Level ID')
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntRandSlopeLevelBoxplotOfInts-1.png)<!-- -->

``` r
mean_prof_score.random_slope_and_intercepts_model.coefs %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>% 
  distinct() %>% 
  ggplot(aes(level_id, mean_prof_score))+
  geom_boxplot()+
  labs(x = 'Level', title = 'Slopes by Level ID', y = 'Slope')
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntRandSlopeLevelBoxplotOfSlopes-1.png)<!-- -->
\#### ANOVAs for Random Intercepts and Random Slopes by Level

``` r
#ANOVA for Intercepts
print('ANOVA for Intercepts by Level')
```

    ## [1] "ANOVA for Intercepts by Level"

``` r
summary(aov(Intercept ~ level_id, data = mean_prof_score.random_slope_and_intercepts_model.coefs %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id, native_language) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>%
  distinct()))
```

    ##               Df Sum Sq  Mean Sq F value  Pr(>F)   
    ## level_id       3  0.088 0.029492    3.93 0.00829 **
    ## Residuals   1552 11.645 0.007503                   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

``` r
print('ANOVA for Slopes by Level')
```

    ## [1] "ANOVA for Slopes by Level"

``` r
summary(aov(mean_prof_score ~ level_id, data = mean_prof_score.random_slope_and_intercepts_model.coefs %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id, native_language) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>%
  distinct()))
```

    ##               Df  Sum Sq  Mean Sq F value   Pr(>F)    
    ## level_id       3 0.00751 0.002502   14.21 3.85e-09 ***
    ## Residuals   1552 0.27317 0.000176                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

### Predict ‘will’ with Level using Random Intercepts

``` r
level_id.intercepts_and_slope_model <- lmer(is_will_construction ~ level_id + (1 + level_id | anon_id), data = future_tokens_data %>% mutate(level_id = level_id-2))

summary(level_id.intercepts_and_slope_model)
```

    ## Linear mixed model fit by REML ['lmerMod']
    ## Formula: is_will_construction ~ level_id + (1 + level_id | anon_id)
    ##    Data: future_tokens_data %>% mutate(level_id = level_id - 2)
    ## 
    ## REML criterion at convergence: 5775.2
    ## 
    ## Scaled residuals: 
    ##     Min      1Q  Median      3Q     Max 
    ## -3.7646  0.0712  0.1659  0.3520  2.8041 
    ## 
    ## Random effects:
    ##  Groups   Name        Variance Std.Dev. Corr 
    ##  anon_id  (Intercept) 0.051353 0.22661       
    ##           level_id    0.008689 0.09322  -0.88
    ##  Residual             0.071701 0.26777       
    ## Number of obs: 20307, groups:  anon_id, 1071
    ## 
    ## Fixed effects:
    ##             Estimate Std. Error t value
    ## (Intercept) 0.869771   0.012263  70.927
    ## level_id    0.015359   0.005503   2.791
    ## 
    ## Correlation of Fixed Effects:
    ##          (Intr)
    ## level_id -0.929

``` r
level_id.model_coefs = coef(level_id.intercepts_and_slope_model)$anon_id %>% 
  rename(Intercept = `(Intercept)`) %>% 
  rownames_to_column("anon_id")
```

``` r
level_id.model_coefs
```

    ##      anon_id  Intercept      level_id
    ## 1        aa0 0.85760350  3.747935e-02
    ## 2        aa1 0.97257609  7.288421e-03
    ## 3        aa2 0.83142770  4.364412e-02
    ## 4        aa3 0.79785821  2.100437e-02
    ## 5        aa5 0.89954446  1.302165e-02
    ## 6        aa8 0.47470046 -4.976045e-02
    ## 7        aa9 0.83926693  9.471293e-03
    ## 8        ab1 0.97073333 -1.480878e-02
    ## 9        ab2 0.97401246 -9.936856e-04
    ## 10       ab6 0.91732387  1.162591e-02
    ## 11       ab8 0.97534837  2.624566e-03
    ## 12       ac2 0.56285788  1.070656e-01
    ## 13       ac3 0.95973850  8.296216e-03
    ## 14       ac5 0.46509018  9.590047e-02
    ## 15       ac6 0.86589139  2.241257e-02
    ## 16       ac7 0.88737896  2.519260e-02
    ## 17       ac8 0.76271194  5.312383e-02
    ## 18       ac9 0.89954446  1.302165e-02
    ## 19       ad1 1.11798943 -1.431254e-01
    ## 20       ad2 0.90942970  1.224563e-02
    ## 21       ad4 0.85655817  3.937968e-02
    ## 22       ad5 0.72473408  5.126443e-02
    ## 23       ad7 0.95288516  1.043105e-02
    ## 24       ad8 0.82190407  8.560843e-03
    ## 25       ad9 0.94936936  4.449317e-03
    ## 26       ae0 0.60103948  1.080179e-01
    ## 27       ae2 0.85521109  4.182857e-02
    ## 28       ae3 0.71638321  1.126299e-01
    ## 29       ae4 0.81645383  1.954455e-02
    ## 30       ae9 0.91665314  2.631721e-02
    ## 31       af0 0.73414084  8.513095e-02
    ## 32       af1 0.97954250 -5.076116e-02
    ## 33       af2 0.83193229  1.832944e-02
    ## 34       af3 0.72010679  8.420438e-02
    ## 35       af4 0.86589139  2.241257e-02
    ## 36       af7 0.92377350  1.111959e-02
    ## 37       ag0 0.86152016  3.035914e-02
    ## 38       ag3 0.89954446  1.302165e-02
    ## 39       ag4 0.85703680  3.850956e-02
    ## 40       ag5 0.85907441  1.347810e-02
    ## 41       ag6 0.93232929 -4.023793e-03
    ## 42       ag7 0.98138727 -7.288487e-03
    ## 43       ag8 0.51089697  4.353180e-02
    ## 44       ag9 0.75461671  3.663446e-03
    ## 45       ah0 0.85828503  3.624036e-02
    ## 46       ah1 1.36768469 -2.495540e-01
    ## 47       ah4 0.96267395 -4.246641e-02
    ## 48       ah5 0.85234309  4.704237e-02
    ## 49       ah6 0.52802222  1.174746e-01
    ## 50       ah8 0.85438038  4.333873e-02
    ## 51       ah9 0.64527716  9.205021e-02
    ## 52       ai0 0.81111746  3.288492e-02
    ## 53       ai1 0.82278445  5.271605e-02
    ## 54       ai3 0.71234712  7.208489e-02
    ## 55       aj1 0.86589139  2.241257e-02
    ## 56       aj3 0.95016793 -8.663774e-03
    ## 57       aj5 0.90460406  1.671806e-02
    ## 58       aj7 0.90548253  4.688353e-03
    ## 59       aj8 0.90158581 -4.247729e-02
    ## 60       aj9 0.86277977  2.806926e-02
    ## 61       ak0 0.97185612 -2.741059e-02
    ## 62       ak1 0.94274270  4.284048e-03
    ## 63       ak5 1.01152702 -2.699805e-02
    ## 64       ak7 0.88680619  1.402165e-02
    ## 65       al0 0.92333576  2.321210e-02
    ## 66       al1 0.97188226  7.342889e-03
    ## 67       al2 0.88196987  2.019810e-02
    ## 68       al5 0.85259291  2.657865e-02
    ## 69       al9 0.92608682 -8.701834e-02
    ## 70       am0 0.85579403  4.076882e-02
    ## 71       am1 0.95278233 -1.193187e-02
    ## 72       am2 0.89954446  1.302165e-02
    ## 73       am3 0.93385187 -3.788487e-03
    ## 74       am5 0.86152016  3.035914e-02
    ## 75       am6 0.90043198  2.058477e-02
    ## 76       am7 0.91012530  2.365036e-02
    ## 77       am8 0.85614855  4.012433e-02
    ## 78       am9 0.97276250 -1.306324e-02
    ## 79       an1 0.83130778 -1.514791e-01
    ## 80       an2 0.82641858  2.831289e-02
    ## 81       an4 0.95447987 -4.780433e-02
    ## 82       an5 0.78178262 -1.176873e-01
    ## 83       an7 0.91732387  1.162591e-02
    ## 84       an9 0.86457066  1.576721e-02
    ## 85       ao0 0.94387517  9.541540e-03
    ## 86       ao1 0.98593321 -7.132140e-02
    ## 87       ao2 0.72637645  8.164264e-02
    ## 88       ao4 1.09243542 -8.909388e-02
    ## 89       ao5 0.56285788  1.070656e-01
    ## 90       ao6 0.95856516  8.388326e-03
    ## 91       ao8 0.98961530 -4.721519e-03
    ## 92       ap0 0.90960177  3.457513e-03
    ## 93       ap4 1.16800489 -1.280737e-01
    ## 94       ap5 0.90326921  1.656259e-02
    ## 95       ap7 0.87202038  3.290458e-02
    ## 96       ap8 0.99149047 -2.101107e-02
    ## 97       aq0 0.91732387  1.162591e-02
    ## 98       aq1 0.90533389  9.916729e-03
    ## 99       aq2 0.79535366  3.759518e-02
    ## 100      aq4 0.92542280 -3.529555e-02
    ## 101      aq7 0.92377350  1.111959e-02
    ## 102      aq9 0.91732387  1.162591e-02
    ## 103      ar0 1.00098393 -2.034746e-02
    ## 104      ar2 1.01653223 -6.254877e-02
    ## 105      ar5 0.89954446  1.302165e-02
    ## 106      ar8 0.89007298  1.652213e-02
    ## 107      ar9 0.92978329  2.133021e-02
    ## 108      as0 0.83487200  4.192648e-02
    ## 109      as1 0.86852069  1.763269e-02
    ## 110      as2 0.90942970  1.224563e-02
    ## 111      as4 0.92128469 -1.582083e-02
    ## 112      as5 0.98316444 -1.852323e-02
    ## 113      as7 0.52660501  6.914196e-02
    ## 114      as8 0.99745667 -2.279379e-02
    ## 115      at1 0.85240604  1.672218e-02
    ## 116      at2 0.94387517  9.541540e-03
    ## 117      at3 0.94647067  9.337785e-03
    ## 118      at4 0.83607391 -2.481811e-02
    ## 119      at7 0.91732387  1.162591e-02
    ## 120      at9 0.60035017  3.650942e-02
    ## 121      au2 0.88680619  1.402165e-02
    ## 122      au3 0.90960177  3.457513e-03
    ## 123      au5 0.94962865  1.372724e-02
    ## 124      au6 0.97969768 -4.236816e-03
    ## 125      au7 0.91732387  1.162591e-02
    ## 126      au8 0.89954446  1.302165e-02
    ## 127      au9 0.90548253  4.688353e-03
    ## 128      av4 0.94877664  9.156758e-03
    ## 129      av8 0.94877664  9.156758e-03
    ## 130      aw2 0.85286004  4.610261e-02
    ## 131      aw4 0.98559527 -6.227855e-03
    ## 132      aw5 0.90960177  3.457513e-03
    ## 133      aw6 0.90347629  2.337287e-02
    ## 134      aw9 1.09206368 -1.165119e-01
    ## 135      ax0 0.50814431  1.234142e-01
    ## 136      ax2 0.89433684  1.849482e-02
    ## 137      ax3 0.97560964 -3.503412e-03
    ## 138      ax4 0.85760350  3.747935e-02
    ## 139      ax5 0.91732387  1.162591e-02
    ## 140      ax6 0.93385187 -3.788487e-03
    ## 141      ax7 0.90960177  3.457513e-03
    ## 142      ax9 0.89324140 -7.909762e-03
    ## 143      ay0 0.89073762  2.227756e-02
    ## 144      ay1 0.53754262  1.352086e-01
    ## 145      ay2 0.86152016  3.035914e-02
    ## 146      ay3 0.79345459  7.124069e-02
    ## 147      ay4 0.47314934  1.575423e-01
    ## 148      ay7 1.02729735 -1.006443e-01
    ## 149      ay8 0.91732387  1.162591e-02
    ## 150      az0 0.89954446  1.302165e-02
    ## 151      az2 0.91722953 -7.091643e-02
    ## 152      az3 0.91746530  1.952369e-02
    ## 153      az4 0.85291532  4.600210e-02
    ## 154      az5 0.61541826  1.286565e-01
    ## 155      az6 0.79651649  6.689419e-02
    ## 156      az8 1.01992144 -4.766691e-02
    ## 157      az9 0.93229334 -3.322793e-03
    ## 158      ba0 0.99940435 -1.294726e-02
    ## 159      ba1 0.94545656  1.562032e-02
    ## 160      ba3 0.93739019 -3.241660e-03
    ## 161      ba9 0.73570025  7.373323e-02
    ## 162      bb0 0.81523828  1.963997e-02
    ## 163      bb4 0.94647067  9.337785e-03
    ## 164      bb5 0.87757120  1.302834e-02
    ## 165      bb6 0.86932373  1.539408e-02
    ## 166      bb9 0.97534837  2.624566e-03
    ## 167      bc0 0.40808478  2.026561e-01
    ## 168      bc1 0.98770207 -1.987908e-02
    ## 169      bc4 0.54138456  1.134819e-01
    ## 170      bd0 0.97900714 -1.503093e-02
    ## 171      bd3 0.70685993  1.330674e-01
    ## 172      bd4 0.97972637 -5.025111e-03
    ## 173      bd7 0.89768117  2.313424e-02
    ## 174      be1 0.85828503  3.624036e-02
    ## 175      be2 0.88594331  1.052673e-02
    ## 176      be4 0.89954446  1.302165e-02
    ## 177      be7 0.88186128 -3.916182e-03
    ## 178      be8 0.95016793 -8.663774e-03
    ## 179      bf0 1.01928071 -7.124732e-02
    ## 180      bf1 0.93385187 -3.788487e-03
    ## 181      bf2 0.94735729  1.119240e-02
    ## 182      bf5 0.84101644  1.761630e-02
    ## 183      bf7 0.73915279  7.356109e-02
    ## 184      bf8 0.95856516  8.388326e-03
    ## 185      bf9 0.96663735  1.021719e-02
    ## 186      bg0 0.90960177  3.457513e-03
    ## 187      bg1 0.93385187 -3.788487e-03
    ## 188      bg2 0.94527795 -2.022650e-03
    ## 189      bg3 0.87854953 -5.990211e-04
    ## 190      bg7 0.82357493  1.898552e-02
    ## 191      bh0 0.86152016  3.035914e-02
    ## 192      bh1 0.71687013  6.104629e-02
    ## 193      bh2 0.95943399  9.529091e-03
    ## 194      bh3 0.93752326  1.517386e-02
    ## 195      bh4 0.89954446  1.302165e-02
    ## 196      bh7 0.86940705  1.538754e-02
    ## 197      bh8 0.99745667 -2.279379e-02
    ## 198      bh9 0.91959091  1.573643e-02
    ## 199      bi0 0.83546579  4.903982e-02
    ## 200      bi1 0.91732387  1.162591e-02
    ## 201      bi3 0.63450741  1.536981e-01
    ## 202      bi4 0.91421647  2.444219e-02
    ## 203      bi5 0.93716441  7.817481e-03
    ## 204      bi6 0.98860719 -6.338233e-02
    ## 205      bi8 0.97763094 -1.686980e-02
    ## 206      bi9 0.85443799  1.656266e-02
    ## 207      bj0 0.88680619  1.402165e-02
    ## 208      bj1 0.95016793 -8.663774e-03
    ## 209      bj2 0.96589397  7.812990e-03
    ## 210      bj4 0.92914186  1.069816e-02
    ## 211      bj6 0.96581650 -3.265629e-02
    ## 212      bj8 0.85521109  4.182857e-02
    ## 213      bj9 0.90960177  3.457513e-03
    ## 214      bk0 0.91732387  1.162591e-02
    ## 215      bk2 0.94093198  9.772591e-03
    ## 216      bk5 0.92542280 -3.529555e-02
    ## 217      bk7 0.95973850  8.296216e-03
    ## 218      bk8 0.48459658  1.304503e-01
    ## 219      bl0 0.90942970  1.224563e-02
    ## 220      bl2 1.01046212 -2.029178e-02
    ## 221      bl3 0.63083007  3.411665e-02
    ## 222      bl4 0.55429415  1.536816e-01
    ## 223      bl6 0.82113540  2.989153e-02
    ## 224      bl7 0.92345771  2.787298e-03
    ## 225      bl8 0.86152016  3.035914e-02
    ## 226      bl9 0.81782560  3.500917e-02
    ## 227      bm0 0.92914186  1.069816e-02
    ## 228      bm1 0.85655817  3.937968e-02
    ## 229      bm3 0.86152016  3.035914e-02
    ## 230      bm4 0.90471393 -4.816399e-02
    ## 231      bm5 0.87670814  3.702243e-02
    ## 232      bm6 0.63673234  8.546751e-02
    ## 233      bm7 0.75791471  7.267945e-02
    ## 234      bm8 0.86518520  4.047159e-02
    ## 235      bm9 0.86448685  2.496591e-02
    ## 236      bn5 0.82954933  4.833956e-02
    ## 237      bn6 0.86852069  1.763269e-02
    ## 238      bn7 0.88006898  2.549213e-02
    ## 239      bo0 0.89970661  6.414216e-03
    ## 240      bo4 0.73952364  2.558383e-02
    ## 241      bo5 0.60035017  3.650942e-02
    ## 242      bo8 0.90942970  1.224563e-02
    ## 243      bo9 0.90960177  3.457513e-03
    ## 244      bp2 0.75416596  1.317282e-02
    ## 245      bp4 0.96593447  7.422678e-03
    ## 246      bp5 0.96362066  7.991452e-03
    ## 247      bp8 0.89673226 -3.365389e-02
    ## 248      bp9 0.71562098  2.746027e-02
    ## 249      bq0 0.41868674  1.086809e-01
    ## 250      bq3 0.91507503  1.802786e-02
    ## 251      bq4 0.90942970  1.224563e-02
    ## 252      bq8 0.85438038  4.333873e-02
    ## 253      br1 0.93385187 -3.788487e-03
    ## 254      br2 0.81782560  3.500917e-02
    ## 255      br3 0.90942970  1.224563e-02
    ## 256      br4 0.85212500  4.307903e-02
    ## 257      br5 0.76356688  4.692459e-02
    ## 258      br6 0.86940705  1.538754e-02
    ## 259      br7 0.85941286  3.419006e-02
    ## 260      br8 0.24913692  1.739949e-01
    ## 261      br9 1.00232234 -7.384788e-02
    ## 262      bs0 0.86940705  1.538754e-02
    ## 263      bs1 0.78647077  7.457623e-02
    ## 264      bs2 0.99470107 -2.197041e-02
    ## 265      bs3 0.90942970  1.224563e-02
    ## 266      bs5 0.75703148  9.139014e-02
    ## 267      bs6 0.95016793 -8.663774e-03
    ## 268      bs8 0.73541711  6.701232e-02
    ## 269      bs9 1.00470223 -2.312747e-02
    ## 270      bt1 0.55892688  3.976129e-02
    ## 271      bt2 0.58496118  1.281042e-02
    ## 272      bt7 0.95433902  4.573261e-03
    ## 273      bt8 0.80679343  3.417695e-02
    ## 274      bu0 0.77658546  2.267435e-02
    ## 275      bu1 1.14482079 -1.316584e-01
    ## 276      bu3 0.85760350  3.747935e-02
    ## 277      bu4 1.20118111 -1.332015e-01
    ## 278      bu6 0.92377350  1.111959e-02
    ## 279      bu8 0.34604730  1.718492e-01
    ## 280      bu9 0.61566107  1.330066e-01
    ## 281      bv0 0.89486913 -3.026685e-02
    ## 282      bv1 1.02625864 -4.270501e-02
    ## 283      bv2 0.87716952  2.085925e-02
    ## 284      bv4 0.89158998  1.364611e-02
    ## 285      bv5 0.88852547 -1.873455e-02
    ## 286      bv8 0.93367981  1.034191e-02
    ## 287      bv9 1.02837235 -6.780509e-02
    ## 288      bw1 0.69430216  6.778967e-02
    ## 289      bw2 0.92251702  2.375704e-02
    ## 290      bw3 0.98625388 -5.960230e-02
    ## 291      bw4 0.95983968  3.846796e-03
    ## 292      bw5 0.97941517 -2.613932e-03
    ## 293      bw6 0.88810134  2.504827e-02
    ## 294      bw9 0.90402328  2.680509e-02
    ## 295      bx3 0.95083898  8.994858e-03
    ## 296      bx5 0.74054927  3.764736e-02
    ## 297      bx6 0.89954446  1.302165e-02
    ## 298      bx9 0.91732387  1.162591e-02
    ## 299      by1 0.88869863 -1.904933e-02
    ## 300      by3 0.98122190 -8.336384e-02
    ## 301      by5 0.89954446  1.302165e-02
    ## 302      by6 1.03635871 -8.953629e-02
    ## 303      by8 0.94020988 -2.805891e-03
    ## 304      by9 0.88103440 -5.116319e-03
    ## 305      bz1 0.92389166  1.789743e-02
    ## 306      bz2 0.85268863  3.624948e-02
    ## 307      bz3 0.56285788  1.070656e-01
    ## 308      bz5 0.93669273  1.266134e-02
    ## 309      bz9 0.97461636 -3.719103e-02
    ## 310      ca0 0.92914186  1.069816e-02
    ## 311      ca4 0.97067581  9.654689e-03
    ## 312      ca5 0.71574530  5.233069e-03
    ## 313      ca6 0.96990481 -1.552150e-02
    ## 314      ca7 0.96189627 -1.216824e-02
    ## 315      cb0 0.95973850  8.296216e-03
    ## 316      cb1 0.86589139  2.241257e-02
    ## 317      cb2 0.98865030 -1.629024e-02
    ## 318      cb3 0.63878730  1.003251e-01
    ## 319      cb5 0.90889253  1.228780e-02
    ## 320      cb7 0.71687013  6.104629e-02
    ## 321      cb8 0.68041098  1.250782e-01
    ## 322      cb9 0.75739756  9.692083e-02
    ## 323      cc1 0.98770207 -1.987908e-02
    ## 324      cc4 0.90686590  2.578725e-02
    ## 325      cc6 0.81529041  2.938176e-02
    ## 326      cc7 0.89954446  1.302165e-02
    ## 327      cd2 0.95973850  8.296216e-03
    ## 328      cd3 0.81645383  1.954455e-02
    ## 329      cd4 0.90960177  3.457513e-03
    ## 330      cd6 0.87793744  3.573045e-02
    ## 331      cd8 0.71687013  6.104629e-02
    ## 332      ce1 0.71562098  2.746027e-02
    ## 333      ce2 0.97171587 -7.344913e-02
    ## 334      ce3 1.02285332 -3.645843e-02
    ## 335      ce6 0.96189627 -1.216824e-02
    ## 336      ce7 0.89646325  6.856842e-03
    ## 337      ce8 0.90960177  3.457513e-03
    ## 338      cf0 0.95856516  8.388326e-03
    ## 339      cf1 0.98316444 -1.852323e-02
    ## 340      cf2 0.85614855  4.012433e-02
    ## 341      cf3 0.93385187 -3.788487e-03
    ## 342      cf6 0.93852547  4.178870e-03
    ## 343      cf7 0.79785821  2.100437e-02
    ## 344      cf9 0.92215409  1.131536e-02
    ## 345      cg1 1.00690946 -2.561831e-02
    ## 346      cg2 0.79493893  2.123354e-02
    ## 347      cg3 0.86021637  3.272934e-02
    ## 348      cg6 0.85760350  3.747935e-02
    ## 349      cg9 0.86852069  1.763269e-02
    ## 350      ch0 0.95983148  4.710243e-03
    ## 351      ch1 0.55370822  1.214758e-01
    ## 352      ch2 0.85455670  4.301819e-02
    ## 353      ch4 0.95269436  8.849205e-03
    ## 354      ch5 0.79494814  3.771636e-02
    ## 355      ch9 0.65492507  7.955566e-02
    ## 356      ci0 0.94065210  1.787611e-02
    ## 357      ci1 0.88852547 -1.873455e-02
    ## 358      ci2 0.94869997  1.428207e-02
    ## 359      ci3 1.01293801 -3.677027e-02
    ## 360      ci4 0.87375406  1.416891e-02
    ## 361      ci5 0.94626463  7.951176e-03
    ## 362      ci6 0.54137328  1.436782e-01
    ## 363      ci9 0.87640123  1.483847e-02
    ## 364      cj2 0.99252947 -8.761669e-03
    ## 365      cj3 0.88817270  1.391437e-02
    ## 366      cj5 1.06411865 -4.933833e-02
    ## 367      cj7 0.98770207 -1.987908e-02
    ## 368      cj8 0.86764053  1.294514e-02
    ## 369      ck0 0.89420424 -2.905814e-02
    ## 370      ck1 0.98480011 -1.502370e-02
    ## 371      ck2 0.88888993  3.128201e-02
    ## 372      ck3 0.85614855  4.012433e-02
    ## 373      ck4 1.09067983 -5.745317e-02
    ## 374      ck6 0.85614855  4.012433e-02
    ## 375      ck7 0.89954446  1.302165e-02
    ## 376      ck9 0.87585640  4.296897e-03
    ## 377      cl1 0.95754693  5.827637e-03
    ## 378      cl2 0.94274270  4.284048e-03
    ## 379      cl3 0.92073969  1.703872e-02
    ## 380      cl4 0.85240604  1.672218e-02
    ## 381      cl5 0.90153988  2.877739e-02
    ## 382      cl6 0.80339715 -5.587563e-02
    ## 383      cl7 0.95437243  8.717470e-03
    ## 384      cl9 0.97348419 -4.369418e-03
    ## 385      cm2 0.69049393  6.892758e-02
    ## 386      cm4 0.90942970  1.224563e-02
    ## 387      cm7 0.78070387  5.961354e-02
    ## 388      cm8 0.85372011  3.067115e-02
    ## 389      cm9 0.14577816  2.416484e-01
    ## 390      cn0 1.11453185 -1.166224e-01
    ## 391      cn1 0.85422019  4.362995e-02
    ## 392      cn3 1.04214749 -7.542846e-02
    ## 393      cn4 1.03377184 -2.986477e-02
    ## 394      cn7 0.71687013  6.104629e-02
    ## 395      cn9 0.87640123  1.483847e-02
    ## 396      co0 0.93306212  1.512655e-02
    ## 397      co1 0.28648032  2.101777e-01
    ## 398      co2 0.89266290 -2.625608e-02
    ## 399      co4 0.86691497  4.026734e-02
    ## 400      co5 0.95842797 -1.140216e-02
    ## 401      co8 0.98234447  6.521569e-03
    ## 402      cp0 0.93983909  1.222787e-02
    ## 403      cp2 0.88590570  1.430539e-02
    ## 404      cp3 0.92411850 -4.224374e-03
    ## 405      cp4 0.61040597  1.005973e-01
    ## 406      cp5 1.22896626 -1.351828e-01
    ## 407      cp6 0.88788483  3.042610e-02
    ## 408      cp7 0.93346452  4.052650e-03
    ## 409      cp8 0.76724587  2.340754e-02
    ## 410      cq0 0.86589139  2.241257e-02
    ## 411      cq1 0.88680619  1.402165e-02
    ## 412      cq2 0.93385187 -3.788487e-03
    ## 413      cq4 0.99322037 -5.350135e-03
    ## 414      cr1 0.87376995  2.903938e-02
    ## 415      cr3 0.92914186  1.069816e-02
    ## 416      cr4 0.79494814  3.771636e-02
    ## 417      cr8 0.82641858  2.831289e-02
    ## 418      cr9 0.90942970  1.224563e-02
    ## 419      cs0 0.83942187  4.541450e-02
    ## 420      cs1 0.87742315  4.738547e-02
    ## 421      cs3 0.93756621  1.003682e-02
    ## 422      cs4 0.87202038  3.290458e-02
    ## 423      cs5 0.92483489  2.153613e-02
    ## 424      cs6 0.91954538  3.705507e-03
    ## 425      cs7 1.07383386 -5.227284e-02
    ## 426      cs8 0.93367981  1.034191e-02
    ## 427      cs9 0.82970459  1.850432e-02
    ## 428      ct0 0.96182374  8.132517e-03
    ## 429      ct1 0.85614855  4.012433e-02
    ## 430      ct4 0.65919073  1.204745e-01
    ## 431      ct5 0.56285788  1.070656e-01
    ## 432      ct9 0.89954446  1.302165e-02
    ## 433      cu0 0.96522927 -3.818980e-03
    ## 434      cu1 0.87877593  3.484920e-02
    ## 435      cu2 0.85286004  4.610261e-02
    ## 436      cu4 0.92045463  2.146456e-04
    ## 437      cu6 0.98171198 -1.141671e-02
    ## 438      cu8 0.96518520  7.868631e-03
    ## 439      cu9 0.94603608 -7.429165e-03
    ## 440      cv1 0.89486913 -3.026685e-02
    ## 441      cv2 0.90323136  2.342629e-02
    ## 442      cv3 0.93998358  3.442381e-03
    ## 443      cv5 0.85942034  1.617153e-02
    ## 444      cv7 0.87903399 -4.785739e-02
    ## 445      cv8 0.92377350  1.111959e-02
    ## 446      cw0 0.99253296 -1.595246e-03
    ## 447      cw1 0.88810134  2.504827e-02
    ## 448      cw2 0.82721799  2.807403e-02
    ## 449      cw4 0.88680619  1.402165e-02
    ## 450      cw6 0.96317578 -4.479527e-03
    ## 451      cw7 0.96189627 -1.216824e-02
    ## 452      cw8 0.93385187 -3.788487e-03
    ## 453      cw9 1.02176234 -3.005640e-02
    ## 454      cx0 0.85228656  4.714514e-02
    ## 455      cx4 0.91531673  1.783591e-02
    ## 456      cx6 0.86152016  3.035914e-02
    ## 457      cx8 0.99149047 -2.101107e-02
    ## 458      cx9 0.86152016  3.035914e-02
    ## 459      cy2 0.98131732 -1.177844e-03
    ## 460      cy3 1.00060075 -1.883271e-02
    ## 461      cy5 0.97444381 -1.161693e-02
    ## 462      cy6 0.70256379  2.848531e-02
    ## 463      cy7 0.95016793 -8.663774e-03
    ## 464      cz2 1.00937893 -4.028011e-02
    ## 465      cz3 0.84214369  2.361419e-02
    ## 466      cz4 0.85718412  4.636225e-02
    ## 467      cz6 0.95790283 -6.175681e-03
    ## 468      cz7 0.88817270  1.391437e-02
    ## 469      cz8 0.94877664  9.156758e-03
    ## 470      cz9 0.97151110  7.372026e-03
    ## 471      da0 0.97097273 -7.218196e-03
    ## 472      da3 0.85912025  3.472200e-02
    ## 473      da6 0.77590348 -5.974903e-03
    ## 474      da7 0.95470872 -2.481127e-02
    ## 475      da9 1.04002392 -7.182387e-02
    ## 476      db2 0.90942970  1.224563e-02
    ## 477      db4 0.98451213  3.215375e-03
    ## 478      db5 0.62526354  8.870638e-02
    ## 479      db6 0.95701323  4.639955e-03
    ## 480      db8 0.82761347  4.960304e-02
    ## 481      dc0 0.91031920  1.142123e-02
    ## 482      dc1 1.01541133 -2.191318e-01
    ## 483      dc2 0.95016793 -8.663774e-03
    ## 484      dc6 1.15800790 -1.217639e-01
    ## 485      dd1 0.63442446  3.627897e-02
    ## 486      dd3 1.01336945 -3.984214e-02
    ## 487      dd4 0.90974868 -5.731679e-02
    ## 488      dd5 0.93385187 -3.788487e-03
    ## 489      dd6 0.90960177  3.457513e-03
    ## 490      dd9 1.07914199 -1.080499e-01
    ## 491      de1 0.96129607  4.746770e-03
    ## 492      de2 0.86589139  2.241257e-02
    ## 493      de4 0.95575219 -4.039148e-04
    ## 494      de5 0.91572203  2.695171e-02
    ## 495      de6 0.92377350  1.111959e-02
    ## 496      de7 0.93852547  4.178870e-03
    ## 497      df0 0.91353393  1.914426e-03
    ## 498      df2 0.70844496  6.356376e-02
    ## 499      df3 0.95180306  6.150176e-05
    ## 500      df4 0.77799992  4.921443e-02
    ## 501      df7 0.90043198  2.058477e-02
    ## 502      df9 0.94877664  9.156758e-03
    ## 503      dg0 0.93756621  1.003682e-02
    ## 504      dg3 0.43322408  4.962939e-02
    ## 505      dg5 0.79222381  1.339393e-02
    ## 506      dg6 0.87595296  4.121355e-03
    ## 507      dg9 0.88268479  2.613049e-02
    ## 508      dh1 0.94527795 -2.022650e-03
    ## 509      dh2 0.85703680  3.850956e-02
    ## 510      dh6 0.96442876 -3.451059e-02
    ## 511      dh7 0.96189627 -1.216824e-02
    ## 512      dh8 0.95146162  1.461828e-02
    ## 513      dh9 0.96777678  7.665183e-03
    ## 514      di0 0.91954538  3.705507e-03
    ## 515      di1 0.98251000 -2.526505e-02
    ## 516      di2 0.91144150  2.731220e-02
    ## 517      di3 0.94971320  1.602291e-02
    ## 518      di7 0.72875092  6.851271e-02
    ## 519      di8 0.88103440 -5.116319e-03
    ## 520      dj0 0.85496854  4.226950e-02
    ## 521      dj3 0.43946121  4.913976e-02
    ## 522      dj6 0.54899736  1.397214e-01
    ## 523      dj7 0.85760350  3.747935e-02
    ## 524      dj8 0.93756621  1.003682e-02
    ## 525      dj9 0.91455631  5.626994e-03
    ## 526      dk0 0.73496359  2.757868e-03
    ## 527      dk3 0.93355226  1.480144e-02
    ## 528      dk5 0.90942970  1.224563e-02
    ## 529      dk6 0.93088615  1.284211e-02
    ## 530      dl1 0.87833269  3.531504e-02
    ## 531      dl2 0.86589139  2.241257e-02
    ## 532      dl3 0.85760350  3.747935e-02
    ## 533      dl5 0.85614855  4.012433e-02
    ## 534      dl7 0.86118687  3.096503e-02
    ## 535      dl8 0.09180736  2.478169e-01
    ## 536      dl9 0.59816785  3.668074e-02
    ## 537      dm2 0.88090152 -4.874751e-03
    ## 538      dm3 0.91155824 -1.758916e-02
    ## 539      dm4 1.00270725 -1.336781e-02
    ## 540      dm8 0.90283028  3.055378e-02
    ## 541      dn0 0.94647067  9.337785e-03
    ## 542      dn1 0.83711807  2.511586e-02
    ## 543      dn2 0.63707034  3.537704e-02
    ## 544      dn4 0.91195315  2.028272e-02
    ## 545      dn5 0.79494814  3.771636e-02
    ## 546      dn6 0.75255660  5.038307e-02
    ## 547      dn7 0.95856516  8.388326e-03
    ## 548      dn8 1.00772678 -6.506326e-02
    ## 549      do0 0.92427398  1.437162e-02
    ## 550      do1 0.98018640 -2.489478e-02
    ## 551      do3 0.88208883  2.771340e-03
    ## 552      do5 0.96589397  7.812990e-03
    ## 553      do6 0.86016772  3.281777e-02
    ## 554      do7 0.99455710 -7.505001e-03
    ## 555      do9 0.94336959 -6.264991e-02
    ## 556      dp1 0.97585966 -1.039898e-02
    ## 557      dp2 0.89954446  1.302165e-02
    ## 558      dp3 0.86333341  2.706278e-02
    ## 559      dp4 0.86333341  2.706278e-02
    ## 560      dp5 1.02172193 -1.163786e-01
    ## 561      dp7 0.64361787  1.268507e-01
    ## 562      dp9 0.91875674  2.321602e-02
    ## 563      dq0 0.85912025  3.472200e-02
    ## 564      dq4 0.93385187 -3.788487e-03
    ## 565      dq7 0.88680619  1.402165e-02
    ## 566      dq9 0.73324789  6.206955e-02
    ## 567      dr0 0.99925945 -1.353039e-02
    ## 568      dr1 0.94387517  9.541540e-03
    ## 569      dr2 0.99455350 -7.142705e-03
    ## 570      dr3 0.92030925  2.382578e-02
    ## 571      dr5 1.00580270 -1.292293e-01
    ## 572      dr8 0.77412864  2.286722e-02
    ## 573      ds0 0.85760350  3.747935e-02
    ## 574      ds2 0.95016793 -8.663774e-03
    ## 575      ds3 0.89954446  1.302165e-02
    ## 576      ds6 0.95447987 -4.780433e-02
    ## 577      ds9 0.95443963 -7.289693e-03
    ## 578      dt1 0.89954446  1.302165e-02
    ## 579      dt3 0.84866993  3.292660e-02
    ## 580      dt4 0.87025016  4.567627e-02
    ## 581      dt5 0.99149047 -2.101107e-02
    ## 582      dt6 0.90230269  1.280512e-02
    ## 583      dt8 0.95589747  8.597749e-03
    ## 584      du2 0.90960177  3.457513e-03
    ## 585      du3 0.93367981  1.034191e-02
    ## 586      du4 0.85271082  4.637387e-02
    ## 587      du5 0.86152016  3.035914e-02
    ## 588      du6 0.95023420 -4.025282e-02
    ## 589      du7 0.89954446  1.302165e-02
    ## 590      du8 0.91732387  1.162591e-02
    ## 591      du9 0.66275510  9.497519e-02
    ## 592      dv0 0.96522927 -3.818980e-03
    ## 593      dv1 0.91690885  1.151794e-02
    ## 594      dv4 0.82113540  2.989153e-02
    ## 595      dv7 0.93367981  1.034191e-02
    ## 596      dv8 0.95269436  8.849205e-03
    ## 597      dv9 0.87715683  1.932815e-03
    ## 598      dw0 0.49096966  1.396901e-01
    ## 599      dw2 0.95097103 -2.184770e-03
    ## 600      dw3 1.00179393 -1.127864e-02
    ## 601      dw6 0.94941248 -1.383681e-03
    ## 602      dw7 0.92764549  1.882656e-02
    ## 603      dx1 0.61007823  1.082433e-01
    ## 604      dx3 0.87302438  2.545272e-03
    ## 605      dx4 0.97560964 -3.503412e-03
    ## 606      dx5 0.94194365  1.193801e-02
    ## 607      dx6 0.96487272 -7.878127e-03
    ## 608      dx7 0.60509397  1.259556e-01
    ## 609      dx8 0.88680619  1.402165e-02
    ## 610      dx9 0.91211269  1.604658e-02
    ## 611      dy4 0.77658546  2.267435e-02
    ## 612      dy7 0.67459439  8.832553e-02
    ## 613      dz0 0.88680619  1.402165e-02
    ## 614      dz1 1.03203319 -8.568249e-02
    ## 615      dz5 0.92377350  1.111959e-02
    ## 616      dz7 0.86589139  2.241257e-02
    ## 617      dz8 0.90960177  3.457513e-03
    ## 618      dz9 0.96046602  1.121336e-02
    ## 619      ea0 0.91619311 -6.903229e-02
    ## 620      ea2 0.85828503  3.624036e-02
    ## 621      ea3 0.90180401  1.284427e-02
    ## 622      ea4 0.96111552  9.929973e-03
    ## 623      ea5 0.95973850  8.296216e-03
    ## 624      ea7 0.89420424 -2.905814e-02
    ## 625      ea9 1.00562739 -1.626713e-02
    ## 626      eb3 0.69258618  5.687797e-02
    ## 627      eb4 0.85655817  3.937968e-02
    ## 628      eb5 0.97073333 -1.480878e-02
    ## 629      eb6 0.79008719  8.690430e-02
    ## 630      eb7 0.90158581 -4.247729e-02
    ## 631      eb9 0.64307354  2.111734e-02
    ## 632      ec1 0.84101644  1.761630e-02
    ## 633      ec5 0.98902144 -1.488556e-02
    ## 634      ec7 0.93385187 -3.788487e-03
    ## 635      ec9 0.85240435  4.693102e-02
    ## 636      ed1 0.89954446  1.302165e-02
    ## 637      ed2 0.82970459  1.850432e-02
    ## 638      ed3 0.71311505  8.502978e-02
    ## 639      ed7 0.88680619  1.402165e-02
    ## 640      ed8 0.85733393  3.796940e-02
    ## 641      ee1 0.59612350  3.684123e-02
    ## 642      ee3 0.85579403  4.076882e-02
    ## 643      ee4 0.86333341  2.706278e-02
    ## 644      ee5 0.97657573 -6.021080e-03
    ## 645      ee7 0.91732387  1.162591e-02
    ## 646      ee8 0.85496854  4.226950e-02
    ## 647      ee9 0.90942970  1.224563e-02
    ## 648      ef0 0.89198799  1.881832e-02
    ## 649      ef1 0.90942970  1.224563e-02
    ## 650      ef2 0.95856516  8.388326e-03
    ## 651      ef3 0.53633813  1.059254e-01
    ## 652      ef4 0.47031138  1.047575e-01
    ## 653      ef6 0.90424608  1.511572e-02
    ## 654      ef9 0.94103721  1.518144e-02
    ## 655      eg2 0.97321201  7.238498e-03
    ## 656      eg5 0.99272970 -1.212531e-01
    ## 657      eg8 0.86152016  3.035914e-02
    ## 658      eh0 0.99150055 -1.307036e-02
    ## 659      eh2 0.43946121  4.913976e-02
    ## 660      eh4 0.87787075  2.414394e-02
    ## 661      eh7 0.82970459  1.850432e-02
    ## 662      eh8 0.78562385  2.196481e-02
    ## 663      ei0 0.95121264  1.066140e-02
    ## 664      ei2 0.92924436  2.167562e-02
    ## 665      ei3 0.96483529 -1.056538e-02
    ## 666      ei5 0.92914186  1.069816e-02
    ## 667      ei7 0.88680619  1.402165e-02
    ## 668      ei8 0.86787591 -7.102992e-02
    ## 669      ej1 0.65492507  7.955566e-02
    ## 670      ej2 0.80438319  3.489713e-02
    ## 671      ej5 0.89769444  1.316689e-02
    ## 672      ej8 0.94941248 -1.383681e-03
    ## 673      ej9 0.90942970  1.224563e-02
    ## 674      ek1 0.94078242  1.385784e-02
    ## 675      ek3 0.97073333 -1.480878e-02
    ## 676      ek5 0.94387517  9.541540e-03
    ## 677      ek6 0.64546422  6.662250e-02
    ## 678      ek7 0.90942970  1.224563e-02
    ## 679      el0 0.83464304  1.264748e-02
    ## 680      el2 0.48099894  1.756813e-01
    ## 681      el5 0.97451503 -9.799115e-02
    ## 682      el7 0.63028096  4.765726e-02
    ## 683      el8 0.99984761 -2.350821e-02
    ## 684      el9 0.92377350  1.111959e-02
    ## 685      em0 0.70188636  4.500073e-02
    ## 686      em1 0.93367981  1.034191e-02
    ## 687      em5 0.98770207 -1.987908e-02
    ## 688      em7 0.88215086  3.130218e-02
    ## 689      em8 0.96724769 -9.249726e-03
    ## 690      en1 0.93756621  1.003682e-02
    ## 691      en2 0.96362066  7.991452e-03
    ## 692      en3 0.91732387  1.162591e-02
    ## 693      en4 0.80799239  5.423967e-02
    ## 694      en9 0.82970459  1.850432e-02
    ## 695      eo1 1.11740912 -8.969860e-02
    ## 696      eo2 0.97371501 -5.722635e-03
    ## 697      eo5 0.88557456  1.559455e-02
    ## 698      eo8 0.86333341  2.706278e-02
    ## 699      ep1 0.85937111  1.846658e-02
    ## 700      ep2 0.84193320  1.754433e-02
    ## 701      ep4 0.99075489 -2.999652e-03
    ## 702      ep6 0.88680619  1.402165e-02
    ## 703      eq0 1.00339104 -5.211213e-02
    ## 704      eq1 0.88009811 -3.414224e-03
    ## 705      eq2 0.92914186  1.069816e-02
    ## 706      eq4 0.87032510  3.506962e-02
    ## 707      eq6 0.86589139  2.241257e-02
    ## 708      eq8 1.46477925 -3.869149e-01
    ## 709      er2 0.99170894 -1.903328e-02
    ## 710      er3 0.92874198  1.072955e-02
    ## 711      er4 0.90562969  2.154614e-02
    ## 712      er5 0.89954446  1.302165e-02
    ## 713      er9 0.64600269  3.292555e-02
    ## 714      es0 0.71636622  4.066683e-02
    ## 715      es2 0.97763094 -1.686980e-02
    ## 716      es4 0.96188374  1.147653e-02
    ## 717      es6 0.34144502  5.683436e-02
    ## 718      es7 0.76471577  4.674988e-02
    ## 719      es9 0.91899937 -2.030644e-03
    ## 720      et0 0.86861896  3.724852e-02
    ## 721      et1 0.97074756 -1.479654e-02
    ## 722      et3 0.38800623  1.609238e-01
    ## 723      et4 0.81821651  4.067771e-02
    ## 724      et5 0.94564404  5.462405e-03
    ## 725      et6 0.85579403  4.076882e-02
    ## 726      et8 0.84929092  7.489402e-03
    ## 727      eu0 0.92086943  1.957971e-02
    ## 728      eu1 0.71562098  2.746027e-02
    ## 729      eu2 0.89954446  1.302165e-02
    ## 730      eu5 0.93367981  1.034191e-02
    ## 731      eu7 0.93385187 -3.788487e-03
    ## 732      eu8 0.60035017  3.650942e-02
    ## 733      eu9 0.60035017  3.650942e-02
    ## 734      ev0 0.71562098  2.746027e-02
    ## 735      ev4 1.00194178 -2.413395e-02
    ## 736      ev5 0.86778683 -1.399847e-02
    ## 737      ev6 0.98843573 -5.764268e-02
    ## 738      ev7 0.89451215  1.341671e-02
    ## 739      ev9 0.55312400  1.103522e-01
    ## 740      ew2 0.86152016  3.035914e-02
    ## 741      ew4 0.58730416  1.220516e-01
    ## 742      ew6 0.99149047 -2.101107e-02
    ## 743      ew7 0.99984761 -2.350821e-02
    ## 744      ew8 0.86589139  2.241257e-02
    ## 745      ew9 0.79785821  2.100437e-02
    ## 746      ex0 0.82140846  1.257971e-03
    ## 747      ex3 0.95728949  8.488471e-03
    ## 748      ex6 0.88680619  1.402165e-02
    ## 749      ex7 0.93756621  1.003682e-02
    ## 750      ex9 0.83314693  4.723210e-02
    ## 751      ey0 0.89954446  1.302165e-02
    ## 752      ey2 0.48610587  1.050412e-01
    ## 753      ey3 0.92377350  1.111959e-02
    ## 754      ey4 0.90960177  3.457513e-03
    ## 755      ey8 0.95443963 -7.289693e-03
    ## 756      ey9 0.90942970  1.224563e-02
    ## 757      ez2 0.94387517  9.541540e-03
    ## 758      ez3 0.87372380  1.504866e-02
    ## 759      ez5 0.95870982  1.040853e-02
    ## 760      ez6 0.74900475  3.851118e-02
    ## 761      ez8 0.95016793 -8.663774e-03
    ## 762      fa0 0.79785821  2.100437e-02
    ## 763      fa2 0.57778618  1.275414e-01
    ## 764      fa4 0.96129607  4.746770e-03
    ## 765      fa5 0.89954446  1.302165e-02
    ## 766      fa6 0.88680619  1.402165e-02
    ## 767      fa9 0.76735204  4.547666e-02
    ## 768      fb1 0.99791787 -8.226992e-03
    ## 769      fb3 0.71759603  7.271121e-02
    ## 770      fb4 0.74200624  3.947508e-02
    ## 771      fb5 0.36040024  1.675605e-01
    ## 772      fb8 0.96076724 -5.254286e-03
    ## 773      fb9 0.90960177  3.457513e-03
    ## 774      fc0 0.86333341  2.706278e-02
    ## 775      fc1 0.95016793 -8.663774e-03
    ## 776      fc5 0.85548419  4.133209e-02
    ## 777      fc7 0.88680619  1.402165e-02
    ## 778      fc9 0.56380026  1.158010e-01
    ## 779      fd1 0.94387517  9.541540e-03
    ## 780      fd2 0.89954446  1.302165e-02
    ## 781      fd3 1.07789894 -1.245906e-01
    ## 782      fd6 0.93116790  1.931923e-02
    ## 783      fd7 0.89954446  1.302165e-02
    ## 784      fd8 0.94188911 -6.190038e-03
    ## 785      fe0 0.93756621  1.003682e-02
    ## 786      fe1 0.85703680  3.850956e-02
    ## 787      fe2 0.82402689  4.128624e-02
    ## 788      fe3 0.99008927 -1.084404e-02
    ## 789      fe4 0.82405614  3.379423e-02
    ## 790      fe5 0.85940627  3.420203e-02
    ## 791      fe7 0.60270921  9.515791e-02
    ## 792      fe9 0.86016772  3.281777e-02
    ## 793      ff0 0.86334650  2.703898e-02
    ## 794      ff1 0.94600114  9.644044e-03
    ## 795      ff2 0.81324696  3.458168e-02
    ## 796      ff3 0.95005727  1.210889e-02
    ## 797      ff4 0.97004828  9.793455e-03
    ## 798      ff6 0.96076724 -5.254286e-03
    ## 799      fg0 0.89528684 -3.102621e-02
    ## 800      fg2 0.92914186  1.069816e-02
    ## 801      fg7 0.37599827  1.628998e-01
    ## 802      fg8 0.67633278  1.021338e-01
    ## 803      fg9 0.84265172 -7.434925e-04
    ## 804      fh1 0.63166097  9.996284e-02
    ## 805      fh2 0.78202791  8.200564e-02
    ## 806      fh4 0.79014355 -1.887418e-02
    ## 807      fh5 0.56950954  1.019015e-01
    ## 808      fh7 0.89094849 -1.041896e-02
    ## 809      fh9 0.96700674  9.283108e-03
    ## 810      fi1 1.02467133 -2.646627e-02
    ## 811      fi4 0.94936936  4.449317e-03
    ## 812      fi5 0.92345323  1.979902e-02
    ## 813      fi6 0.95437243  8.717470e-03
    ## 814      fj0 1.00184470 -2.247406e-01
    ## 815      fj2 0.88822289  3.304577e-02
    ## 816      fj4 0.96730613  8.907480e-03
    ## 817      fj7 0.94180931 -1.378817e-02
    ## 818      fj8 0.92045463  2.146456e-04
    ## 819      fj9 0.93385187 -3.788487e-03
    ## 820      fk0 0.91732387  1.162591e-02
    ## 821      fk1 0.88680619  1.402165e-02
    ## 822      fk2 0.90461827  1.262334e-02
    ## 823      fk3 0.88842012 -1.854303e-02
    ## 824      fk5 0.99745667 -2.279379e-02
    ## 825      fk7 0.93367981  1.034191e-02
    ## 826      fk8 1.00808571 -2.713756e-02
    ## 827      fk9 0.90942970  1.224563e-02
    ## 828      fl0 0.85828503  3.624036e-02
    ## 829      fl4 0.79547700  3.644038e-02
    ## 830      fl5 0.74281700  6.198353e-02
    ## 831      fl6 0.51176536  4.346363e-02
    ## 832      fm1 0.61440440  1.150056e-01
    ## 833      fm2 1.02537130 -4.409235e-02
    ## 834      fm4 0.89954446  1.302165e-02
    ## 835      fm5 0.89398752  2.660220e-02
    ## 836      fm6 0.90960177  3.457513e-03
    ## 837      fm9 0.73447347  9.184942e-02
    ## 838      fn0 0.81523828  1.963997e-02
    ## 839      fn1 0.70670307  2.816036e-02
    ## 840      fn2 0.86589139  2.241257e-02
    ## 841      fn5 0.91425425  2.193286e-02
    ## 842      fn8 0.87044086  3.409285e-02
    ## 843      fo0 0.55142444  1.520962e-01
    ## 844      fo1 0.95820412  4.669656e-03
    ## 845      fo2 0.94387517  9.541540e-03
    ## 846      fo3 0.67839254  3.038283e-02
    ## 847      fo4 0.85081474  1.650731e-02
    ## 848      fo5 0.58465729  3.774137e-02
    ## 849      fo7 0.90180401  1.284427e-02
    ## 850      fo8 0.72461097  5.873331e-02
    ## 851      fp1 0.96856978 -1.666992e-02
    ## 852      fp2 0.89954446  1.302165e-02
    ## 853      fp4 0.82140846  1.257971e-03
    ## 854      fp5 0.33322040  1.914933e-01
    ## 855      fp6 0.70670307  2.816036e-02
    ## 856      fp9 0.68237077  1.067294e-01
    ## 857      fq0 0.93367981  1.034191e-02
    ## 858      fq1 0.86060193  3.202841e-02
    ## 859      fq5 0.74994731  2.476554e-02
    ## 860      fq6 0.96189627 -1.216824e-02
    ## 861      fq8 0.88680619  1.402165e-02
    ## 862      fr0 0.69214601  4.654531e-02
    ## 863      fr1 0.99481987 -3.390749e-02
    ## 864      fr2 0.87595296  4.121355e-03
    ## 865      fr4 0.87400450  7.663511e-03
    ## 866      fr5 0.97305562  2.270234e-03
    ## 867      fr9 0.85191015  2.018683e-03
    ## 868      fs0 0.86589139  2.241257e-02
    ## 869      fs1 1.00690946 -2.561831e-02
    ## 870      fs3 0.87454318  2.968271e-02
    ## 871      fs4 0.95269436  8.849205e-03
    ## 872      fs5 0.93385187 -3.788487e-03
    ## 873      fs6 0.96442907  7.927990e-03
    ## 874      fs9 0.92727851  3.898371e-03
    ## 875      ft0 0.92377350  1.111959e-02
    ## 876      ft2 0.91485547  2.502628e-02
    ## 877      ft6 0.90942970  1.224563e-02
    ## 878      ft8 0.94387517  9.541540e-03
    ## 879      ft9 0.94139788 -4.270309e-02
    ## 880      fu0 0.88680619  1.402165e-02
    ## 881      fu1 0.93610082 -6.949754e-02
    ## 882      fu2 0.74994731  2.476554e-02
    ## 883      fu3 0.70684117  6.404298e-02
    ## 884      fu4 0.95790283 -6.175681e-03
    ## 885      fu5 0.95728949  8.488471e-03
    ## 886      fu6 0.79597865 -1.305776e-02
    ## 887      fu7 0.93756621  1.003682e-02
    ## 888      fu8 0.94647067  9.337785e-03
    ## 889      fv1 0.98760589 -3.541259e-03
    ## 890      fv4 0.98041176 -6.500702e-03
    ## 891      fv6 0.46045991  1.574611e-01
    ## 892      fv7 0.99155215 -1.915919e-02
    ## 893      fv8 0.86333341  2.706278e-02
    ## 894      fv9 0.56089892  1.076510e-01
    ## 895      fw1 0.59706637  8.725198e-02
    ## 896      fw3 0.95016793 -8.663774e-03
    ## 897      fw4 0.97706832 -9.359266e-03
    ## 898      fw5 0.37599827  1.628998e-01
    ## 899      fw7 0.96186678  6.763839e-03
    ## 900      fw9 1.10759064 -8.209980e-02
    ## 901      fx2 0.69156433  1.680116e-02
    ## 902      fx4 0.72383820  8.657170e-02
    ## 903      fx7 0.67851375 -3.678436e-03
    ## 904      fx8 0.93998358  3.442381e-03
    ## 905      fx9 0.90844869 -5.495349e-02
    ## 906      fy0 0.76909264  4.540632e-02
    ## 907      fy1 0.77718555  4.302387e-02
    ## 908      fy2 0.85828503  3.624036e-02
    ## 909      fy3 0.92798783 -5.157952e-02
    ## 910      fy5 0.80855130  2.016493e-02
    ## 911      fy6 0.98053468 -6.377413e-03
    ## 912      fy8 0.88680619  1.402165e-02
    ## 913      fz1 0.98770207 -1.987908e-02
    ## 914      fz2 0.86389174  2.604777e-02
    ## 915      fz3 0.95016793 -8.663774e-03
    ## 916      fz5 0.78201985  2.224773e-02
    ## 917      fz7 0.86333341  2.706278e-02
    ## 918      fz8 0.74994731  2.476554e-02
    ## 919      fz9 0.99470107 -2.197041e-02
    ## 920      ga1 0.80850476  4.095153e-02
    ## 921      ga3 0.99010032 -3.053211e-02
    ## 922      ga5 0.90461827  1.262334e-02
    ## 923      ga6 0.80984793  2.006314e-02
    ## 924      ga9 0.88773012  3.235694e-02
    ## 925      gb0 0.90942970  1.224563e-02
    ## 926      gb1 0.87316034  8.793905e-03
    ## 927      gb3 0.79785821  2.100437e-02
    ## 928      gb4 0.70535393  9.647407e-02
    ## 929      gb6 0.90015218  2.409780e-02
    ## 930      gb7 0.89646325  6.856842e-03
    ## 931      gb9 0.84214369  2.361419e-02
    ## 932      gc0 0.98702737  2.759599e-03
    ## 933      gc2 0.94736285  1.612934e-02
    ## 934      gc3 0.90091746 -4.126227e-02
    ## 935      gc5 1.07479163 -5.884182e-02
    ## 936      gc7 0.97703564  6.938331e-03
    ## 937      gd1 0.86778683 -1.399847e-02
    ## 938      gd7 0.85876358  1.785846e-02
    ## 939      gd8 0.68688080  7.060885e-02
    ## 940      ge1 0.81111746  3.288492e-02
    ## 941      ge3 0.90960177  3.457513e-03
    ## 942      ge5 0.85240604  1.672218e-02
    ## 943      ge6 0.92427398  1.437162e-02
    ## 944      ge7 0.85760350  3.747935e-02
    ## 945      ge8 0.97073333 -1.480878e-02
    ## 946      ge9 0.86852069  1.763269e-02
    ## 947      gf0 0.99745667 -2.279379e-02
    ## 948      gf2 0.74994731  2.476554e-02
    ## 949      gf5 0.89387744  2.665956e-02
    ## 950      gf7 0.69344503  7.331269e-02
    ## 951      gf9 0.51089697  4.353180e-02
    ## 952      gg0 0.86333341  2.706278e-02
    ## 953      gg1 0.89769444  1.316689e-02
    ## 954      gg2 0.91507503  1.802786e-02
    ## 955      gg4 0.95269436  8.849205e-03
    ## 956      gg5 0.90899292 -1.414447e-02
    ## 957      gg6 0.74694209  4.209440e-02
    ## 958      gg7 0.86665062  2.103233e-02
    ## 959      gh0 0.88680619  1.402165e-02
    ## 960      gh1 0.73924560  5.436044e-02
    ## 961      gh3 0.97244069 -6.417612e-03
    ## 962      gh4 0.85455670  4.301819e-02
    ## 963      gh6 0.88009811 -3.414224e-03
    ## 964      gh8 0.91510878 -2.212354e-02
    ## 965      gh9 0.97028609  7.468194e-03
    ## 966      gi1 1.04243238 -3.897521e-02
    ## 967      gi2 0.86333341  2.706278e-02
    ## 968      gi4 0.96718617  7.711548e-03
    ## 969      gi7 0.88680619  1.402165e-02
    ## 970      gj0 1.02885063 -1.673830e-01
    ## 971      gj2 0.68721616  6.373601e-02
    ## 972      gj4 0.95083898  8.994858e-03
    ## 973      gj5 0.93367981  1.034191e-02
    ## 974      gk0 0.92261601  1.121046e-02
    ## 975      gk3 0.37611611  5.411256e-02
    ## 976      gk4 1.07081665 -6.201842e-02
    ## 977      gk5 0.90983979  2.526416e-02
    ## 978      gl1 0.93355226  1.480144e-02
    ## 979      gl2 1.07881305 -7.342400e-02
    ## 980      gl4 0.42069658  1.534820e-01
    ## 981      gl6 0.91885647  1.836519e-02
    ## 982      gl7 0.84214369  2.361419e-02
    ## 983      gl9 0.85422019  4.362995e-02
    ## 984      gm1 0.87624416  3.751006e-02
    ## 985      gm5 0.94523677 -2.125384e-02
    ## 986      gm7 0.62995111  7.070918e-02
    ## 987      gm8 0.86589139  2.241257e-02
    ## 988      gn0 0.87758277  3.610320e-02
    ## 989      gn5 0.90414445  1.714403e-02
    ## 990      go0 0.83997056  5.505911e-02
    ## 991      go2 0.91954538  3.705507e-03
    ## 992      go3 0.85548419  4.133209e-02
    ## 993      go4 1.03715162 -4.855340e-02
    ## 994      go7 0.86333341  2.706278e-02
    ## 995      go8 0.89073762  2.227756e-02
    ## 996      go9 0.73938089 -7.934905e-02
    ## 997      gp0 0.96189627 -1.216824e-02
    ## 998      gp1 0.48357233  1.307564e-01
    ## 999      gp2 0.52564185  1.242979e-01
    ## 1000     gp3 0.59692553  3.677827e-02
    ## 1001     gp4 0.88869863 -1.904933e-02
    ## 1002     gp7 0.96317578 -4.479527e-03
    ## 1003     gp8 0.86084171  3.159251e-02
    ## 1004     gp9 0.89954446  1.302165e-02
    ## 1005     gq0 0.87233701  1.069489e-02
    ## 1006     gq1 0.92207706 -3.185756e-02
    ## 1007     gq2 0.90942970  1.224563e-02
    ## 1008     gq4 0.91947772  2.271086e-02
    ## 1009     gq8 0.97042733 -4.428737e-02
    ## 1010     gr8 0.88680619  1.402165e-02
    ## 1011     gr9 0.89954446  1.302165e-02
    ## 1012     gs0 0.77366363  5.443090e-02
    ## 1013     gs2 0.89390379  4.957320e-03
    ## 1014     gs3 0.88680619  1.402165e-02
    ## 1015     gs5 0.85332477  4.525775e-02
    ## 1016     gs6 0.87314459  3.146885e-02
    ## 1017     gs8 0.96655969  7.760729e-03
    ## 1018     gs9 0.74994731  2.476554e-02
    ## 1019     gt0 0.85655817  3.937968e-02
    ## 1020     gt1 0.95728949  8.488471e-03
    ## 1021     gt3 0.92411850 -4.224374e-03
    ## 1022     gt4 0.95016793 -8.663774e-03
    ## 1023     gt6 0.58710838  9.981949e-02
    ## 1024     gt9 0.99470107 -2.197041e-02
    ## 1025     gu0 0.93001613 -7.860310e-02
    ## 1026     gu1 0.85912025  3.472200e-02
    ## 1027     gu2 0.96189627 -1.216824e-02
    ## 1028     gu3 0.95016793 -8.663774e-03
    ## 1029     gu5 0.97763094 -1.686980e-02
    ## 1030     gu7 0.73952364  2.558383e-02
    ## 1031     gv1 0.91335374  2.691825e-02
    ## 1032     gv3 0.94671204  1.651224e-02
    ## 1033     gv7 0.86555566  2.302289e-02
    ## 1034     gv8 0.92377350  1.111959e-02
    ## 1035     gv9 0.79785821  2.100437e-02
    ## 1036     gw0 0.87370688  2.792426e-02
    ## 1037     gw1 0.95463776  3.496389e-03
    ## 1038     gw3 0.95437243  8.717470e-03
    ## 1039     gw4 0.85912025  3.472200e-02
    ## 1040     gw5 0.63660532  1.052459e-01
    ## 1041     gw7 0.89399005  4.847154e-03
    ## 1042     gx0 0.88261523  1.435065e-02
    ## 1043     gx1 0.89158998  1.364611e-02
    ## 1044     gx2 0.90129138  1.288451e-02
    ## 1045     gx5 0.94262890  1.184363e-02
    ## 1046     gx6 0.86333341  2.706278e-02
    ## 1047     gx8 0.94544302  1.000808e-02
    ## 1048     gy1 0.91423343  2.948162e-02
    ## 1049     gy3 0.91954538  3.705507e-03
    ## 1050     gy7 0.95437243  8.717470e-03
    ## 1051     gz0 0.90548253  4.688353e-03
    ## 1052     gz1 0.86589139  2.241257e-02
    ## 1053     gz2 0.88703193  3.523776e-02
    ## 1054     gz4 0.92377350  1.111959e-02
    ## 1055     gz5 0.96362066  7.991452e-03
    ## 1056     gz7 0.88680619  1.402165e-02
    ## 1057     ha0 0.86333341  2.706278e-02
    ## 1058     ha1 0.92727851  3.898371e-03
    ## 1059     ha2 0.88458580  2.332332e-02
    ## 1060     ha3 0.76529483 -1.415042e-04
    ## 1061     ha6 0.62983761  8.126491e-02
    ## 1062     ha7 0.91954538  3.705507e-03
    ## 1063     ha9 1.00748982 -6.450780e-02
    ## 1064     hb0 0.87983611 -1.213632e-02
    ## 1065     hb2 0.70912363  9.542966e-02
    ## 1066     hb4 0.94840846  9.513844e-03
    ## 1067     hb6 0.94647067  9.337785e-03
    ## 1068     hb8 0.96129607  4.746770e-03
    ## 1069     hb9 0.83902844  3.130681e-02
    ## 1070     hc0 0.88837891  2.750751e-02
    ## 1071     hc1 0.86333341  2.706278e-02

``` r
level_id.model_coefs %>% 
  left_join(student_info %>% select(anon_id, native_language), by = 'anon_id') %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id), by = 'anon_id') %>% 
  ggplot()+
  geom_boxplot(aes(x = factor(level_id.y), y = Intercept)) + 
  labs(x = "ELI Level", y = "Random Speaker Intercept", title = "Effects of ELI Level on Speaker Intercept")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntSloLevelBoxplot-1.png)<!-- -->

``` r
level_id.model_coefs %>% 
  left_join(student_info %>% select(anon_id, native_language), by = 'anon_id') %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id), by = 'anon_id') %>% 
  ggplot()+
  geom_boxplot(aes(x = factor(level_id.y), y = level_id.x)) + 
  labs(x = "ELI Level", y = "Random Speaker Slope", title = "Effects of ELI Level on Speaker Slope")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntSloLevelBoxplotSlope-1.png)<!-- -->

#### ANOVA of Prediction ‘will’ with Level Id using Random Intercepts and Random Slopes

``` r
summary(aov(Intercept ~  level_id.y, data = 
    level_id.model_coefs %>% 
    left_join(future_tokens_data %>% select(anon_id, level_id), by = 'anon_id') %>% 
    distinct()
  ))
```

    ##               Df Sum Sq Mean Sq F value   Pr(>F)    
    ## level_id.y     1   0.23 0.23241   10.92 0.000969 ***
    ## Residuals   1677  35.68 0.02128                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

``` r
summary(aov( level_id.x ~ level_id.y, data = 
    level_id.model_coefs %>% 
    left_join(future_tokens_data %>% select(anon_id, level_id), by = 'anon_id') %>% 
    distinct()
  ))
```

    ##               Df Sum Sq  Mean Sq F value  Pr(>F)   
    ## level_id.y     1  0.025 0.025155   8.147 0.00437 **
    ## Residuals   1677  5.178 0.003088                   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
