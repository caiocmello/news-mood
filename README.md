# Is sentiment in the news becoming more negative over time? 

A recent [report](https://reutersinstitute.politics.ox.ac.uk/digital-news-report/2022/dnr-executive-summary) published by the Reuters Institute for the Study of Journalism analysing news consumption across different countries unveiled the increase in news avoidance. Among the reasons, one of the most common answers by interviewees was that ‘news negatively affect their mood’: 

>'The proportion of news consumers who say they avoid news, often or sometimes, has increased sharply across countries. This type of selective avoidance has doubled in both Brazil (54%) and the UK (46%) over the last five years, with many respondents saying news has a negative effect on their mood'. (Digital News Report, 2022)

![](./img/image2.png)

However, in other countries like Germany, Sweden, Switzerland, Finland and Netherlands the variation was not considerable. 

This project compared the sentiment of news articles published by BBC on twitter in 2012, 2015, 2017, 2019 and 2022 to see if there is a variation of sentiment in the language used by news articles over time that could be related to how people feel the effect in their mood.  

## Data Collection

The data collection consisted of the following steps:

1. Getting tweets from @BBCBreaking timeline for specific years (2012,2015,2017,2019 and 2022) using the Twitter API.
- Packages used: `Twarc2`
2. Filtering the tweets data to get only the url for news articles published by @BBCBreaking.
- Packages used: `Pandas`
3. From the list of urls, using Newspaper3k to scrape the news headlines from the articles' webpages. After getting the list of news headlines, we removed the duplicated results as @BBCBreaking tweeted some articles more than once. 
- Packages used: `Newspaper3k` (`Beautiful Soup` & `NLTK`), `Pandas`

The image below illustrates the data collection pipeline:

![](./img/collection_pipeline.png)

## Dataset description:

Dataset is available for download [here](https://doi.org/10.5281/zenodo.6927800)

The dataset is in `.csv` format and is organised as follows:
- Columns:
  - ID (tweet ID)
  - created_at (tweet publication's date)
  - url (url of the news article attached to the tweet)
  - Titles (news headline)
- Rows:
Each row contains a single news article headline sorted by date of publication (created_at). Total number of entries: 7213.

The image below shows the dataset overview:

![](./img/dataset_overview.png)

It is interesting to notice how the number of tweets published by @BBCBreaking reduced over the years. NOTE: The year 2022 data goes only until July. 

## Data Analysis

The data analysis aimed to answer the following questions:
1. Has sentiment in news headlines become more negative over time?
2. What are the yearly changes in sentiments?
3. What are the most frequent topics and terms for each year?

### Sentiment Analysis

The first step to analyse the variation of sentiment over the years was applying `VADER`, a sentiment classifier of `NLTK`, to each news headline. VADER is a lexicon and rule-based algorithm. The result of the sentiment analysis can be visualised in the chart below: 

![](./img/sentiment_overtime.png)

There is a slight increase in negative sentiments over time (around 10% between 2012 to 2022), while neutral scores decreased. Positive, however, remained stable over the analysed years. 

It is interesting to notice the discrepancy between negative and positive sentiments in the news headlines. In this corpus, negative scores were assigned more frequently to headlines than positive and neutral. It is important to consider the dataset context, as the news analysed are a selection of what was tweeted by @BBCBreaking among the articles published by bbc.co.uk. 


#### Time series

To gain insight into the changing nature of sentiment in news headlines over time, we first constructed time series graphs for each of our study periods (2017, 2019, and 2022) using the Python package ``Seaborn``.

Plotted on the graph of each time period is a 30-day rolling mean of sentiment scores returned by ``VADER`` via the ``NLTK`` package: Positive Polarity, Negative Polarity, and Overall Polarity (or compound score).

Each individual word in ``VADER``’s lexicon is measured on a scale from -4 to +4, with -4 representing the highest valence (or intensity) score of negative sentiment and +4 representing the highest valence score of positive sentiment.

Overall polarity of a given document (in this case title of a news article) is measured using ``VADER``’s compound value: a normalised, weighted sum of the valence scores of each word in its lexicon. The polarity value returned is an overall score in the range [-1,1]. _pos_, _neu_, and _neg_ scores represent ratios for the proportions of text in each news headline that fall in each category. For each document the pos, neu, and neg scores sum to 1.

To provide context, peaks and troughs in these scores over time were investigated manually by interrogating the dataset and finding the corresponding news stories in relevant periods. Graphs were annotated to illustrate the news environment, helping to ensure validity of the sentiment scoring process.

![2017 annotated ts graph](https://user-images.githubusercontent.com/77411190/181502396-36242517-0fd6-4b7f-82f3-c4c94dd34789.png)
![2019 annotated ts graph](https://user-images.githubusercontent.com/77411190/181502417-5677530e-dd12-4af6-8fc3-59d309c9bda3.png)
![2022 annotated ts graph](https://user-images.githubusercontent.com/77411190/181502448-200fc7d7-d1fa-473b-9f7e-c030b05226d9.png)

An unusual dip in negativity scores around November 2017 helps illustrate some of the limitations in using dictionary-based sentiment analysis tools. The Paradise Papers tax scandal was undoubtedly a negative story, but generates disproportionately positive scores due to the presence of positive words such as 'paradise', 'wealthy', and 'fundraiser'. The BBC also shared stories on its Breaking News Twitter feed repeatedly, skewing the 30-day rolling mean. With more time, we would recommend applying sentiment analysis to a larger body of text (e.g. the introduction of the web article) to generate a more realistic depiction of overall sentiment.



#### Regression analysis

To provide a statistical answer to the question of whether overall sentiment has changed over time, we turn to OLS regression via ``statsmodels``. A new column to measure time as an independent variable - day_num - was constructed using the datetime package. This was  regressed against our three key dependent variables - polarity, positivity and negativity - in turn. The results of these three models are shown below.



![polarity_ols_result](https://user-images.githubusercontent.com/77411190/181587942-3c55e696-3993-4ffb-9c41-46af0f8ba6cc.png)
![pos_ols_result](https://user-images.githubusercontent.com/77411190/181587992-910ae4c7-defd-4f5f-a8a8-31fb2fc227cb.png)
![neg_ols_result](https://user-images.githubusercontent.com/77411190/181588166-427df3cc-54e7-45fb-99a4-4136eeddb912.png)

While the coefficients in each model are small, their direction presents interesting results. Surprisingly, the proportion of positive words in headlines is increasing very slightly over time, while the proportion of negative words is decreasing slightly over time. All appear statistically significant.



### Emotion Analysis

In an attempt to complexify the interpretation of the 'news mood', we have used NRCLex, an affect generator based on TextBlob and the NRC affect lexicon. The package measures the following emotional affects:

-fear
-anger
-anticipation
-trust
-surprise
-positive
-negative
-sadness
-disgust
-joy

The result of Emotion Analysis applied to headlines by year can be visualised in the chart below (NOTE: the results are not normalised. The different amount of headlines for each year does not allow comparisons over the years): 


![](./img/emotions.png)

What this classifier does it assign an emotion to each word in the corpus. This is not sentence level as the Sentiment Analysis applied before. It is interesting to highlight that 'sadness' appears among the top 5 emotions for every year in the corpus, except for 2022, when it became the 7th. Fear was always the second most common emotion. The use of emotion classification deserves further investigation to see how it could be more meaninful for this kind of analysis.

### Topic Modeling

For topic modeling we first planned to run the analysis with the [BERTopic](https://maartengr.github.io/BERTopic/index.html) package of Python, however due to the functional issues, we switched to R. In R, we benefitted from the [quanteda](https://quanteda.io/) and [STM](https://cran.r-project.org/web/packages/stm/vignettes/stmVignette.pdf) (Structural Topic Modeling) packages.

At the first step, we constructed our corpus seperately for each year. The corpus has been tokenized and stopwords have been remowed. In case of any duplicates, those rows have been remowed as well. A [document-feature matrix](https://www.rdocumentation.org/packages/quanteda/versions/1.5.2/topics/dfm) (dfm) was created for running stm. 

Top twenty topics with their top three terms appear as follows in stm:

![](./img/2012_toptopics.png)
![](./img/2015_toptopics.png)
![](./img/2017_new.png)
![](./img/2019_new.png)
![](./img/2022_new.png)

We also visualized the most frequent words in each year within the `textplot_wordcloud()` function of the quanteda package.
Most frequent 100 words for 2015 appear as follows:

![](./img/colour2015.jpeg)

Required packages: `readtext`, `stm`, `quanteda`, `quanteda.textstats`, `quanteda.textplots`, `quanteda.corpora`, `ggplot2`.




### References

- Brady, William; Wills, Julian; Jost, John; Tucker, Joshua, and Van Bavel, Jay (2017). ‘Emotion shapes the diffusion of moralized content in social networks'. Proceedings of the National Academy of Sciences, 114, 7313 - 7318.

- Brady, William; Wills, Julian; Burkart, Dominic; Jost, John and Van Bavel, Jay (2019) 'An Ideological Asymmetry in the Diffusion of Moralized Content on Social Media Among Political Leaders'. Journal of Experimental Psychology. 148(10): 1802-1813. doi: 10.1037/xge0000532 

- de León E and Trilling D (2021) 'A Sadness Bias in Political News Sharing? The Role of Discrete Emotions in the Engagement and Dissemination of Political News on Facebook'. Social Media + Society. October 2021. doi:10.1177/20563051211059710

- Kraft, Patrick; Krupnikov, Yanna; Milita, Kerri; Ryan, John Barry and Soroka, Stuart (2020) 'Social Media and the Changing Information Environment: Sentiment Differences in Read Versus Recirculated News Content', Public Opinion Quarterly, Volume 84, Issue S1, 2020, Pages 195–215, https://doi.org/10.1093/poq/nfaa015

- Soroka S, Young L, and Balmas M (2015) 'Bad News or Mad News? Sentiment Scoring of Negativity, Fear, and Anger in News Content'. The ANNALS of the American Academy of Political and Social Science. 2015;659(1):108-121. doi:10.1177/0002716215569217

- Trussler M, and Soroka, S. (2014) 'Consumer Demand for Cynical and Negative News Frames'. The International Journal of Press/Politics. 2014;19(3):360-379. doi:10.1177/1940161214524832

#### Packages

- [Twarc2](https://twarc-project.readthedocs.io/en/latest/twarc2_en_us/)
- [Pandas](https://pandas.pydata.org/)
- [Newspaper3k](https://newspaper.readthedocs.io/en/latest/)
- [BeautifulSoup](https://beautiful-soup-4.readthedocs.io/en/latest/)
- [NLTK](https://www.nltk.org/)
- [Seaborn](https://seaborn.pydata.org/)
- [Statsmodels](https://www.statsmodels.org/stable/index.html)
- [NRCLex](https://pypi.org/project/NRCLex/)
- [BERTopic](https://maartengr.github.io/BERTopic/index.html)
- [Quanteda](https://quanteda.io/)
- [Wordcloud](https://amueller.github.io/word_cloud/)
- [ggplot2](https://ggplot2.tidyverse.org/)
- [Raw Graphs](https://www.rawgraphs.io/)


### Acknowledgement
We would like to thank Jana Lasser for the valuable contributions and support provided during this project. 

#### This project was developed during the SICSS-Aachen-Graz (July 18 to July 29, 2022).



