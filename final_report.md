Daniel Crawford

24 April 2024

LING2020 - Data Science for Research in Linguistics

Final Report - Future Tense Construction Preference in L2 English Learners.pdf

## Introduction

The construction of the future tense in English is a curious topic. Indeed, there have been calls for re-analyzing English future tense (Huddleston 1995). Regardless, expressing occurrences that will happen in the future remains integral to a speaker's linguistic capability. To this end, when teaching English as a Second Language, it is essential to construct a curriculum that will allow for the future tense to be expressed by those learning English. This project is a corpus-based analysis of the effects of the speaker's proficiency on their preference for how the future tense is constructed.

## Background

### Literature Review

In a review of the construction of the future tense in English, Berglund (2005) finds that there are five ways of constructing the future tense in Modern English:

-   `will`: "I will walk tomorrow."
-   `'ll` : "I'll walk tomorrow."
-   `shall`: "I shall walk tomorrow."
-   `going to`: "I am going to walk tomorrow."
-   `gonna`: "I am gonna walk tomorrow."

Note that these constructions directly indicate that the verb will take place in the future, so this will not investigate constructions like a plan: "I plan to walk tomorrow." Research has been conducted in this area, finding that learners of English were over five times more likely to use the `will` construction than the `going to` construction. This is an interesting result because it is also found that the will construction emerges earlier than the going to construction. This project seeks to address these questions and analyze the role of proficiency in students' preference for one construction or the other.

### Data

The data utilized to suggest an answer to this question comes from the University of Pittsburgh English Language Institute Corpus (PELIC). This corpus is a collection of written responses from 1177 students enrolled in the University of Pittsburgh's Intensive English Program from 2005 to 2012 in an English for Academic Purposes Context. The data comprises 46,230 texts and 4,250,703 tokens. Each text has been tokenized and lemmatized, according to the Penn Tree Bank (Marcus, 1999). This was done with Python's NLTK library, as was Part-of-Speech tagging, utilizing the Penn Treebank POS tagset.

### Methods

The goal of this project was to understand the role of proficiency in future tense construction preference. The methods selected captured the effects of proficiency score on whether a speaker would use will or going to in a particular token. That is, a token-level analysis was conducted.

Proficiency was measured as the mean of the following scores z-scored normalized:

-   `LCT_Score`: an ELI (English Language Institute) listening test
-   `Writing_Sample`: an ELI (English Language Institute) writing test
-   `MTELP_Conv_Score`: combined score from the Michigan Test of English Language Proficiency.

(Raw scores were provided in the data set.) Proficisency was also operationalized by the level of course the student was in:

-   `2`: Pre-Intermediate
-   `3`: Intermediate
-   `4`: Upper Intermediate
-   `5`: Advanced

For the models used, the number of the course will be treated as a real number value, minus 2: 2 \> 0, 3 \> 1, etc. This allows for a mixed effects model to be used, and then the coefficient of the level can be thought of as corresponding changes due to one increase in level. s

Each occurrence of a `will` or `going to` construction was treated as an individual observation. Then, considering each student as a 'group' whose members are the tokens of future construction, a mixed effects model was fit. This method selection is in accordance with research on language variation and change, which suggests that meaningful insights are contained in random intercepts of random effects models (Drager & Hay 2012).

Students who took more than one set of proficiency (3%) exams were excluded from the main analysis, and had a seperate longitudinal analysis conducted on their data points.

## Results

In the corpus, 18,490 instances of the future tense construction were found: `will` construction count was 19,762 (90.7%) and `going to` construction count was 1,728 (9.3%). The vast majority (98.75%) of verbs that follow `will` are of the base form, as are (98.62%) of verbs following the `going to` construction. This suggests that the context of the sentence, as of the current part-of-speech tagging scheme, will be unlikely to hold explanatory power. There were 23 stunts in Level 2, 394 in Level 3, 709 in Level 4, and 430 in Level 3; this imbalance is addressed.

A correlation across groups of students was not deemed prudent. There appeared to be now qualitative correlation found, with any of the proficiency scores. Such an anlayis would also become statistically problematic if predictions of the *proportion* of times a student would use one constuction or the other suggested values outside of 0.0 to 1.0. So, we turn to the mixed methods approach discussed previously.

![Figure 1. Each Proficency Score and Percentage of `will` Construction](/images/DataAnalysis-corrAn-1.png)

Conducting a random effects model, with random slopes and random intercepts that predicts whether a instance of future tense construction will use the `will` or `going to` variant (with `will` being a value of 1 and `going to` a value of 0), proficiency is found to be a significant (t = 2.871, F = 8.2417) predictor of construction preference.

Further, Figure 2 shows that the random intercepts found by the model vary in distribution significantly (p = 0.00829), as do the slopes (p \< 0.001) found by the model, shown in Figure 4.

