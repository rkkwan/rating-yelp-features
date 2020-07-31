# Rating Food using Yelp Reviews
Analyzing Yelpers' sentiments to determine a restaurant's best and worst dishes

_Author: Ritchie Kwan_

---

## Table of Contents

0. [Converting Yelp's JSON files to CSV](code/00-converting-json-to-csv.ipynb)
1. [Cleaning Data](code/01-cleaning-data.ipynb)
1. [Exploratory Data Analysis](code/02-eda.ipynb)
1. [Selecting Data](code/03-selecting-a-restaurant.ipynb)
1. Preparing Data
    1. [Extracting Sentences](code/04.1-extracting-sentences.ipynb)
    1. [Extracting Chunks](code/04.2-extracting-chunks.ipynb)
1. [Rating Dishes](code/05-rating-dishes.ipynb)
    1. Predicting a Dish's Sentiment Score
    1. Converting Sentiment Score to Stars
1. [Evaluating Predictions](code/06-evaluating-predictions.ipynb)


## Presentation
[Rating Yelp Dishes](https://docs.google.com/presentation/d/1AJK0bjvfv5uJDRuppb7xC9GwR_sUUkeGrc7KPsJZdw0/edit?usp=sharing)


## Problem Statement
A single 5-star rating system on a restaurant might give you an idea of which restaurant to go to, but it does not specifically rate any particular feature. Key features could be specific menu items, freshness, tastiness, service, speediness, cleanliness. A 4.5-star restaurant does not mean all of its dishes are 4.5 star dishes. It could have 5-star dishes, probably a few 1-star dishes, 4-star service, 2-star speediness. A Yelper should be able to immediately see what dishes are recommended by reviewers, which restaurant has the best service, and so on.

Enter **[Named-Entity Recognition](https://en.wikipedia.org/wiki/Named-entity_recognition)** (NER) and **[Sentiment Intensity Analysis](https://www.kdnuggets.com/2018/08/emotion-sentiment-analysis-practitioners-guide-nlp-5.html)** (SIA).

Performing NER to detect key features of a restaurant, then SIA to predict the  sentimental score of those features can provide more detailed insight about a business than a generalized star rating. SIA on sentence fragments of reviews that mention specific menu items can predict the true rating of each dish.


## Assumptions
With a sufficient number of reviews that mention a particular dish, the quality of a menu item's can be accurately predicted by aggregating the sentiment of the reviews.
It is assumed that Yelp has already removed fake reviews, so all reviews being analyzed have honest opinions.


## Executive Summary
### Goal
Predict the quality of a restaurant's menu items and key features by analyzing fragments of reviews that mention specific menu items and features of interest.  Features may include: Tastiness, quality of service, cleanliness of bathrooms, speediness.

#### Use Case 1:  Yelper wants to know the best dishes to order at a certain restaurant.
1. Select a restaurant.
2. Collect all reviews from that restaurant.
3. Perform NER and SIA. Predict a rating for each dish.
4. Display menu items, sorted by rating.
5. Result: The top-rated dishes are the restaurant's highest recommended menu items.

![monamigabi-best](images/monamigabi-best.png)

#### Use Case 2: Yelper wants to know where to get the best dish in a certain region.
1. Select a dish and a region (zip code, city).
2. Find all restaurants in the region that serve the dish.
3. Perform NER and SIA on reviews that mention the dish for each restaurant and predict a rating for each restaurant.
4. Display the restaurants, sorted by rating of the dish.
5. Result: The top-rated restaurants serve the best versions of that dish.

#### Use Case 3: Yelper wants to get an overview of a restaurant's key features.
1. Select a restaurant.
2. Collect all reviews from that restaurant.
3. Perform NER and SIA. Predict a rating for each key feature.
4. Display the key features, sorted by rating.
5. Result: The top-rated features are the restaurant's best features.


## Statistical Analysis

### Metrics
This is an unsupervised analysis, so the metric of success is getting a sufficiently diverse distribution of sentiment scores / ratings for each restaurant's menu items. Comparing the predicted best dishes to Yelp's most reviewed dishes could be a viable metric.

### Findings
* Preliminary analysis features Mon Ami Gabi, the most reviewed restaurant in the dataset with 7968 reviews. Most restaurants have less than 100 reviews, so performing the same analysis on such a small sample of reviews may produce unreliable results.
* SIA was performed at three levels of detail: Review, Sentence, and Chunk. Chunks are sentence fragments containing up to 7 words before and after the entity of interest. Chunk-level SIA proved have the highest variance of sentiment scores.

#### Example: Entity of interest is *onion soup*.
##### Most positive Chunks
![onion-soup-pos](images/onion-soup-pos.png)
##### Most negative chunks
![onion-soup-neg](images/onion-soup-neg.png)
##### Average sentiment chunks
![onion-soup-avg](images/onion-soup-avg.png)

### Limitations
* Sentence fragment extraction is performed on single sentences. The sentiment of a multi-sentence fragment like "I got the onion soup. It was delicious." is missed.
* Dish-level Sentence fragments may still include sentiments meant for a different entity.
    * Example sentence: "The onion soup was great, the steak was terrible, the lemon tarte was delicious." will get processed as `"the onion soup was great the steak was terrible the"` instead of `"the onion soup was great"`.
* Converting sentiment score to stars requires tuning. The current version uniformly distributes the score range `[-1,1]` to discrete stars `[1,5]`. In reality a score between `[-1, .1]` could all be 1 star ratings.
