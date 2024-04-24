FutureTenseConstructionL2English-DataProcessing
================
Daniel Crawford
04/02/2024

- [Correlation of Future Tense Construction Preference with Proficiency
  Scores for English L2
  Learners](#correlation-of-future-tense-construction-preference-with-proficiency-scores-for-english-l2-learners)
  - [Load Data](#load-data)
    - [Read in Data from PELIC](#read-in-data-from-pelic)
    - [Read in Student Indo](#read-in-student-indo)
  - [Tokenize Data](#tokenize-data)
    - [Tokenize Lemmatized Data Helper
      Function](#tokenize-lemmatized-data-helper-function)
  - [Summary of Data](#summary-of-data)
    - [Summary of ‘will’ tokens](#summary-of-will-tokens)
    - [Summary of ‘going’ tokens](#summary-of-going-tokens)
  - [Format the Data](#format-the-data)
    - [Bring in score data](#bring-in-score-data)
    - [Save The data](#save-the-data)
  - [Session Info](#session-info)

# Correlation of Future Tense Construction Preference with Proficiency Scores for English L2 Learners

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

## Load Data

Raw data can be found
[here](https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/PELIC_compiled.csv).

### Read in Data from PELIC

``` r
#Read in Data from PELIC: 

if (file.exists(paste0(dirname(getwd()),'/large_data/pelic_compiled.csv'))){
  print('Reading in File from directory')
  PELIC_compiled = read.csv(paste0(dirname(getwd()),'/large_data/pelic_compiled.csv'))
}else{
  print('Reading in File from url')
  PELIC_compiled = as_tibble(read.csv(url("https://github.com/ELI-Data-Mining-Group/PELIC-dataset/raw/master/PELIC_compiled.csv"), fileEncoding = "ISO-8859-1"))
}
```

    ## [1] "Reading in File from directory"

``` r
nrow(PELIC_compiled)
```

    ## [1] 46204

``` r
ncol(PELIC_compiled)
```

    ## [1] 16

``` r
head(PELIC_compiled)
```

    ##   X answer_id anon_id      L1 gender  semester placement_test course_id
    ## 1 1         1     eq0  Arabic   Male 2006_fall             NA       149
    ## 2 2         2     am8    Thai Female 2006_fall             NA       149
    ## 3 3         3     dk5 Turkish Female 2006_fall             NA       115
    ## 4 4         4     dk5 Turkish Female 2006_fall             NA       115
    ## 5 5         5     ad1  Korean Female 2006_fall             NA       115
    ## 6 6         6     ad1  Korean Female 2006_fall             NA       115
    ##   level_id class_id question_id version text_len
    ## 1        4        g           5       1      177
    ## 2        4        g           5       1      137
    ## 3        4        w          12       1       63
    ## 4        4        w          13       1        6
    ## 5        4        w          12       1       59
    ## 6        4        w          13       1        2
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          text
    ## 1 I met my friend Nife while I was studying in a middle school. I was happy when I met him because he was a good student in our school. We continued the middle and high school to gather in the same school. We were studying in the different classes in the middle school; however, in the high school we were studying in the same class. We went to many places in the free time while we were studying in the high school. When we finished from the high school, I went to K.S University and he went to I.M University. While we were enjoying in academic life, we made many achievement in these universities. I graduated when Nife was studying in the last semester in the university. After that, I got a job. Fortunately, it was nearby my home. I worked two years then I got scholarship from ministry of high education in my country. When I came here to U.S, my friend Nife arrange some documents to study at grad school in Malaysia.
    ## 2                                                                                                                                                                                                                                                                Ten years ago, I met a women on the train between the way was going to school,she was sitting next me.That day for school opened; She had a long back hair,a tan skin and a big brow eyes. I thought, she was so beautiful, and one thing as important she wear uniform similar me. When she took off the train,she said good bye with me. I feel better for first day of school. I was sitting until the last train stop it was near my school.I was finding the class when I met the women agin in that room. I saw the number of the room that was same me. While I was introducing my self to the women,, the teacher came in. Since we were the best friends until finished university.
    ## 3                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      In my country we usually don't use tea bags. First we boil the water,then we put some of the hot water at the top.We add the tea at the top. We wait like 20 minutes with low heat for it to be ready.At last, we first poor the water with tea,then we add some pure hot water in it.
    ## 4                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       I organized the instructions by time.
    ## 5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      First, prepare a port, loose tea, and cup.\nSecond, boil water in the port.\nThird, put loose tea in the cup.\nFourth, pour water into the cup.\nFinally, after waiting for a few minutes, drink the tea. Loose tea won't float on the surface of the tea, so you don't have to pick the loose tea up.
    ## 6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     By time
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               tokens
    ## 1 ['I', 'met', 'my', 'friend', 'Nife', 'while', 'I', 'was', 'studying', 'in', 'a', 'middle', 'school', '.', 'I', 'was', 'happy', 'when', 'I', 'met', 'him', 'because', 'he', 'was', 'a', 'good', 'student', 'in', 'our', 'school', '.', 'We', 'continued', 'the', 'middle', 'and', 'high', 'school', 'to', 'gather', 'in', 'the', 'same', 'school', '.', 'We', 'were', 'studying', 'in', 'the', 'different', 'classes', 'in', 'the', 'middle', 'school', ';', 'however', ',', 'in', 'the', 'high', 'school', 'we', 'were', 'studying', 'in', 'the', 'same', 'class', '.', 'We', 'went', 'to', 'many', 'places', 'in', 'the', 'free', 'time', 'while', 'we', 'were', 'studying', 'in', 'the', 'high', 'school', '.', 'When', 'we', 'finished', 'from', 'the', 'high', 'school', ',', 'I', 'went', 'to', 'K', '.', 'S', 'University', 'and', 'he', 'went', 'to', 'I', '.', 'M', 'University', '.', 'While', 'we', 'were', 'enjoying', 'in', 'academic', 'life', ',', 'we', 'made', 'many', 'achievement', 'in', 'these', 'universities', '.', 'I', 'graduated', 'when', 'Nife', 'was', 'studying', 'in', 'the', 'last', 'semester', 'in', 'the', 'university', '.', 'After', 'that', ',', 'I', 'got', 'a', 'job', '.', 'Fortunately', ',', 'it', 'was', 'nearby', 'my', 'home', '.', 'I', 'worked', 'two', 'years', 'then', 'I', 'got', 'scholarship', 'from', 'ministry', 'of', 'high', 'education', 'in', 'my', 'country', '.', 'When', 'I', 'came', 'here', 'to', 'U.S', ',', 'my', 'friend', 'Nife', 'arrange', 'some', 'documents', 'to', 'study', 'at', 'grad', 'school', 'in', 'Malaysia', '.']
    ## 2                                                                                                                                                                                                                                                                                                                                                                                          ['Ten', 'years', 'ago', ',', 'I', 'met', 'a', 'women', 'on', 'the', 'train', 'between', 'the', 'way', 'was', 'going', 'to', 'school', ',', 'she', 'was', 'sitting', 'next', 'me', '.', 'That', 'day', 'for', 'school', 'opened', ';', 'She', 'had', 'a', 'long', 'back', 'hair', ',', 'a', 'tan', 'skin', 'and', 'a', 'big', 'brow', 'eyes', '.', 'I', 'thought', ',', 'she', 'was', 'so', 'beautiful', ',', 'and', 'one', 'thing', 'as', 'important', 'she', 'wear', 'uniform', 'similar', 'me', '.', 'When', 'she', 'took', 'off', 'the', 'train', ',', 'she', 'said', 'good', 'bye', 'with', 'me', '.', 'I', 'feel', 'better', 'for', 'first', 'day', 'of', 'school', '.', 'I', 'was', 'sitting', 'until', 'the', 'last', 'train', 'stop', 'it', 'was', 'near', 'my', 'school', '.', 'I', 'was', 'finding', 'the', 'class', 'when', 'I', 'met', 'the', 'women', 'agin', 'in', 'that', 'room', '.', 'I', 'saw', 'the', 'number', 'of', 'the', 'room', 'that', 'was', 'same', 'me', '.', 'While', 'I', 'was', 'introducing', 'my', 'self', 'to', 'the', 'women', ',', ',', 'the', 'teacher', 'came', 'in', '.', 'Since', 'we', 'were', 'the', 'best', 'friends', 'until', 'finished', 'university', '.']
    ## 3                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       ['In', 'my', 'country', 'we', 'usually', 'do', "n't", 'use', 'tea', 'bags', '.', 'First', 'we', 'boil', 'the', 'water', ',', 'then', 'we', 'put', 'some', 'of', 'the', 'hot', 'water', 'at', 'the', 'top', '.', 'We', 'add', 'the', 'tea', 'at', 'the', 'top', '.', 'We', 'wait', 'like', '20', 'minutes', 'with', 'low', 'heat', 'for', 'it', 'to', 'be', 'ready', '.', 'At', 'last', ',', 'we', 'first', 'poor', 'the', 'water', 'with', 'tea', ',', 'then', 'we', 'add', 'some', 'pure', 'hot', 'water', 'in', 'it', '.']
    ## 4                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       ['I', 'organized', 'the', 'instructions', 'by', 'time', '.']
    ## 5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 ['First', ',', 'prepare', 'a', 'port', ',', 'loose', 'tea', ',', 'and', 'cup', '.', 'Second', ',', 'boil', 'water', 'in', 'the', 'port', '.', 'Third', ',', 'put', 'loose', 'tea', 'in', 'the', 'cup', '.', 'Fourth', ',', 'pour', 'water', 'into', 'the', 'cup', '.', 'Finally', ',', 'after', 'waiting', 'for', 'a', 'few', 'minutes', ',', 'drink', 'the', 'tea', '.', 'Loose', 'tea', 'wo', "n't", 'float', 'on', 'the', 'surface', 'of', 'the', 'tea', ',', 'so', 'you', 'do', "n't", 'have', 'to', 'pick', 'the', 'loose', 'tea', 'up', '.']
    ## 6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     ['By', 'time']
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               tok_lem_POS
    ## 1 [('I', 'I', 'PRP'), ('met', 'meet', 'VBD'), ('my', 'my', 'PRP$'), ('friend', 'friend', 'NN'), ('Nife', 'Nife', 'NNP'), ('while', 'while', 'IN'), ('I', 'I', 'PRP'), ('was', 'be', 'VBD'), ('studying', 'study', 'VBG'), ('in', 'in', 'IN'), ('a', 'a', 'DT'), ('middle', 'middle', 'JJ'), ('school', 'school', 'NN'), ('.', '.', '.'), ('I', 'I', 'PRP'), ('was', 'be', 'VBD'), ('happy', 'happy', 'JJ'), ('when', 'when', 'WRB'), ('I', 'I', 'PRP'), ('met', 'meet', 'VBD'), ('him', 'him', 'PRP'), ('because', 'because', 'IN'), ('he', 'he', 'PRP'), ('was', 'be', 'VBD'), ('a', 'a', 'DT'), ('good', 'good', 'JJ'), ('student', 'student', 'NN'), ('in', 'in', 'IN'), ('our', 'our', 'PRP$'), ('school', 'school', 'NN'), ('.', '.', '.'), ('We', 'We', 'PRP'), ('continued', 'continue', 'VBD'), ('the', 'the', 'DT'), ('middle', 'middle', 'NN'), ('and', 'and', 'CC'), ('high', 'high', 'JJ'), ('school', 'school', 'NN'), ('to', 'to', 'TO'), ('gather', 'gather', 'VB'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('same', 'same', 'JJ'), ('school', 'school', 'NN'), ('.', '.', '.'), ('We', 'We', 'PRP'), ('were', 'be', 'VBD'), ('studying', 'study', 'VBG'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('different', 'different', 'JJ'), ('classes', 'class', 'NNS'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('middle', 'middle', 'JJ'), ('school', 'school', 'NN'), (';', ';', ':'), ('however', 'however', 'RB'), (',', ',', ','), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('high', 'high', 'JJ'), ('school', 'school', 'NN'), ('we', 'we', 'PRP'), ('were', 'be', 'VBD'), ('studying', 'study', 'VBG'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('same', 'same', 'JJ'), ('class', 'class', 'NN'), ('.', '.', '.'), ('We', 'We', 'PRP'), ('went', 'go', 'VBD'), ('to', 'to', 'TO'), ('many', 'many', 'JJ'), ('places', 'place', 'NNS'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('free', 'free', 'JJ'), ('time', 'time', 'NN'), ('while', 'while', 'IN'), ('we', 'we', 'PRP'), ('were', 'be', 'VBD'), ('studying', 'study', 'VBG'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('high', 'high', 'JJ'), ('school', 'school', 'NN'), ('.', '.', '.'), ('When', 'when', 'WRB'), ('we', 'we', 'PRP'), ('finished', 'finish', 'VBD'), ('from', 'from', 'IN'), ('the', 'the', 'DT'), ('high', 'high', 'JJ'), ('school', 'school', 'NN'), (',', ',', ','), ('I', 'I', 'PRP'), ('went', 'go', 'VBD'), ('to', 'to', 'TO'), ('K', 'K', 'NNP'), ('.', '.', '.'), ('S', 'S', 'NNP'), ('University', 'University', 'NNP'), ('and', 'and', 'CC'), ('he', 'he', 'PRP'), ('went', 'go', 'VBD'), ('to', 'to', 'TO'), ('I', 'I', 'PRP'), ('.', '.', '.'), ('M', 'M', 'NNP'), ('University', 'University', 'NNP'), ('.', '.', '.'), ('While', 'while', 'IN'), ('we', 'we', 'PRP'), ('were', 'be', 'VBD'), ('enjoying', 'enjoy', 'VBG'), ('in', 'in', 'IN'), ('academic', 'academic', 'JJ'), ('life', 'life', 'NN'), (',', ',', ','), ('we', 'we', 'PRP'), ('made', 'make', 'VBD'), ('many', 'many', 'JJ'), ('achievement', 'achievement', 'NN'), ('in', 'in', 'IN'), ('these', 'these', 'DT'), ('universities', 'university', 'NNS'), ('.', '.', '.'), ('I', 'I', 'PRP'), ('graduated', 'graduate', 'VBD'), ('when', 'when', 'WRB'), ('Nife', 'Nife', 'NNP'), ('was', 'be', 'VBD'), ('studying', 'study', 'VBG'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('last', 'last', 'JJ'), ('semester', 'semester', 'NN'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('university', 'university', 'NN'), ('.', '.', '.'), ('After', 'after', 'IN'), ('that', 'that', 'DT'), (',', ',', ','), ('I', 'I', 'PRP'), ('got', 'get', 'VBD'), ('a', 'a', 'DT'), ('job', 'job', 'NN'), ('.', '.', '.'), ('Fortunately', 'fortunately', 'RB'), (',', ',', ','), ('it', 'it', 'PRP'), ('was', 'be', 'VBD'), ('nearby', 'nearby', 'JJ'), ('my', 'my', 'PRP$'), ('home', 'home', 'NN'), ('.', '.', '.'), ('I', 'I', 'PRP'), ('worked', 'work', 'VBD'), ('two', 'two', 'CD'), ('years', 'year', 'NNS'), ('then', 'then', 'RB'), ('I', 'I', 'PRP'), ('got', 'get', 'VBD'), ('scholarship', 'scholarship', 'NN'), ('from', 'from', 'IN'), ('ministry', 'ministry', 'NN'), ('of', 'of', 'IN'), ('high', 'high', 'JJ'), ('education', 'education', 'NN'), ('in', 'in', 'IN'), ('my', 'my', 'PRP$'), ('country', 'country', 'NN'), ('.', '.', '.'), ('When', 'when', 'WRB'), ('I', 'I', 'PRP'), ('came', 'come', 'VBD'), ('here', 'here', 'RB'), ('to', 'to', 'TO'), ('U.S', 'U.S', 'NNP'), (',', ',', ','), ('my', 'my', 'PRP$'), ('friend', 'friend', 'NN'), ('Nife', 'Nife', 'NNP'), ('arrange', 'arrange', 'VB'), ('some', 'some', 'DT'), ('documents', 'document', 'NNS'), ('to', 'to', 'TO'), ('study', 'study', 'VB'), ('at', 'at', 'IN'), ('grad', 'grad', 'JJ'), ('school', 'school', 'NN'), ('in', 'in', 'IN'), ('Malaysia', 'Malaysia', 'NNP'), ('.', '.', '.')]
    ## 2                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             [('Ten', 'ten', 'CD'), ('years', 'year', 'NNS'), ('ago', 'ago', 'RB'), (',', ',', ','), ('I', 'I', 'PRP'), ('met', 'meet', 'VBD'), ('a', 'a', 'DT'), ('women', 'woman', 'NNS'), ('on', 'on', 'IN'), ('the', 'the', 'DT'), ('train', 'train', 'NN'), ('between', 'between', 'IN'), ('the', 'the', 'DT'), ('way', 'way', 'NN'), ('was', 'be', 'VBD'), ('going', 'go', 'VBG'), ('to', 'to', 'TO'), ('school', 'school', 'NN'), (',', ',', ','), ('she', 'she', 'PRP'), ('was', 'be', 'VBD'), ('sitting', 'sit', 'VBG'), ('next', 'next', 'JJ'), ('me', 'me', 'PRP'), ('.', '.', '.'), ('That', 'that', 'DT'), ('day', 'day', 'NN'), ('for', 'for', 'IN'), ('school', 'school', 'NN'), ('opened', 'open', 'VBD'), (';', ';', ':'), ('She', 'She', 'PRP'), ('had', 'have', 'VBD'), ('a', 'a', 'DT'), ('long', 'long', 'JJ'), ('back', 'back', 'RB'), ('hair', 'hair', 'NN'), (',', ',', ','), ('a', 'a', 'DT'), ('tan', 'tan', 'JJ'), ('skin', 'skin', 'NN'), ('and', 'and', 'CC'), ('a', 'a', 'DT'), ('big', 'big', 'JJ'), ('brow', 'brow', 'NN'), ('eyes', 'eye', 'NNS'), ('.', '.', '.'), ('I', 'I', 'PRP'), ('thought', 'think', 'VBD'), (',', ',', ','), ('she', 'she', 'PRP'), ('was', 'be', 'VBD'), ('so', 'so', 'RB'), ('beautiful', 'beautiful', 'JJ'), (',', ',', ','), ('and', 'and', 'CC'), ('one', 'one', 'CD'), ('thing', 'thing', 'NN'), ('as', 'as', 'IN'), ('important', 'important', 'JJ'), ('she', 'she', 'PRP'), ('wear', 'wear', 'JJ'), ('uniform', 'uniform', 'JJ'), ('similar', 'similar', 'JJ'), ('me', 'me', 'PRP'), ('.', '.', '.'), ('When', 'when', 'WRB'), ('she', 'she', 'PRP'), ('took', 'take', 'VBD'), ('off', 'off', 'RP'), ('the', 'the', 'DT'), ('train', 'train', 'NN'), (',', ',', ','), ('she', 'she', 'PRP'), ('said', 'say', 'VBD'), ('good', 'good', 'JJ'), ('bye', 'bye', 'NN'), ('with', 'with', 'IN'), ('me', 'me', 'PRP'), ('.', '.', '.'), ('I', 'I', 'PRP'), ('feel', 'feel', 'VBP'), ('better', 'better', 'RB'), ('for', 'for', 'IN'), ('first', 'first', 'JJ'), ('day', 'day', 'NN'), ('of', 'of', 'IN'), ('school', 'school', 'NN'), ('.', '.', '.'), ('I', 'I', 'PRP'), ('was', 'be', 'VBD'), ('sitting', 'sit', 'VBG'), ('until', 'until', 'IN'), ('the', 'the', 'DT'), ('last', 'last', 'JJ'), ('train', 'train', 'NN'), ('stop', 'stop', 'VBD'), ('it', 'it', 'PRP'), ('was', 'be', 'VBD'), ('near', 'near', 'IN'), ('my', 'my', 'PRP$'), ('school', 'school', 'NN'), ('.', '.', '.'), ('I', 'I', 'PRP'), ('was', 'be', 'VBD'), ('finding', 'find', 'VBG'), ('the', 'the', 'DT'), ('class', 'class', 'NN'), ('when', 'when', 'WRB'), ('I', 'I', 'PRP'), ('met', 'meet', 'VBD'), ('the', 'the', 'DT'), ('women', 'woman', 'NNS'), ('agin', 'agin', 'VBP'), ('in', 'in', 'IN'), ('that', 'that', 'DT'), ('room', 'room', 'NN'), ('.', '.', '.'), ('I', 'I', 'PRP'), ('saw', 'see', 'VBD'), ('the', 'the', 'DT'), ('number', 'number', 'NN'), ('of', 'of', 'IN'), ('the', 'the', 'DT'), ('room', 'room', 'NN'), ('that', 'that', 'WDT'), ('was', 'be', 'VBD'), ('same', 'same', 'JJ'), ('me', 'me', 'PRP'), ('.', '.', '.'), ('While', 'while', 'IN'), ('I', 'I', 'PRP'), ('was', 'be', 'VBD'), ('introducing', 'introduce', 'VBG'), ('my', 'my', 'PRP$'), ('self', 'self', 'NN'), ('to', 'to', 'TO'), ('the', 'the', 'DT'), ('women', 'woman', 'NNS'), (',', ',', ','), (',', ',', ','), ('the', 'the', 'DT'), ('teacher', 'teacher', 'NN'), ('came', 'come', 'VBD'), ('in', 'in', 'IN'), ('.', '.', '.'), ('Since', 'since', 'IN'), ('we', 'we', 'PRP'), ('were', 'be', 'VBD'), ('the', 'the', 'DT'), ('best', 'good', 'JJS'), ('friends', 'friend', 'NNS'), ('until', 'until', 'IN'), ('finished', 'finish', 'VBN'), ('university', 'university', 'NN'), ('.', '.', '.')]
    ## 3                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      [('In', 'in', 'IN'), ('my', 'my', 'PRP$'), ('country', 'country', 'NN'), ('we', 'we', 'PRP'), ('usually', 'usually', 'RB'), ('do', 'do', 'VBP'), ("n't", 'not', 'RB'), ('use', 'use', 'VB'), ('tea', 'tea', 'NN'), ('bags', 'bag', 'NNS'), ('.', '.', '.'), ('First', 'First', 'NNP'), ('we', 'we', 'PRP'), ('boil', 'boil', 'VBP'), ('the', 'the', 'DT'), ('water', 'water', 'NN'), (',', ',', ','), ('then', 'then', 'RB'), ('we', 'we', 'PRP'), ('put', 'put', 'VBD'), ('some', 'some', 'DT'), ('of', 'of', 'IN'), ('the', 'the', 'DT'), ('hot', 'hot', 'JJ'), ('water', 'water', 'NN'), ('at', 'at', 'IN'), ('the', 'the', 'DT'), ('top', 'top', 'JJ'), ('.', '.', '.'), ('We', 'We', 'PRP'), ('add', 'add', 'VBP'), ('the', 'the', 'DT'), ('tea', 'tea', 'NN'), ('at', 'at', 'IN'), ('the', 'the', 'DT'), ('top', 'top', 'JJ'), ('.', '.', '.'), ('We', 'We', 'PRP'), ('wait', 'wait', 'VBP'), ('like', 'like', 'IN'), ('20', '20', 'CD'), ('minutes', 'minute', 'NNS'), ('with', 'with', 'IN'), ('low', 'low', 'JJ'), ('heat', 'heat', 'NN'), ('for', 'for', 'IN'), ('it', 'it', 'PRP'), ('to', 'to', 'TO'), ('be', 'be', 'VB'), ('ready', 'ready', 'JJ'), ('.', '.', '.'), ('At', 'at', 'IN'), ('last', 'last', 'JJ'), (',', ',', ','), ('we', 'we', 'PRP'), ('first', 'first', 'RB'), ('poor', 'poor', 'JJ'), ('the', 'the', 'DT'), ('water', 'water', 'NN'), ('with', 'with', 'IN'), ('tea', 'tea', 'NN'), (',', ',', ','), ('then', 'then', 'RB'), ('we', 'we', 'PRP'), ('add', 'add', 'VBP'), ('some', 'some', 'DT'), ('pure', 'pure', 'JJ'), ('hot', 'hot', 'JJ'), ('water', 'water', 'NN'), ('in', 'in', 'IN'), ('it', 'it', 'PRP'), ('.', '.', '.')]
    ## 4                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        [('I', 'I', 'PRP'), ('organized', 'organize', 'VBD'), ('the', 'the', 'DT'), ('instructions', 'instruction', 'NNS'), ('by', 'by', 'IN'), ('time', 'time', 'NN'), ('.', '.', '.')]
    ## 5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            [('First', 'first', 'RB'), (',', ',', ','), ('prepare', 'prepare', 'VB'), ('a', 'a', 'DT'), ('port', 'port', 'NN'), (',', ',', ','), ('loose', 'loose', 'JJ'), ('tea', 'tea', 'NN'), (',', ',', ','), ('and', 'and', 'CC'), ('cup', 'cup', 'NN'), ('.', '.', '.'), ('Second', 'Second', 'NNP'), (',', ',', ','), ('boil', 'boil', 'NN'), ('water', 'water', 'NN'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('port', 'port', 'NN'), ('.', '.', '.'), ('Third', 'Third', 'NNP'), (',', ',', ','), ('put', 'put', 'VBD'), ('loose', 'loose', 'JJ'), ('tea', 'tea', 'NN'), ('in', 'in', 'IN'), ('the', 'the', 'DT'), ('cup', 'cup', 'NN'), ('.', '.', '.'), ('Fourth', 'Fourth', 'NNP'), (',', ',', ','), ('pour', 'pour', 'JJ'), ('water', 'water', 'NN'), ('into', 'into', 'IN'), ('the', 'the', 'DT'), ('cup', 'cup', 'NN'), ('.', '.', '.'), ('Finally', 'finally', 'RB'), (',', ',', ','), ('after', 'after', 'IN'), ('waiting', 'wait', 'VBG'), ('for', 'for', 'IN'), ('a', 'a', 'DT'), ('few', 'few', 'JJ'), ('minutes', 'minute', 'NNS'), (',', ',', ','), ('drink', 'drink', 'VBP'), ('the', 'the', 'DT'), ('tea', 'tea', 'NN'), ('.', '.', '.'), ('Loose', 'loose', 'JJ'), ('tea', 'tea', 'NN'), ('wo', 'will', 'MD'), ("n't", 'not', 'RB'), ('float', 'float', 'VB'), ('on', 'on', 'IN'), ('the', 'the', 'DT'), ('surface', 'surface', 'NN'), ('of', 'of', 'IN'), ('the', 'the', 'DT'), ('tea', 'tea', 'NN'), (',', ',', ','), ('so', 'so', 'IN'), ('you', 'you', 'PRP'), ('do', 'do', 'VBP'), ("n't", 'not', 'RB'), ('have', 'have', 'VB'), ('to', 'to', 'TO'), ('pick', 'pick', 'VB'), ('the', 'the', 'DT'), ('loose', 'loose', 'JJ'), ('tea', 'tea', 'NN'), ('up', 'up', 'RB'), ('.', '.', '.')]
    ## 6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            [('By', 'by', 'IN'), ('time', 'time', 'NN')]

The data contains about 46,000 entries, and a vast about of background
data on the students. Of note will be L1, level, and scores on
proficiency measures.

### Read in Student Indo

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

## Tokenize Data

### Tokenize Lemmatized Data Helper Function

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
   #make the df longer and unndest
   unnest(tokenized_nested_df)

  # un-comment if you want to save
  #tokenized_df_unnested %>% write.csv('large_data/tokenized_df_unnested.csv')
}
```

    ## [1] "Reading in File from directory"

## Summary of Data

### Summary of ‘will’ tokens

``` r
tokenized_df_unnested %>% 
  filter(trimws(lemma) == "will" & trimws(token) != 'will' ) %>% 
  count(token, lemma)
```

    ##    token lemma    n
    ## 1   "ll"  will 1157
    ## 2   WILL  will   13
    ## 3     WO  will    2
    ## 4   Will  will   64
    ## 5 willed  will    4
    ## 6     wo  will  465

### Summary of ‘going’ tokens

``` r
tokenized_df_unnested %>% 
  filter(trimws(lemma) == "go" & trimws(token) != 'going' ) %>% 
  count(token)
```

    ##    token    n
    ## 1     GO    7
    ## 2  GOING    3
    ## 3   GONE    2
    ## 4     Go   37
    ## 5  Going   43
    ## 6     go 6879
    ## 7   goes  417
    ## 8    gon    7
    ## 9   gone  395
    ## 10  went 2554

Note that not all of these are of the ‘going to’ future construction

## Format the Data

``` r
#Create counts_data, each row is a token
counts_data =  tokenized_df_unnested %>% 
  #if there is a 'will' token that is a modal, put 1 in new column, else 0
  mutate(is_will_construction = if_else(trimws(lemma) == 'will' & trimws(POS) == 'MD', 1,0)) %>% 
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

### Bring in score data

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

### Save The data

``` r
countruction_counts_and_student_info_with_scores %>% 
  write.csv(paste0(dirname(getwd()),'/Data Files/FINAL_DATA_countruction_counts_and_student_info_with_scores.csv'))
```

My strategy for importing the data is to find construction in question
in the text, and then to join in the student demographic information

``` r
#In order to perform analysis on tokens later
filtered_tokenized_data = tokenized_df_unnested %>% 
  #if there is a 'will' token that is a modal, put 1 in new column, else 0
  mutate(is_will_construction = if_else(trimws(lemma) == 'will' & trimws(POS) == 'MD', 1,0)) %>% 
  #if there is a 'going' token followed by 'to' then a verb within 2 words, put 1 in new column
  mutate(is_goingTo_construction = 
            if_else(
              token == "going" & lead(token) == "to" & (
                #lead allows to 'look ahead'
                startsWith(trimws(lead(POS, 2)), 'V') | 
                startsWith(trimws(lead(POS, 3)), 'V')
              ),1,0)) %>% 
  filter(is_will_construction | is_goingTo_construction)

filtered_tokenized_data %>% write.csv(paste0(dirname(getwd()),'/Data Files/filtered_tokenized_data.csv'))
```

## Session Info

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
