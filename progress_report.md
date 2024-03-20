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
