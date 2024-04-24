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
  - [Correlations For each L1](#correlations-for-each-l1)
- [Random Intercepts Analysis](#random-intercepts-analysis)
  - [Token Level Analysis](#token-level-analysis)
    - [Predict ‘will’ with Mean of Prof Scores using Random
      Intercepts](#predict-will-with-mean-of-prof-scores-using-random-intercepts)
    - [Predict ‘will’ with Mean of Prof Scores using Random Intercepts
      and Random
      Slopes](#predict-will-with-mean-of-prof-scores-using-random-intercepts-and-random-slopes)
    - [Predict ‘will’ with Years of English Learning using Random
      Intercepts](#predict-will-with-years-of-english-learning-using-random-intercepts)
  - [Token Context Analysis](#token-context-analysis)

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

``` r
create_corr_diagram = function(scaled_construction_props_30plus, title_str){
  
  cm = as_tibble(cor(scaled_construction_props_30plus %>%  
           filter(!is.na(prop_will_construction)) %>%
           select(prop_will_construction, LCT_Score, MTELP_I, MTELP_II, MTELP_III, MTELP_Conv_Score, Writing_Sample)))
  
  

  cm %>% 
    mutate(score = colnames(cm)) %>%
    pivot_longer(!score) %>%
    mutate(value = round(value,2)) %>% 
    
    ggplot(aes(x = score, y = name, fill = value)) +
    
    geom_tile(color = "white") +
    scale_fill_gradient2(
      low = "blue",
      high = "red",
      mid = "white",
      midpoint = 0,
      limit = c(-1, 1),
      space = "Lab",
      name = "Pearson\nCorrelation"
    ) +
    theme_minimal() + # minimal theme
    theme(axis.text.x = element_text(
      angle = 45,
      vjust = 1,
      size = 12,
      hjust = 1
    )) +
    coord_fixed() +
    geom_text(aes(score, name, label = value),
              color = "black",
              size = 4) +
    theme(
      axis.title.x = element_blank(),
      axis.title.y = element_blank(),
      panel.grid.major = element_blank(),
      panel.border = element_blank(),
      panel.background = element_blank(),
      axis.ticks = element_blank(),
      legend.justification = c(1, 0),
      legend.direction = "horizontal"
    ) +
    guides(fill = guide_colorbar(
      barwidth = 7,
      barheight = 1,
      title.position = "top",
      title.hjust = 0.5
    ))+
    labs(title = paste0("Scaled Proficiency Scores Correlation (30 plus) - ",title_str))
    
}
```

``` r
create_corr_diagram(scaled_construction_props_30plus, "All Langs")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-allLangCorrGram-1.png)<!-- -->

``` r
construction_props %>%
  #Get Only languages with at least 30 L1 speakers
  group_by(native_language) %>% 
  filter(n()>30) %>% 
  select(native_language, prop_will_construction, prop_goingTo_construction) %>% 
  
  summarise(across(everything(), .f = list(mean = mean), na.rm = TRUE)) %>% 
  pivot_longer(cols = starts_with('prop')) %>% 
  
  
  #Plotting
  mutate(name = str_split_i(name, "_",2)) %>% 
  arrange(desc(value)) %>% 
  mutate(Group = factor(native_language, levels = unique(native_language))) %>% 
  ggplot(aes(Group, value, fill = name))+
    geom_bar(stat = 'identity', position = 'dodge')+
    labs(title = "L1 In Future Tense Construction Proportion", x = "Native Language (L1)", y = "Proportion", fill = "Construction") +
    theme_minimal()
```

    ## Warning: There was 1 warning in `summarise()`.
    ## ℹ In argument: `across(everything(), .f = list(mean = mean), na.rm = TRUE)`.
    ## ℹ In group 1: `native_language = "Arabic"`.
    ## Caused by warning:
    ## ! The `...` argument of `across()` is deprecated as of dplyr 1.1.0.
    ## Supply arguments directly to `.fns` through an anonymous function instead.
    ## 
    ##   # Previously
    ##   across(a:b, mean, na.rm = TRUE)
    ## 
    ##   # Now
    ##   across(a:b, \(x) mean(x, na.rm = TRUE))

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-L1ConstructionProp-1.png)<!-- -->

``` r
scaled_construction_props_30plus_withL1 = scaled_construction_props_30plus %>% 
  left_join(construction_counts %>% select(anon_id, native_language), by = 'anon_id') %>% 
  group_by(native_language) %>% 
  filter(n()>10)

scaled_construction_props_30plus_withL1 %>% count(native_language)
```

    ## # A tibble: 5 × 2
    ## # Groups:   native_language [5]
    ##   native_language     n
    ##   <chr>           <int>
    ## 1 Arabic             74
    ## 2 Chinese            34
    ## 3 Japanese           11
    ## 4 Korean             35
    ## 5 Thai               11

## Correlations For each L1

``` r
create_corr_diagram(
  scaled_construction_props_30plus_withL1 %>% filter(native_language == 'Arabic') %>% ungroup() %>%  select(-native_language,-anon_id), "Arabic")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-langCorrgramArabic-1.png)<!-- -->

``` r
create_corr_diagram(
  scaled_construction_props_30plus_withL1 %>% filter(native_language == 'Chinese') %>% ungroup() %>%  select(-native_language,-anon_id), "Chinese")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-langCorrgramChinese-1.png)<!-- -->

``` r
create_corr_diagram(
  scaled_construction_props_30plus_withL1 %>% filter(native_language == 'Japanese') %>% ungroup() %>%  select(-native_language,-anon_id), "Japanese")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-langCorrgramJapanese-1.png)<!-- -->

``` r
create_corr_diagram(
  scaled_construction_props_30plus_withL1 %>% filter(native_language == 'Korean') %>% ungroup() %>%  select(-native_language,-anon_id), "Korean")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-langCorrgramKorean-1.png)<!-- -->

``` r
create_corr_diagram(
  scaled_construction_props_30plus_withL1 %>% filter(native_language == 'Thai') %>% ungroup() %>%  select(-native_language,-anon_id), "Thai")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-langCorrgramThai-1.png)<!-- -->

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

### Predict ‘will’ with Mean of Prof Scores using Random Intercepts

``` r
mean_prof_score.intercepts_model <- lmer(is_will_construction ~ (1 | anon_id) + mean_prof_score, data = future_tokens_data)

mean_prof_score.intercepts_model.coefs = coef(mean_prof_score.intercepts_model)$anon_id %>% 
  #Change names to intercept
  rename(Intercept = `(Intercept)`, Slope = mean_prof_score) %>% 
  rownames_to_column("anon_id")
```

#### Visualize by Native Languages

``` r
mean_prof_score.intercepts_model.coefs %>% 
  #Bring in student info
  left_join(student_info %>% select(anon_id, native_language), by = 'anon_id') %>% 
  #Only use L1s with at least 30 students
  group_by(native_language) %>% filter(n()>30) %>% 
  left_join(future_tokens_data %>% select(anon_id, mean_prof_score), by = 'anon_id') %>% 
  ggplot()+
  geom_point(aes(x = mean_prof_score, y = Intercept)) +
  facet_wrap(~ native_language)
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntProfsVisByL1-1.png)<!-- -->

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

### Predict ‘will’ with Years of English Learning using Random Intercepts

``` r
yrs_learning_eng_intercepts_model <- lmer(is_will_construction ~ (1 | anon_id) + yrs_of_english_learning , data = future_tokens_data)

yrs_learning_eng_model_coefs = coef(yrs_learning_eng_intercepts_model)$anon_id %>% 
  rename(Intercept = `(Intercept)`) %>% 
  rownames_to_column("anon_id")

yrs_learning_eng_model_coefs %>% 
  left_join(student_info %>% select(anon_id, yrs_of_english_learning, native_language), by = 'anon_id') %>% 
  group_by(native_language) %>% filter(n()>30) %>% 
  ggplot()+
  geom_boxplot(aes(x = yrs_of_english_learning, y = Intercept))
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntYrsLearning-1.png)<!-- -->
\### Predict ‘will’ with Level using Random Intercepts

``` r
level_id.intercepts_model <- lmer(is_will_construction ~ (1 | anon_id) + level_id, data = future_tokens_data %>% mutate(level_id = factor(level_id)))

level_id.intercepts_model
```

    ## Linear mixed model fit by REML ['lmerMod']
    ## Formula: is_will_construction ~ (1 | anon_id) + level_id
    ##    Data: future_tokens_data %>% mutate(level_id = factor(level_id))
    ## REML criterion at convergence: 5935.629
    ## Random effects:
    ##  Groups   Name        Std.Dev.
    ##  anon_id  (Intercept) 0.1168  
    ##  Residual             0.2710  
    ## Number of obs: 20307, groups:  anon_id, 1071
    ## Fixed Effects:
    ## (Intercept)    level_id3    level_id4    level_id5  
    ##      0.7343       0.1288       0.1772       0.1742

``` r
level_id.model_coefs = coef(level_id.intercepts_model)$anon_id %>% 
  rename(Intercept = `(Intercept)`) %>% 
  rownames_to_column("anon_id")

level_id.model_coefs %>% 
  left_join(student_info %>% select(anon_id, native_language), by = 'anon_id') %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id), by = 'anon_id') %>% 
  ggplot()+
  geom_boxplot(aes(x = factor(level_id), y = Intercept)) + 
  labs(x = "ELI Level", y = "Random Speaker Intercept", title = "Effects of ELI Level on Speaker Intercept")
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-randIntLevel-1.png)<!-- -->
\#### ANOVA of Prediction ‘will’ with Level Id using Random Intercepts

``` r
summary(aov(Intercept ~ level_id, data = level_id.model_coefs %>% 
  select(anon_id,Intercept) %>% 
  left_join(future_tokens_data %>% select(anon_id, level_id), by = 'anon_id') %>% 
  mutate(level_id = factor(level_id)) %>% 
  distinct()))
```

    ##               Df Sum Sq  Mean Sq F value Pr(>F)  
    ## level_id       3  0.064 0.021403   2.425  0.064 .
    ## Residuals   1675 14.782 0.008825                 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

``` r
text_len.intercepts_model <- lmer(is_will_construction ~ text_len + (1 + text_len | anon_id), data = future_tokens_data %>% mutate(text_len = scale(text_len)))

text_len.intercepts_model
```

    ## Linear mixed model fit by REML ['lmerMod']
    ## Formula: is_will_construction ~ text_len + (1 + text_len | anon_id)
    ##    Data: future_tokens_data %>% mutate(text_len = scale(text_len))
    ## REML criterion at convergence: 5936.773
    ## Random effects:
    ##  Groups   Name        Std.Dev. Corr 
    ##  anon_id  (Intercept) 0.11702       
    ##           text_len    0.04465  -0.01
    ##  Residual             0.26974       
    ## Number of obs: 20307, groups:  anon_id, 1071
    ## Fixed Effects:
    ## (Intercept)     text_len  
    ##     0.90216      0.01841

``` r
text_len.model_coefs = coef(text_len.intercepts_model)$anon_id %>% 
  rename(Intercept = `(Intercept)`) %>% 
  rownames_to_column("anon_id")

text_len.model_coefs %>% 
  left_join(student_info %>% select(anon_id, native_language), by = 'anon_id') %>% 
  left_join(future_tokens_data %>% select(anon_id, text_len, level_id), by = 'anon_id') %>% 
  ggplot()+
  geom_point(aes(x = text_len.y, y = Intercept))+
  facet_wrap(~level_id)
```

![](C:/Users/dcraw/projects/ling_projects/Future-Tense-Construction-L2English/images/DataAnalysis-textLenModel-1.png)<!-- -->

## Token Context Analysis

``` r
tokenized_df_unnested %>% 
  mutate(will_next_V_code = if_else(
    trimws(lemma) == 'will' & trimws(POS) == 'MD', 
    if_else(
      startsWith(trimws(lead(POS)),'V'),
      lead(POS),
      if_else(
        startsWith(trimws(lead(POS,2)),'V'),
        lead(POS,2),
        lead(POS,3)
      )
    ),
    'NotV')) %>% 
 
  filter(trimws(lemma) == 'will' & trimws(POS) == 'MD') %>% 
  mutate(will_next_V_code = if_else(
    startsWith(trimws(will_next_V_code),'V'), will_next_V_code,'Other_NonV' 
  )) %>% 
  count(will_next_V_code)
```

    ##   will_next_V_code     n
    ## 1               VB 17863
    ## 2              VBD    48
    ## 3              VBG    57
    ## 4              VBN    38
    ## 5              VBP    21
    ## 6              VBZ    61
    ## 7       Other_NonV   323

``` r
tokenized_df_unnested %>% 
  mutate(goingTo_next_V_code = if_else(
      token == "going" & lead(token) == "to" & (
        #lead allows to 'look ahead'
        startsWith(trimws(lead(POS, 2)), 'V') | 
        startsWith(trimws(lead(POS, 3)), 'V')),
      
    if_else(
      startsWith(trimws(lead(POS)),'V'),
      lead(POS),
      if_else(
        startsWith(trimws(lead(POS,2)),'V'),
        lead(POS,2),
        lead(POS,3)
      )
    ),
    'NotV')) %>% 
 
  filter(token == "going" & lead(token) == "to") %>% 
  count(goingTo_next_V_code)
```

    ##   goingTo_next_V_code    n
    ## 1                  VB 1870
    ## 2                 VBD    1
    ## 3                 VBG   15
    ## 4                 VBN    4
    ## 5                 VBZ    6
    ## 6                NotV  497
