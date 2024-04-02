# Progress Reports

## Progress Report 1

So far I have been working with the [PELIC data](https://eli-data-mining-group.github.io/Pitt-ELI-Corpus/). I have been scratching some scripts to get the data. Because the data is in a mainly relational format, this will be a crucial part of the project. Met with Dan to iron out details.

I have been able to leverage that the [data](https://github.com/ELI-Data-Mining-Group/PELIC-dataset) is already in a somewhat workable format. Additionally, because the data is open source, I am able to [read it in](https://github.com/ClassOrg-Data-Sci-2024/Future-Tense-Construction-L2English/blob/main/FutureTenseConstructionL2English.md#load-data) from the online source. This means I can reduce the amount of space on my machine **and** it makes the code more easily transferable.

A key component of my project is the [regex creation](https://github.com/ClassOrg-Data-Sci-2024/Future-Tense-Construction-L2English/blob/main/FutureTenseConstructionL2English.md#set-the-regex---these-will-likely-change). While I may need to make it much more robust, for now, I have simple placeholders that I can build the rest of the pipeline around.

I have a [pipeline](https://github.com/ClassOrg-Data-Sci-2024/Future-Tense-Construction-L2English/blob/main/FutureTenseConstructionL2English.md#set-the-regex---these-will-likely-change) that searches for the constructions and then joins in the student information. This allows me to bring all of the data together into one table. This means that I (when the regexes are in their final form) will have the count of the number of constructions, and the demographic information ready to go.

#### Next Steps

I want to check to see if there is any other useful data by poking around the corpus sight more. Also, I am going to look into the prompts, as this will have a bearing on what kind of tense construction students used.

The biggest thing however, will be the regex. I need to think of a way to determine that the constructs are correct. One thing I am certainly thinking about is doing it manually. There is about 1500 cases, which may be in the realm of possibility to check. The other is to get sophisticated and bring in part of POS tagging (which is in the data). And of course, statistics will be done after this.

(Also some personal conventions like variable names that will be updated)

## Progress Report 2

My data is pretty much in my final form. The only part that is a catch it determining if the 'going to' construction is capturing the right information. That is, am I actually capturing real information about the use of future tense. To do this, I have created some logic that tags the data with the correct indication: whether or not it uses the 'Going to [verb]' Construction. This means writing a function, then simply filtering for the correct tag. 

It was easier to do the will construction, because if it was tagged for 'MOD' for modal, then I knew it was involved with a verb. But 'going' does not have that. I can check for 'TO' next to it, but I need to see if there is a verb "close" by (sometimes adverbs could intervene).

Since I am looking at proficient, one thing I need to investigate is covariance. Naturally, students who score higher on one proficiency assessment may be more likely to score higher on another. So, either I will have to figure out how to assess these correlations, form a metric between the different assessments, or just pick one and stick with that. 

The data included in the data folder, my working data, is just a transformation of the PELIC data, and can be put there, as this project is clearly attributed to [PELIC data](https://eli-data-mining-group.github.io/Pitt-ELI-Corpus/).This data does not have any annotation. 

#### Data Scheme
The data will be the same as 'working data' right now, just with counts adjusted, so for each student, I will have the count they used 'going' the count the used 'will' and then them information about them. This will allow me to run my analysis. As I planned on looking for correlation with proficiency, this is where I will start. 

#### Liscense
I am using the Creative Commons License [CC BY-NC-ND 4.0 DEED](https://creativecommons.org/licenses/by-nc-nd/4.0/) for two reasons. First, it ensures that if someone take my code, it cannot be used commercially. It also must be attributed to me, and indicate if any changes were made. But chiefly, it is the [license of the PELIC Database]([CC BY-NC-ND 4.0 DEED](https://creativecommons.org/licenses/by-nc-nd/4.0/)) already, so it would be a natural choice because what I have written "sits on top of" PELIC.