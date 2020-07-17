# Rating Food using Yelp Reviews
Analyzing Yelpers' sentiments to determine a restaurant's best and worst dishes

_Author: Ritchie Kwan_

---

## Table of Contents

0. [Converting Yelp's JSON files to CSV](code/00-Converting-JSON-to-CSV.ipynb)
1. [Cleaning Data](code/01-Cleaning-Data.ipynb)
1. [Exploratory Data Analysis](code/02-EDA.ipynb)
1. [Selecting Data](code/03.1-selecting-a-restaurant.ipynb)
1. Data Preparation
    1. [Extracting Sentences](code/03.2-splitting-to-sentences.ipynb)
    1. [Extracting Chunks](code/03.3-splitting-to-chunks.ipynb)
1. [Sentiment Intensity Analysis](code/04-rating-dishes.ipynb)
    1. Predicting a Dish's Sentiment Score
    1. Converting Sentiment Score to Stars
    1. [EDA on Results](code/05-results-EDA)
    1. Recommendations


## Presentation
[Rating Yelp Dishes](https://docs.google.com/presentation/d/1AJK0bjvfv5uJDRuppb7xC9GwR_sUUkeGrc7KPsJZdw0/edit?usp=sharing)


## Problem Statement
A single 5-star rating system on a restaurant does not specifically measure the quality of its individual dishes. A 4.5-star restaurant does not mean all of its dishes are 4.5 star dishes. It could have a variance of 5-star dishes, 4-star dishes, maybe a couple 2-star dishes. A Yelper should be able to immediately see what dishes are truly worth ordering.

Sentiment analysis on specific parts of reviews that mention individual food items can predict the true rating of each food item.


## Assumptions
With a sufficient number of reviews that mention a particular dish, the quality of a menu item's can be accurately predicted by aggregating the sentiment of the reviews.
It is assumed that Yelp has already removed fake reviews, so all reviews being analyzed have honest sentiments.


## Executive Summary
### Goal
Predict the quality of a restaurant's individual menu items by analyzing the sentiment of reviews that talk about food items.

#### Use Case 1:  A Yelper wants to know the best dishes to order at a certain restaurant.
Select a restaurant.
Collect all reviews from that restaurant.
Sentiment analysis is performed. Each dish is given a rating out of 5 stars.
Display menu items sorted by rating.
Result: The top listed items should be the restaurant's most popular dishes.

#### Use Case 2: A Yelper wants to know where to get the best dish in a certain region.
Select a food item and a region (zip code, city).
Find all restaurants in the region that serve the food item.
Sentiment analysis is performed on the food itme for each restaurant and is given a rating out of 5 stars.
The restaurants are sorted by rating of the food item.
Result: The top listed restaurants should serve the best version of that food item.



## Statistical Analysis

### Metrics
This is an unsupervised analysis, so the metric of success is getting a sufficiently diverse distribution of sentiment scores / ratings for each restaurant's menu items. Comparing the predicted best dishes to Yelp's current recommendations (most reviewed dishes) could be viable metric.


### Findings
* Preliminary analysis used the most reviewed restaurant in the dataset with over 8000 reviews. Most restaurants have less than 100 reviews, so performing the same analysis on such a small sample of reviews may produce unreliable results.

### Limitations
* Sentence fragment extraction is performed on single sentences. The sentiment of a multi-sentence fragment like "I got the onion soup. It was delicious." is missed.
* Dish-level Sentence fragments may still include sentiments meant for a different entity.
    * Example sentence: "The onion soup was great, the steak was terrible, the lemon tarte was delicious." will get processed as `"the onion soup was great the steak was terrible the"` instead of `"the onion soup was great"`.
* Converting sentiment score to stars requires tuning. The current version uniformly distributes the score range `[-1,1]` to discrete stars `[1,5]`. In reality a score between `[-1, .1]` could all be 1 star ratings.
