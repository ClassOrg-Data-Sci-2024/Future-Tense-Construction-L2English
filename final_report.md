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

In the corpus, 18,490 instances of the future tense construction were found: `will` construction count was 19,762 (90.7%) and `going to` construction count was 1,728 (9.3%). The vast majority (98.75%) of verbs that follow `will` are of the base form, as are (98.62%) of verbs following the `going to` construction. This suggests that the context of the sentence, as of the current part-of-speech tagging scheme, will be unlikely to hold explanatory power.


A correlation across groups of students was not deemed prudent. There appeared to be now qualitative correlation found, with any of the proficiency scores. Such an anlayis would also become statistally problematic if predictions of the  *proportion* of times a student would use one constuction or the other suggested values outside of 0.0 to 1.0. So, we turn to the mixed methods approach discussed previously.  

![Figure 1. Each Proficency Score and Percentage of `will` Construction](/images/DataAnalysis-corrAn-1.png)




Conducting a random effects model, with random slopes and random intercepts that predicts whether a instance of future tense construction will use the `will` or `going to` variant (with `will` being a value of 1 and `going to` a value of 0), proficiency is found to be a significant (`t = 2.871`) predictor of construction preference.

Further, Figure 1 shows that the random intercepts found by the model vary in distribution significantly (p = 0.00829), as do the slopes (p \< 0.001) found by the model, shown in Figure 2.

![Figure 1. Boxplot of Intercepts predicted By Proficiency grouped by Level - Random Intercepts and Random Slopes Model](/images/DataAnalysis-randIntRandSlopeLevelBoxplotOfInts-1.png)

![Figure 2. Boxplot of Slopes predicted By Proficiency grouped by Level - Random Intercepts and Random Slopes Model](/images/DataAnalysis-randIntRandSlopeLevelBoxplotOfSlopes-1.png)

A similar analysis was conducted on the effects of the level of the students. The level of the course the student was in was also found to be a significant (t = 2.791) predictor of preference for construction. The resulting Intercepts and slopes are shown in Figure 3 and Figure 4 respectively. 

![Figure 3. Boxplot of Intercepts predicted By Level grouped by Level - Random Intercepts and Random Slopes Model](/images/DataAnalysis-randIntSloLevelBoxplot-1.png)

![Figure 4. Boxplot of Slopes predicted By Level grouped by Level - Random Intercepts and Random Slopes Model](/images/DataAnalysis-randIntSloLevelBoxplotSlope-1.png)

## Discussion

## Conclusion

### Debrief

## References

Bardovi-Harlig, K. (2005). Proceedings of the 7th Generative Approaches to Second Language Acquisition Conference (GASLA 2004), ed. Laurent Dekydtspotter et al., 1-12. Somerville, MA: Cascadilla Proceedings Project.

Berglund, Y. (2005). Expressions of Future in Present-day English: A Corpus-based Approach (PhD dissertation, Acta Universitatis Upsaliensis). Retrieved from <https://urn.kb.se/resolve?urn=urn:nbn:se:uu:diva-5794>

Drager, K & Hay, J. (2012). Exploiting random intercepts: Two case studies in sociophonetics. Language Variation and Change. 24. 10.1017/S0954394512000014.

Huddleston, R. (1995). The case against a future tense in English. Studies in Language 19 (2) 399-446. <https://doi.org/10.1075/sl.19.2.04hud>

Marcus, M P., et al. (1999). Treebank-3 LDC99T42. Web Download. Philadelphia: Linguistic Data Consortium
