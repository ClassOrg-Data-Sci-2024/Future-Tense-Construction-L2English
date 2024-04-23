# Correlation of Future Tense Construction Preference with Proficiency Scores for English L2 Learners.

Daniel Crawford

daniel.crawford [(a)] pitt.edu

24 April 2024

## Future-Tense-Construction-L2English

When forming the future tense in English, multiple constructions can be utilized to construct future tense. These two constructions are `will` (e.g. "I will walk") and `going to` (e.g. "I am going to walk"). This project analyzes what affects how English L2 learners construct future tense. With demographic and proficiency data, this project looks for trends and correlations in construction preferences (Which of these two will learners opt for?)

The data used in this project is from the University of Pittsburgh English Language Institute Corpus ([PELIC](https://eli-data-mining-group.github.io/Pitt-ELI-Corpus/)). This is a corpus of written responses collected from an English Intensive program. The dates of collection range form 2005-2012. All data for this project was gathered from [PELIC's GitHub Data Repository](https://github.com/ELI-Data-Mining-Group/PELIC-dataset). The corpus consists of data from 1177 students, creating 46,230 tests, with 4,250,703 tokens. Notably, the texts were all tokenized and lemmatized. (39,623 word types and 39,307 lemma types)

**Corpus citation**:

Juffs, A., Han, N-R., & Naismith, B. (2020). The University of Pittsburgh English Language Corpus (PELIC) [Data set]. <http://doi.org/10.5281/zenodo.3991977>

## Project Directory Navigation

-   [**final_report.md**](/final_report.md): The final report for this project, including background information, results, and discussion
-   [Data Processing](/Data Processing): Sub-directory to hold main data processing files
    -   [DataProcessing.Rmd](/Data Processing/DataProcessing.Rmd): Data Processing Pipeline. ([Knitted](/Data Processing/DataProcessing.md))
-   [Data Analysis](/Data Analysis): Sub-directory to hold main data analysis files
    -   [DataAnalysis.Rmd](/Data Analysis/DataAnalysis.Rmd): Data Analyses: Correlation, Mixed Effects Models, Random Intercept Models ([Knitted](/Data Analysis/DataAnalysis.md))
-   [Data Files](/Data Files): Sub-directory to hold **small** data files.   
    -   [filtered_tokenized_data.csv](/Data Files/filtered_tokenized_data.csv): Tokenized data, filtered for `will` or `going to` construction, created [here](/blob/main/Data%20Processing/DataProcessing.md#save-the-data)
    -   [FINAL_DATA_countruction_counts_and_student_info_with_scores.csv](/Data Files/FINAL_DATA_countruction_counts_and_student_info_with_scores.csv): Final data with counts of each construction, created [here](/blob/main/Data%20Processing/DataProcessing.md#save-the-data)
-   [scratchpads](/scratchpads): Folder to hold files with extra bits of code
    -   [scratchpad.Rmd](/scratchpads/scratchpad.Rmd): Some extra (analytics) code
-   [progress_report.md](/progress_report.md): Three progress updates as work was completed
-   [project_plan.md](/project_plan.md): Original Project plan, with some background information. 
-   [README.md](/README.md): This file, for orientation and direction
-   [LICENSE.md](/LICENSE.md): Creative Commons License (inherited form [PELIC's Liscense](https://github.com/ELI-Data-Mining-Group/PELIC-dataset?tab=readme-ov-file#11-license))
-   [.gitignore](/.gitignore): Standard R .gitgnore with addition of `large_data/*` (See Below)


