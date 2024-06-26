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
    - [Predict ‘will’ with Level using Random Intercepts and Random
      Slopes](#predict-will-with-level-using-random-intercepts-and-random-slopes)
  - [Longitudinal Analysis](#longitudinal-analysis)

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
```

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
![Correlation Image](../images/DataAnalysis-distScores-1.png)

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
![Correlation Image](../images/DataAnalysis-distWritingScores-1.png)

## Scale Proficiency Scores

``` r
#Scale Scores

scaled_construction_props = construction_props %>% 
  select(anon_id, prop_will_construction, prop_goingTo_construction, LCT_Score, MTELP_I, MTELP_II, MTELP_III, MTELP_Conv_Score, Writing_Sample) %>% 
  mutate(across(-c(anon_id,prop_will_construction, prop_goingTo_construction), ~ scale(.x))) 
```

``` r
#Get Scaled values, but only for students who used future tense at least 30 times. 

scaled_construction_props_30plus = construction_counts %>% 
  filter(count_will_construction+count_goingTo_construction >= 30) %>% 
  mutate(prop_goingTo_construction = count_goingTo_construction/(count_will_construction+count_goingTo_construction), .after = anon_id) %>% 
  mutate(prop_will_construction = count_will_construction/(count_will_construction+count_goingTo_construction), .after = anon_id) %>% 
  select(anon_id, prop_will_construction, prop_goingTo_construction, LCT_Score, MTELP_I, MTELP_II, MTELP_III, MTELP_Conv_Score, Writing_Sample) %>% 
  mutate(across(-c(anon_id,prop_will_construction, prop_goingTo_construction), ~ scale(.x)))
```

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
![Correlation Image](../images/DataAnalysis-corrAn-1.png)

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
  left_join(future_tokens_data %>% select(anon_id, level_id) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>% 
  distinct() %>% 
  ggplot(aes(level_id, Intercept))+
  geom_boxplot()+
  labs(x = 'Level', title = 'Intercepts by Level ID')
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntRandSlopeLevelBoxplotOfInts-1.png)<!-- -->
![Boxplots of Intercept by
Level](../images/DataAnalysis-randIntRandSlopeLevelBoxplotOfInts-1.png)

``` r
mean_prof_score.random_slope_and_intercepts_model.coefs %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>% 
  distinct() %>% 
  ggplot(aes(level_id, mean_prof_score))+
  geom_boxplot()+
  labs(x = 'Level', title = 'Slopes by Level ID', y = 'Slope')
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntRandSlopeLevelBoxplotOfSlopes-1.png)<!-- -->
![Boxplots of Intercept by
Level](../images/DataAnalysis-randIntRandSlopeLevelBoxplotOfSlopes-1.png)

``` r
mean_prof_score.random_slope_and_intercepts_model.coefs %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>% 
  distinct() %>% count(level_id)
```

    ##   level_id   n
    ## 1        2  23
    ## 2        3 394
    ## 3        4 709
    ## 4        5 430

#### ANOVAs for Random Intercepts and Random Slopes by Level

``` r
anova(mean_prof_score.random_slope_and_intercepts_model)
```

    ## Analysis of Variance Table
    ##                 npar  Sum Sq Mean Sq F value
    ## mean_prof_score    1 0.60352 0.60352  8.2417

``` r
#ANOVA for Intercepts
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
#ANOVA for Slopes
summary(aov(mean_prof_score ~ level_id, data = mean_prof_score.random_slope_and_intercepts_model.coefs %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id, native_language) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>%
  distinct()))
```

    ##               Df  Sum Sq  Mean Sq F value   Pr(>F)    
    ## level_id       3 0.00751 0.002502   14.21 3.85e-09 ***
    ## Residuals   1552 0.27317 0.000176                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

#### Without Level 2

``` r
mean_prof_score.random_slope_and_intercepts_model.woL2 <- lmer(is_will_construction ~ mean_prof_score + (1 + mean_prof_score | anon_id), data = future_tokens_data %>% filter(level_id != 2))

summary(mean_prof_score.random_slope_and_intercepts_model.woL2)
```

    ## Linear mixed model fit by REML ['lmerMod']
    ## Formula: is_will_construction ~ mean_prof_score + (1 + mean_prof_score |  
    ##     anon_id)
    ##    Data: future_tokens_data %>% filter(level_id != 2)
    ## 
    ## REML criterion at convergence: 5033.3
    ## 
    ## Scaled residuals: 
    ##     Min      1Q  Median      3Q     Max 
    ## -3.6694  0.0819  0.1719  0.3439  2.5942 
    ## 
    ## Random effects:
    ##  Groups   Name            Variance Std.Dev. Corr
    ##  anon_id  (Intercept)     0.013460 0.11602      
    ##           mean_prof_score 0.001495 0.03867  0.08
    ##  Residual                 0.071970 0.26827      
    ## Number of obs: 18305, groups:  anon_id, 983
    ## 
    ## Fixed effects:
    ##                 Estimate Std. Error t value
    ## (Intercept)     0.899267   0.004783 188.004
    ## mean_prof_score 0.014064   0.005906   2.381
    ## 
    ## Correlation of Fixed Effects:
    ##             (Intr)
    ## men_prf_scr 0.033

``` r
mean_prof_score.random_slope_and_intercepts_modelWOL2.coefs = coef(mean_prof_score.random_slope_and_intercepts_model.woL2)$anon_id %>% 
  rename(Intercept = `(Intercept)`) %>% 
  rownames_to_column("anon_id")
```

``` r
anova(mean_prof_score.random_slope_and_intercepts_model.woL2)
```

    ## Analysis of Variance Table
    ##                 npar  Sum Sq Mean Sq F value
    ## mean_prof_score    1 0.40808 0.40808  5.6702

``` r
#ANOVA for Intercepts without Level 2
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
#ANOVA for Slopes without Level 2
summary(aov(mean_prof_score ~ level_id, data = mean_prof_score.random_slope_and_intercepts_model.coefs %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id, native_language) %>% mutate(level_id = factor(level_id)), by = 'anon_id') %>%
  distinct()))
```

    ##               Df  Sum Sq  Mean Sq F value   Pr(>F)    
    ## level_id       3 0.00751 0.002502   14.21 3.85e-09 ***
    ## Residuals   1552 0.27317 0.000176                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

### Predict ‘will’ with Level using Random Intercepts and Random Slopes

``` r
level_id.intercepts_and_slope_model <- lmer(is_will_construction ~ level_id + (1 + level_id | anon_id), data = future_tokens_data   %>% mutate(level_id = level_id-2))

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
anova(level_id.intercepts_and_slope_model)
```

    ## Analysis of Variance Table
    ##          npar  Sum Sq Mean Sq F value
    ## level_id    1 0.55862 0.55862   7.791

#### Without Level 2

``` r
level_id.intercepts_and_slope_model.woLevel2 <- lmer(is_will_construction ~ level_id + (1 + level_id | anon_id), data = future_tokens_data  %>%  filter(level_id !=2) %>% mutate(level_id = level_id-2))

summary(level_id.intercepts_and_slope_model.woLevel2)
```

    ## Linear mixed model fit by REML ['lmerMod']
    ## Formula: is_will_construction ~ level_id + (1 + level_id | anon_id)
    ##    Data: 
    ## future_tokens_data %>% filter(level_id != 2) %>% mutate(level_id = level_id -  
    ##     2)
    ## 
    ## REML criterion at convergence: 5448.2
    ## 
    ## Scaled residuals: 
    ##     Min      1Q  Median      3Q     Max 
    ## -3.7882  0.0718  0.1636  0.3496  2.6319 
    ## 
    ## Random effects:
    ##  Groups   Name        Variance Std.Dev. Corr 
    ##  anon_id  (Intercept) 0.050951 0.22572       
    ##           level_id    0.008766 0.09363  -0.87
    ##  Residual             0.070700 0.26589       
    ## Number of obs: 20122, groups:  anon_id, 1067
    ## 
    ## Fixed effects:
    ##             Estimate Std. Error t value
    ## (Intercept) 0.875208   0.012354  70.845
    ## level_id    0.013017   0.005565   2.339
    ## 
    ## Correlation of Fixed Effects:
    ##          (Intr)
    ## level_id -0.930

``` r
level_id.model_coefs.woLevel2 = coef(level_id.intercepts_and_slope_model.woLevel2)$anon_id %>% 
  rename(Intercept = `(Intercept)`) %>% 
  rownames_to_column("anon_id")
```

``` r
anova(level_id.intercepts_and_slope_model.woLevel2)
```

    ## Analysis of Variance Table
    ##          npar  Sum Sq Mean Sq F value
    ## level_id    1 0.38681 0.38681  5.4712

## Longitudinal Analysis

``` r
#score Data
score_data = as_tibble(read.csv(url('https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/corpus_files/test_scores.csv')))

#Find students who took the test more than once
n_occur <- data.frame(table(score_data$anon_id))
multiple_ids = n_occur[n_occur$Freq > 1,]$Var1

longit_score_data = score_data %>% 
  filter(trimws(anon_id) %in% multiple_ids)
```

``` r
future_tokens_data %>% 
  inner_join(longit_score_data, by = c('anon_id','semester')) %>% 
  distinct() %>% 
  mutate(scale_lct = scale(LCT_Score.y)) %>% 
  mutate(scale_mtelp = scale(MTELP_Conv_Score.y)) %>% 
  mutate(scale_wr = scale(Writing_Sample.y)) %>% 
  mutate(mean_prof_score = rowMeans(select(.,scale_lct, scale_mtelp, scale_wr), na.rm = T)) %>% 
  select(anon_id, semester, is_will_construction, is_goingTo_construction, mean_prof_score) %>% 
  group_by(anon_id, semester) %>% 
  summarise_all(mean) %>% 
  filter(n()>1) %>% 
  separate(semester,c('year',NA)) %>% 
  ggplot(aes(year, mean_prof_score, color = anon_id, group = anon_id)) +
  geom_point() + geom_line()+
  labs(title = 'Longitudinal Proficiency Increase', x = 'Year',y = 'Mean Proficiency Score (scaled)', color = 'Student (ID)')
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-longAnProf-1.png)<!-- -->
![Correlation Image](../images/DataAnalysis-longAnProf-1.png)

``` r
future_tokens_data %>% 
  inner_join(longit_score_data, by = c('anon_id','semester')) %>% 
  distinct() %>% 
  mutate(scale_lct = scale(LCT_Score.y)) %>% 
  mutate(scale_mtelp = scale(MTELP_Conv_Score.y)) %>% 
  mutate(scale_wr = scale(Writing_Sample.y)) %>% 
  mutate(mean_prof_score = rowMeans(select(.,scale_lct, scale_mtelp, scale_wr), na.rm = T)) %>% 
  select(anon_id, semester, is_will_construction, is_goingTo_construction, mean_prof_score) %>% 
  group_by(anon_id, semester) %>% 
  summarise_all(mean) %>% 
  filter(n()>1) %>% 
  separate(semester,c('year',NA)) %>% 
  ggplot(aes(year, is_will_construction, color = anon_id, group = anon_id)) +
  geom_point() + geom_line()+
  labs(title = 'Proportion of will Construction Utilized', x = 'Year',y = 'Proportion of future tokens with will constr.', color = 'Student (ID)')
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-longAnWill-1.png)<!-- -->
![Correlation Image](../images/DataAnalysis-longAnWill-1.png)
