# Project Plan

When learning English as a second language, speakers have options for how they will choose to construct the future tense: the *going to* and *will*: `I am going to walk,` and `I will walk`. Multiple factors can influence this selection: L1 future constructions, awareness, English L2 education curriculum, and experiences outside of the classroom all play a part.

The aim of this project will be to utilize the University of Pittsburgh's Pitt English Language Institute Corpus ([PELIC](https://eli-data-mining-group.github.io/Pitt-ELI-Corpus/)) to investigate the correlation between future tense construction preference (FTCP) and proficiency level. Some of the factors mentioned will be able to be taken into account to. The goal is then to determine:

1.  Is there a correlation between FTCP and Proficiency, based on proficiency scores
2.  What factors, if any, may influence FTCP?

To get there, I am planning on the following stages:

1.  **Initialize Access to PELIC**: This is the step where I will read over and begin to harness [PELIC data](https://github.com/ELI-Data-Mining-Group/PELIC-dataset).
2.  **Isolate the competing constructions**: using regex, count the occurrences of `"going to \w"` and `"will \w"`. I may need to revisit the exact regex and test that this captures the desired constructions.
3.  **Aggregate the data by factors**: Using grouping on things like grade level, age, gender, L1, I will get comparative results for FTCP.
4.  **Analyze the results**: If I have more discrete factors, then something like decision trees may be helpful. Otherwise, regression/fitting a linear model may be helpful in predicting efficiency. If there is a natural way to classify proficiency discretely, then perhaps a clustering algorithm will be helpful here as well.

I am planning on using `tidyverse` (naturally), and `purr`.
