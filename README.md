# Phase-1-dscproject
Introduction
As a data scientist I have been given a task by Microsoft Movie studios to analize given datasets so as to generate insights on what type of films to create. We are going to do an exploratory data analysis on the data provided from the movies box office.

### Objectives
1. Effect of runtime minutes on movie average rating.
2. What are the top perfoming movie genres according to genres average ratings?
3. Effect of directors and writters selection towards movie success?
4. What is the correlation between budget and revenue generated for a given movie?
### Business Understanding
Microsoft is a start up business in the movie industry .They sees all the big companies creating original video content and they want to get in on the fun. They have decided to create a new movie studio, but they don’t know anything about creating movies. We will conduct an exploratory data analysis so as to explore what types of films are currently doing the best at the box office. We will then translate our findings into actionable insights that the head of Microsoft's new movie studio can use to help decide what type of films to create.

### Data Understanding
We have been provided with various data sets for the study. This section will give us an overview of the data we will use to genrate our insights. From this given data we will generate insights so as to advice the company.

The first data set we will use is the im.db database (https://github.com/JIMWAMAE/dsc-phase-1-project-v2-4/blob/master/zippedData/im.db) whose erd is shown below. It will give us data that relates to the movie genres and ratings from the tables movie_basics and movie_ratings. We will use the persons table and the directors to generate isight about director perfomance as well as writter perfomances.
The second data set is the movie budget csv file(https://github.com/JIMWAMAE/dsc-phase-1-project-v2-4/blob/master/zippedData/im.db) that will help us visualize revenue generated as well as the budget allocated for the movies. Data Preparation #importing the relevant libraries import sqlite3 import pandas as pd

## Final Project Submission

Please fill out:
* Student name: JIMCOLLINS WAMAE WANGUKU
* Student pace: full time
* Scheduled project review date/time: 12th March 2023
* Instructor name: William Okomba
* Blog post URL: https://github.com/JIMWAMAE/Phase-1-dscproject

### Introduction

As a data scientist I have been given a task by Microsoft Movie studios to analize given datasets so as to generate insights on what type of films to create. 

#### Objectives
1. Effect of runtime minutes on movie average rating.
2. What are the top perfoming movie genres according to genres average ratings?
3. Effect of directors and writters selection towards movie success.
4. What is the correlation between budget and  revenue generated for a given movie?
#### Business understanding.
Microsoft is a start up business in the movie industry .They see all the big companies creating original video content and they want to get in on the fun. They have decided to create a new movie studio, but they don’t know anything about creating movies. We will conduct an exploratory data analysis so as to explore what types of films are currently doing the best at the box office. We will then translate our findings into actionable insights that the head of Microsoft's new movie studio can use to help decide what type of films to create.
#### Data understanding
We have been provided with various data sets for the study. This section will give us an overview of the data we will use to genrate our insights.

1. The first data set we will use is the im.db database (https://github.com/JIMWAMAE/dsc-phase-1-project-v2-4/blob/master/zippedData/im.db) whose erd is shown below. It will give us data that relates to the movie genres and ratings from the tables movie_basics and movie_ratings. We will use the persons table and the directors to generate isight about director perfomance as well as writter perfomances.
![movie data erd](https://raw.githubusercontent.com/learn-co-curriculum/dsc-phase-1-project-v2-4/master/movie_data_erd.jpeg)

2. The second data set is the movie budget csv file(https://github.com/JIMWAMAE/dsc-phase-1-project-v2-4/blob/master/zippedData/im.db) that will help us visualize revenue generated as well as the budget allocated for the movies.

### Data Preparation
#importing the relevant libraries
import sqlite3
import pandas as pd

# connecting to the database
conn = sqlite3.connect('zippedData/im.db')
cur =conn.cursor()
# Querying the database
df = pd.read_sql (""" SELECT * FROM movie_basics
JOIN movie_ratings
USING (movie_id) """,conn)
df.head(18)
DATA CLEANING
# We begin with checking for duplicates in our data
df.duplicated().value_counts()
# Since it returned that there are no duplicated values in the data frame 
# we proceed with checking for Nan values
# check for nan values
df.isna().sum()
The data subset contains null values in run time minutes and genre with each having 7620 and 804 respectively. We will therefore proceed with dealing with the missing data by further exploring what data is missing and the effect of the missing data for our analysis.
#checking null values in run_time.
df[df['runtime_minutes'].isna()]
# checking the null values in genre
df[df['genres'].isna()]
This gives us a visualisation of what is actually missing which is the duration that a movie lasts. For the 7620 movies we have not been provided with the runtime . This missing data translates to 10% of the total data for run time and 1% for genre . We will therefore proceed with droping the rows containing null values. 
#dropping the missing rows of data
df = df.dropna()

# Checking for missing values in the dataset
df.isna().sum()
Our data set is now free from missing values and duplicates and we can threfore proceed with data exploration.
#### Data exploration
Data exloration will help us in highlighting the patterns and relations in our data set. We will explore the  data set in an unstructured way to uncover patterns, characteristics, and points of interest to help us identify the films that are curently doing well in the curent market. This process isn’t meant to reveal every bit of information that this dataset holds, but rather to help create a broad picture of important trends and major factors that facilitate the success of a film.
# Checking basic descriptive statistics
df.describe()
From the above data we can identify some important information such as the average duration that a film runs by the mean value which is 95 minutes while the median is at 91 minutes showing that the mean is positively skewed.
It is also evident that we have possible outliers based on the min duration and the max duration.
# Visualizing outliers using box plots
import matplotlib.pyplot as plt
%matplotlib inline
plt.style.use("ggplot")
plt.boxplot(df.runtime_minutes)
plt.title('Runtime box plot')
plt.show()
#dealing with the outliers
df1 =df[df['runtime_minutes'].isin(range(55,130))]
plt.style.use("ggplot")
plt.boxplot(df1.runtime_minutes)
plt.ylabel('Runtime in minutes')
plt.title('Runtime box plot')
plt.show()
From the above figures we have visualised the outliers in the data and eliminated them so as to have a data set that is free from outragious values that would affect our study.
Having cleaned the data set we will now proceed with our analysis guided by our objectives so as to come up with the summary of findings
## Getting started
### 1 . Effect of runtime minutes on movie average rating.
With covariance we  want to look at two variables (runtime and average rating) to get an idea about how they change together. This will help us identify how the two variables vary together.
#checking the covariance
import numpy as np
runtime_rating = np.cov(df1.runtime_minutes,df1.averagerating)
runtime_rating
The covariance between the two variables is -0.88 . A negative covariance indicates that two variables are inversely related. If the covariance for any two variables is negative, that means, both the variables move in the opposite direction. This therefore means that increase in runtime minutes leads to a lesser average rating.

So as to identify the extent of the relationship we will go ahead and identify the correlation between the two variables.
# checking the correlation
correlation_matrix = np.corrcoef(df1.runtime_minutes,df1.averagerating)
correlation_matrix
The result is a negative correlation of 0.03 signifying a week negative correlation which implies that the significance of the effect of change between runtime and rating is inverse and significantly small.
# Distribution of runtime and ratings
plt.hist(df1.runtime_minutes,color='green')
plt.xlabel('Runtime in munutes')
plt.ylabel('count')
plt.title('Runtime_minutes')
plt.show()
plt.hist(df1.averagerating,color='blue')
plt.xlabel('Rating')
plt.ylabel('count')
plt.title('Average rating')
plt.show()

#### Conclusion
1. From the covariance we get to identify a negative covariance between the the runtime and average ratings which signifies that they are inversely related . Therefore movies with shorter runtime tend to receive higher ratings.

2. From the correlation analysis we realize that the relationship is a week negative correlation therefore signifying that despite the fact that the two are inversely related the change in one affects the other slightly.

3.  From the histograms we can draw some insight that most of the movies being produced in the market range from 75 minutes and 100 minutes and that most movies receive a rating between 6 and 8. We can also identify that the data is not semetrical for both the variables.
### 2. Top perfoming movie genres according to genres average ratings.
This aims at identifying the top produced genres in the market so as to provide Microsoft studios with an idea of what film to produce so as to generate a high average rating from the industry and in return a higher profit margin as a result of increased sales.
# Checking the top genres produced
df_genre = df.genres.value_counts().head(12)
df_genre
Having obtained a significant sample from the population of the genre count we will proceed with grouping our data using the genres so as to obtain a dataset that will enable us to calculate the average rating per genre.
''' This code aims to select only the movies in the sample genre 
we created and puting them in a dataframe which is a summary of the popular genres'''
options = ['Drama',
'Comedy',
'Documentary',
'Comedy,Drama',
'Horror',
'Drama,Romance',
'Thriller',
'Comedy,Drama,Romance',
'Comedy,Romance',
'Horror,Thriller',
'Drama,Thriller',
'Action']
dfg = df1[df1['genres'].isin(options)]
dfg
# This code aims at displaying the grouped data frame that will be grouped by genre and movie id
genre_grouping = dfg.groupby(['genres','movie_id'])
genre_grouping1 = genre_grouping.first()
genre_grouping1
Having grouped the data we will now proceed with tabulating the averagerating mean per genre. 
# a code to calculate the mean average genre rating/sorting the values
avgrating_mean = dfg.groupby('genres')['averagerating'].mean().sort_values(ascending=False).head(12)
#rounding off the values
avgrating_mean  = round(avgrating_mean,1)
avgrating_mean
# A code to plot the bargraph
fig,ax = plt.subplots( figsize=(15,10))
sns.barplot(data=avgrating_mean,x=avgrating_mean.index,y= avgrating_mean.values,order= avgrating_mean.values)
plt.xticks(range(len(avgrating_mean)), avgrating_mean.index,rotation=-45)
plt.xlabel('Genre')
plt.ylabel('Total average rating')
plt.title('Movie Genre')
;
#### Conclusion

In the movie industry there are various genres which all perfom differently in terms of the average rating . We have used the data provided so as to identify the top perfoming genres according to rating . The top perfoming genre happens to be Documentary and drama with an average rating of above 6. It is therefore advised that prior to deciding what movie to produce in the studio always concider what genre so as to achieve the target rating and also to make the best out of the business.
### 3. Effect of directors and writters selection towards movie success.  
This study seeks to identify the effect of using different directors and writteres for a movie creation. All the directors and writters have different experiences in the industry and choosing an experienced one with a good average rating will influence the quality of your movie and in turn a great rating and revenue generation.
# Visualizing the table directors
df3 =  pd.read_sql (""" SELECT * FROM directors
 """,conn)
df3.head(18)
# Checking for duplicates
df3.person_id.duplicated().value_counts()
# Querying the tables so as to deal with duplicates by grouping the directors
df2 = pd.read_sql (""" SELECT primary_name,person_id AS Director_id,movie_id,runtime_minutes,averagerating FROM movie_basics
JOIN movie_ratings
USING (movie_id)
JOIN directors
USING (movie_id)
JOIN persons
USING (person_id)

GROUP BY Director_id

 """,conn)

df2
# A query to create new columns sum of ratings and number of raatings so as to get the total average rating.
df5 = pd.read_sql (""" SELECT primary_name,
person_id AS Director_id,movie_id,runtime_minutes,averagerating,COUNT(averagerating)AS numberOfRatings,
SUM(averagerating)AS sumOfRatings,
SUM(averagerating)/COUNT(averagerating) AS total_rating 
FROM movie_basics
JOIN movie_ratings
USING (movie_id)
JOIN directors
USING (movie_id)
JOIN persons
USING (person_id)

GROUP BY Director_id
HAVING numberOfRatings>20
ORDER BY total_rating DESC
LIMIT 10;	

 """,conn)
df5 = df5.dropna()

df5
#Displaying the director name and total rating
df5_1 = df5.drop(["Director_id","movie_id","runtime_minutes","averagerating","numberOfRatings","sumOfRatings"],axis=1)
df5_1
Having created a data frame showing the top 10 movie directors we will now create a bar graph showing the best movie director.
#Importing seaborn for ploting and rounding off the ratings
import seaborn as sns
df5_1.total_rating= round(df5_1.total_rating,1)
# A code to plot the bargraph
fig,ax = plt.subplots( figsize=(15,10))
sns.barplot(data=df5_1,x=df5_1.primary_name,y= df5_1.total_rating)
plt.xticks(rotation=-45)
plt.xlabel('Director Name')
plt.ylabel('Total average rating')
plt.title('Top 10 movie directors')
;
From this data we can identify the directors whose movie average ratings are highest from a sample of 20 movies directed as the minimum movie count. This brings us to the conclusion that movie succes is related to the choice of director. We will now proceed with investigating the effect of writter selection on movie success.
#selecting top movie writters based on average rating
df6= pd.read_sql (""" SELECT primary_name,
person_id AS Writter_id,movie_id,runtime_minutes,averagerating,COUNT(averagerating)AS numberOfRatings,
SUM(averagerating)AS sumOfRatings,
SUM(averagerating)/COUNT(averagerating) AS total_rating 
FROM movie_basics
JOIN movie_ratings
USING (movie_id)
JOIN writers
USING (movie_id)
JOIN persons
USING (person_id)

GROUP BY Writter_id
HAVING numberOfRatings>20
ORDER BY total_rating DESC
LIMIT 10;	

 """,conn)
df6 = df6.dropna()

df6
#Displaying the writter name and total rating
df6_1 = df6.drop(["Writter_id","movie_id","runtime_minutes","averagerating","numberOfRatings","sumOfRatings"],axis=1)
df6_1
#Importing seaborn for ploting and rounding off the ratings
import seaborn as sns
df6_1.total_rating= round(df6_1.total_rating,1)
# A code to plot the bargraph
fig,ax = plt.subplots( figsize=(15,10))
sns.barplot(data=df6_1,x=df6_1.primary_name,y= df6_1.total_rating)
plt.xticks(rotation=-45)
plt.xlabel('Writter Name')
plt.ylabel('Total average rating')
plt.title('Top 10 movie Writters')
;
This data represents the top 10 writters that have significantly had succesful movie writting carreers from a minimum sample of 20 movies. Equally the choice of a writter will have effect on the movie success.
#### Conclusion
1. In the movie industry there are various directors that are in the industry. The directors have all had different experience in the industry and different movie ratings. The choice of the director will affect the movie rating and thus one will need to select the director base on their success.

2. Similarly in the  movie industry there are various writters that are in the industry. The writters have all had different experience in the industry and different movie ratings. The choice of the writter will affect the movie rating and thus one will need to select the director base on their success.
### 4. What is the correlation between budget and  revenue generated for a given movie.
This aims at establishing the relationship between budget assigned to a movie and the profits that the movie will generate. We will evaluate the significance of increace in budget towards the revenue generated and also cunduct a correlation analysis to identify the relationship between the two and how they affect each other.
# Importing csv to read the csv dataset
import csv

#creating a dataframe of the csv
mov_budget= pd.read_csv('zippedData/tn.movie_budgets.csv')
mov_budget
##### Data cleaning
we will start with cleaning the data so as to generate the correct insights. 
# removing the $ sign from the columns
mov_budget.production_budget = mov_budget.production_budget.str.replace('$','') 
mov_budget.domestic_gross = mov_budget.domestic_gross.str.replace('$','')
mov_budget.worldwide_gross = mov_budget.worldwide_gross.str.replace('$','')
# removing the commas from the columns
mov_budget.production_budget = mov_budget.production_budget.str.replace(',','') 
mov_budget.domestic_gross = mov_budget.domestic_gross.str.replace(',','')
mov_budget.worldwide_gross = mov_budget.worldwide_gross.str.replace(',','')
#converting the data type to int
mov_budget.production_budget = mov_budget.production_budget.astype(int)
mov_budget.domestic_gross = mov_budget.domestic_gross.astype(int)
mov_budget.worldwide_gross = mov_budget.worldwide_gross.astype(float)
We have succesfully converted the three columns to integers and floating point numerals. We will now proceed with dropping columns with 0 values.
# removing 0 values
mov_budget = mov_budget.loc[~ ((mov_budget.production_budget ==0)|(mov_budget.domestic_gross ==0)|(mov_budget.worldwide_gross ==0))]  
Having cleaned the data we will now proceed with data visualisation.
#cleaned dataframe
mov_budget
We need to create a new column known as revenue that combines the domestic gross with the worldwide gross so as to help us tabulate the total revenue generated and another column profits that will get the difference between revenues and the budget.
#Creating the column revenue and profits
mov_budget['revenue']= mov_budget.domestic_gross + mov_budget.worldwide_gross
mov_budget['Profits']= mov_budget.revenue - mov_budget.production_budget
# Sorting the values so as to order them in descending order based on profits
mov_budget = mov_budget.sort_values(by=["Profits"],ascending=False)
mov_budget
Now having the profits generated by the movies we need to identify the correlation between the budget and the profit that the movies make.  
#Correlation analysis budget vs profits
correlationn_matrix = np.corrcoef(mov_budget.production_budget,mov_budget.Profits)
correlationn_matrix
From the analysis we find that the correlation is at 0.64 wich signifies strong positive correlation.This implies that movies that had high budgets equally had high profits. This can be visualised by a scatter plot below.  
#ploting a scatter plot to visualize correlation.
fig,ax = plt.subplots(figsize=(12,10))
ax.scatter(mov_budget.production_budget,mov_budget.Profits)
plt.xlabel('Budget')
plt.ylabel('Profit')
plt.title('Profits Vs Budget')

This scatter plot helps us visualize a possitive correlation between budget and profit.
# Now that we are done with writting queries we close the connection to the database
conn.close()
#### Conclusion
1. The budget assigned to a movie production is directly proportional to the profits generated.
2. We would rather incurr a high budget towards production so as to generate more income from the movie.
### Findings
1. Runtime is inversely related to movie average ratings. Long movies have lower ratings compared to short movies.
2. Different genres perfom differently in the movie industry with regard to the ratings.
      1. Documentary(7.3)
      2. Drama(6.5)
      3. Comedy,Drama(6.4)
      4. Comedy,Drama,Romance(6.3)
      5. Drama,Romance(6.2)
      6. Drama,Thriller(6.1)
      7. Comedy,Romance(5.8)
      8. Comedy (5.8)
      9. Action(5.6)
      10. Thriller(5.5)
      11. Horror,Thriller(4.7)
      12. Horror(4.7)
3. Different writters and dirrectors perfom differently based on factors such as experience and expertise in the industry by looking at their average rating movies perfomance.
4. Movie budget and revenue genaration are directly related where high budget generates more revenue.
### Recommendations
 1. The studio should aim at producing movies with relatively short runtime as it is inversely related to average rating.
 2. The studio should aim at producing the top preffered genres that have a high average rating as it will in the long run facilitate movie success.
 3. The studio should aim at using the top directors and writters according to experience in the industry according to their average ratings.
 4. The studio should aim at assigning the required budget and not under funding their movie projects since from the data movies with high budgets generated higher revenue.      
### Summary
In this project we have familiarized with the movie industry and the factors that affect the movie success so as to advise microsoft movie studios in their start up.