![Figure 2. Boxplot of Intercepts predicted By Proficiency grouped by Level - Random Intercepts and Random Slopes Model](/images/DataAnalysis-randIntRandSlopeLevelBoxplotOfInts-1.png)

![Figure 3. Boxplot of Slopes predicted By Proficiency grouped by Level - Random Intercepts and Random Slopes Model](/images/DataAnalysis-randIntRandSlopeLevelBoxplotOfSlopes-1.png)

Because there was steep imbalance in the number of students in level 2 the same model was run excluding these students. This model (t = 2.381, F = 5.6702) also found that slope was a significant predictor, when comparing levels 3 and higher (p = 0.002), but Intercepts were not found to vary significantly by level (p = 0.20), in the model that used proficiency to predict future tense construction.

A similar analysis was conducted on the effects of the level of the students. The course level the student was in was also found to be a significant (t = 2.791, F = 7.791) predictor of preference for construction. The resulting Intercepts and slopes are shown in Figure 4 and Figure 5, respectively. When Level 2 students were removed from the model, the results hold (t = 2.339, F = 5.4712), that Level is a significant predictor

![Figure 4. Boxplot of Intercepts predicted By Level grouped by Level - Random Intercepts and Random Slopes Model](/images/DataAnalysis-randIntSloLevelBoxplot-1.png)

![Figure 5. Boxplot of Slopes predicted By Level grouped by Level - Random Intercepts and Random Slopes Model](/images/DataAnalysis-randIntSloLevelBoxplotSlope-1.png)

## Discussion

The models suggest a meaningful difference in what construction will be used based on the proficiency metric utilized (the mean of three independent tests.) With an increase in the intercepts, which can be interpreted as the base probability that the `will` construction will be used, it seems that the `will` construction will increase over time. Furthermore, because the model utilized random slopes, the level at which the students were associated with the proficiency exam seems to influence their rate of increase in the wall construction. However, after the pre-intermediate, there does not seem to be significant variation in *Intercepts* that can be attributed to Level. This suggests a learning period for students to use either construction. The slope, which captures the effect of proficiency, still varies significantly by level.

Naturally, students' proficiency will correspond to their placement in a course level. This is confirmed by a second set of models, indicating that Level is a significant predictor of the `will` construction use. This suggests that the level of instruction plays a major role in the construction the students will utilize.

## Conclusion

This project is a corpus-based investigation of the role of English learners' proficiency in selecting between the `will` construction and `going to` construction when formulating the future tense. These results suggest that there is indeed a significant effect of both proficiency and course level on not only if the students utilize one of the constructions but the rate and default probability varies by level. These results have implications for curricula designed to teach English, as they show that utilizing the `will` construction correlates with higher proficiency scores.

Future directions for this research naturally include extending the scope of the analysis to include lexical future construction (Bardovi-Harlig, 2005). This includes other ways of indicating that an event will occur in the future (such as `want`). This offers more options to learners, which may be selected for. Note that this data was collected from an academic context, so further studies would include spoken and more casual speech. Investigations as to the level at which students are taught the future tense could hold important explanatory power. This would be a curriculum analysis approach. Finally, the prompt that was given to the students for them to respond to may affect the call for using the future tense. While this investigation seeks to minimize the effects by investigating individual tokens, an in-depth, review of the questions asked would be an important research question.

### Debrief

This project was part of LING2020 - Data Science for Research in Linguistics, at the University of Pittsburgh. The research could be described as incremental. Pulling in the data and working it mostly into a helpful format was straightforward. However, there was a significant issue when pulling in the tokenized, lemmatized, and part-of-speech tagged data. Because this was saved just as a string, there was no structure, which made interpretation difficult. Once the data was massaged into something useful, the main difficulty was investing all the different possible correlations that could exist. Starting with proficiency, many models were tested and run, about five times what is actually reported (see the scratchpad for just some of these.) Further, utilizing the correct statistical tools to answer the questions was a major learning experience, and I familiarized myself with a new set of analytic tools.

## References

Bardovi-Harlig, K. (2005). Proceedings of the 7th Generative Approaches to Second Language Acquisition Conference (GASLA 2004), ed. Laurent Dekydtspotter et al., 1-12. Somerville, MA: Cascadilla Proceedings Project.

Berglund, Y. (2005). Expressions of Future in Present-day English: A Corpus-based Approach (PhD dissertation, Acta Universitatis Upsaliensis). Retrieved from <https://urn.kb.se/resolve?urn=urn:nbn:se:uu:diva-5794>

Drager, K & Hay, J. (2012). Exploiting random intercepts: Two case studies in sociophonetics. Language Variation and Change. 24. 10.1017/S0954394512000014.

Huddleston, R. (1995). The case against a future tense in English. Studies in Language 19 (2) 399-446. <https://doi.org/10.1075/sl.19.2.04hud>

Marcus, M P., et al. (1999). Treebank-3 LDC99T42. Web Download. Philadelphia: Linguistic Data Consortium
