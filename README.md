# Is sentiment in the news becoming more negative over time? 

A recent [report](https://reutersinstitute.politics.ox.ac.uk/digital-news-report/2022/dnr-executive-summary) published by the Reuters Institute for the Study of Journalism analysing news consumption across different countries unveiled the increase in news avoidance. Among the reasons, one of the most common answers by interviewees was that ‘news negatively affect their mood’: 

>'The proportion of news consumers who say they avoid news, often or sometimes, has increased sharply across countries. This type of selective avoidance has doubled in both Brazil (54%) and the UK (46%) over the last five years, with many respondents saying news has a negative effect on their mood'. (Digital News Report, 2022)

![](./img/image2.png)

However, in other countries like Germany, Sweden, Switzerland, Finland and Netherlands the variation was not considerable. 

This project compared the sentiment of news articles published by BBC on twitter in 2017, 2019 and 2022 to see if there is a variation of sentiment in the language used by news articles over time that could be related to how people feel the effect in their mood.  

## Data Collection

The data collection consisted of the following steps:

1. Getting tweets from @BBCBreaking timeline for specific years (2012,2015,2017,2019 and 2022) using the Twitter API.
- Packages used: `Twarc2`
2. Filtering the tweets data to get only the url for news articles published by @BBCBreaking.
- Packages used: `Pandas`
3. From the list of urls, using Newspaper3k to scrape the news headlines from the articles' webpages. After getting the list of news headlines, we removed the duplicate results as @BBCBreaking tweeted some articles more than once. 
- Packages used: `Newspaper3k` (`Beautiful Soup` & `NLTK`), `Pandas`

The image below illustrates the data collection pipeline:

![](./img/collection_pipeline.png)

## Dataset description:

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

It is interesting to notice how the number of tweets published by @BBCBreaking reduced over the years. 

## Data Analysis

The data analysis aimed to answer the following questions:
1. Has sentiment in news headlines become more negative over time?
2. What are the yearly changes in sentiments?
3. What are the most frequent topics and terms for each year?
...

### Sentiment Analysis

![](./img/sentiment_overtime.png)

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

*A recommendation on using another sentiment analysis to provide convergent validity goes here!*


#### Regression analysis

To provide a statistical answer to the question of whether overall sentiment has changed over time, we turn to OLS regression via ``statsmodels``. Using ``datetime``, we converted the date column in our dataset to a new column: duration. This represented the sole independent variable to be regresses separately against our key dependent variabls: _polarity_, _pos_, and _neg_. The results of these OLS models are shown below.

![polarity_ols_result](https://user-images.githubusercontent.com/77411190/181547196-b5cd7d13-21d0-4ce3-9f90-cc214beb509f.png)
![pos_ols_result](https://user-images.githubusercontent.com/77411190/181547261-ac0be0be-b6ff-4500-90ef-82f05a6fa768.png)
![neg_ols_result](https://user-images.githubusercontent.com/77411190/181547429-4b549954-909d-4991-bf4e-613d891d477e.png)


Surprisingly, ....


### Emotion Analysis

### Topic Modeling

For topic modeling we first planned to run the analysis with the BERTopic package of Python, however due to the functional issues, we switched to R. 

### References





