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

Proficiency was measured as the mean of the following scores z-scored normalized: - `LCT_Score`: an ELI (English Language Institute) listening test - `Writing_Sample`: an ELI (English Language Institute) writing test - `MTELP_Conv_Score`: combined score from the Michigan Test of English Language Proficiency.

(Raw scores were provided in the data set.)

Each occurrence of a `will` or `going to` construction was treated as an individual observation. Then, considering each student as a 'group' whose members are the tokens of future construction, a mixed effects model was fit. This method selection is in accordance with research on language variation and change, which suggests that meaningful insights are contained in random intercepts of random effects models (Drager & Hay 2012).

## Results

## Discussion

## Conclusion

### Debrief

## References

Bardovi-Harlig, K. (2005). Proceedings of the 7th Generative Approaches to Second Language Acquisition Conference (GASLA 2004), ed. Laurent Dekydtspotter et al., 1-12. Somerville, MA: Cascadilla Proceedings Project.

Berglund, Y. (2005). Expressions of Future in Present-day English: A Corpus-based Approach (PhD dissertation, Acta Universitatis Upsaliensis). Retrieved from <https://urn.kb.se/resolve?urn=urn:nbn:se:uu:diva-5794>

Drager, K & Hay, J. (2012). Exploiting random intercepts: Two case studies in sociophonetics. Language Variation and Change. 24. 10.1017/S0954394512000014.

Huddleston, R. (1995). The case against a future tense in English. Studies in Language 19 (2) 399-446. <https://doi.org/10.1075/sl.19.2.04hud>

Marcus, M P., et al. (1999). Treebank-3 LDC99T42. Web Download. Philadelphia: Linguistic Data Consortium
