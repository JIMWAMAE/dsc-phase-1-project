## Final Project Submission


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
### 1 . Effect of runtime minutes on movie average rating.
Using this analysis we are aiming to identify any relationship available between the runtime of a movie and the average rating it ends up receiving. It will help us advise the company on whether to produce long movies or relatively shorter movies


1. Covariance
With covariance we want to look at two variables (runtime and average rating) to get an idea about how they change together. This will help us identify how the two variables vary together.

```python
#checking the covariance
import numpy as np
runtime_rating = np.cov(df1.runtime_minutes,df1.averagerating)
runtime_rating
array([[232.30049963,  -0.88116023],
       [ -0.88116023,   2.11127023]])
```
The covariance between the two variables is -0.88 . A negative covariance indicates that two variables are inversely related. If the covariance for any two variables is negative, that means, both the variables move in the opposite direction. This therefore means that increase in runtime minutes leads to a lesser average rating.

So as to identify the extent of the relationship we will go ahead and identify the correlation between the two variables.

2.Correlation
```python
# checking the correlation
correlation_matrix = np.corrcoef(df1.runtime_minutes,df1.averagerating)
correlation_matrix
array([[ 1.        , -0.03978853],
       [-0.03978853,  1.        ]])
```
The result is a negative correlation of 0.03 signifying a week negative correlation which implies that the significance of the effect of change between runtime and rating is inverse and significantly small.

```python
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
```
![Runtime_minutes](https://github.com/JIMWAMAE/dsc-phase-1-project/blob/master/img/output.png)
![Average_Rating](https://github.com/JIMWAMAE/dsc-phase-1-project/blob/master/Average_rating.png)
##### Conclusion
From the covariance we get to identify a negative covariance between the the runtime and average ratings which signifies that they are inversely related . Therefore movies with shorter runtime tend to receive higher ratings.

From the correlation analysis we realize that the relationship is a week negative correlation therefore signifying that despite the fact that the two are inversely related the change in one affects the other slightly.

From the histograms we can draw some insight that most of the movies being produced in the market range from 75 minutes and 100 minutes and that most movies receive a rating between 6 and 8. We can also identify that the data is not semetrical for both the variables.

#### 2. Top perfoming movie genres according to genres average ratings.
This aims at identifying the top produced genres in the market so as to provide Microsoft studios with an idea of what film to produce so as to generate a high average rating from the industry and in return a higher profit margin as a result of increased sales.

```python
# Checking the top genres produced
df_genre = df.genres.value_counts().head(12)
df_genre
Drama                   10189
Documentary              9149
Comedy                   4604
Comedy,Drama             2451
Horror                   2232
Drama,Romance            1392
Thriller                 1288
Comedy,Drama,Romance     1159
Comedy,Romance           1128
Horror,Thriller           943
Drama,Thriller            938
Action                    721
Name: genres, dtype: int64
```
Having obtained a significant sample from the population of the genre count we will proceed with grouping our data using the genres so as to obtain a dataset that will enable us to calculate the average rating per genre.





Having grouped the data we will now proceed with tabulating the averagerating mean per genre.

```python
# a code to calculate the mean average genre rating/sorting the values
avgrating_mean = dfg.groupby('genres')['averagerating'].mean().sort_values(ascending=False).head(12)
#rounding off the values
avgrating_mean  = round(avgrating_mean,1)
avgrating_mean
genres
Documentary             7.3
Drama                   6.5
Comedy,Drama            6.4
Comedy,Drama,Romance    6.3
Drama,Romance           6.2
Drama,Thriller          6.1
Comedy,Romance          5.8
Comedy                  5.8
Action                  5.6
Thriller                5.5
Horror,Thriller         4.7
Horror                  4.7
Name: averagerating, dtype: float64
```
```python
# A code to plot the bargraph
fig,ax = plt.subplots( figsize=(15,10))
sns.barplot(data=avgrating_mean,x=avgrating_mean.index,y= avgrating_mean.values,order= avgrating_mean.values)
plt.xticks(range(len(avgrating_mean)), avgrating_mean.index,rotation=-45)
plt.xlabel('Genre')
plt.ylabel('Total average rating')
plt.title('Movie Genre')
;
''
```
![Top_genre](https://github.com/JIMWAMAE/dsc-phase-1-project/blob/master/img/Movie%20Genre.png)

##### Conclusion
In the movie industry there are various genres which all perfom differently in terms of the average rating . We have used the data provided so as to identify the top perfoming genres according to rating . The top perfoming genre happens to be Documentary and drama with an average rating of above 6. It is therefore advised that prior to deciding what movie to produce in the studio always concider what genre so as to achieve the target rating and also to make the best out of the business.

#### 3. Effect of directors and writters selection towards movie success.
This study seeks to identify the effect of using different directors and writteres for a movie creation. All the directors and writters have different experiences in the industry and choosing an experienced one with a good average rating will influence the quality of your movie and in turn a great rating and revenue generation.


```python
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
primary_name	Director_id	movie_id	runtime_minutes	averagerating	numberOfRatings	sumOfRatings	total_rating
0	Dennis Korbylo	nm6179119	tt3428990	90.0	9.0	28	252.0	9.000000
1	Erin Korbylo	nm6179118	tt3428990	90.0	9.0	28	252.0	9.000000
2	Sylvia Broeckx	nm6179115	tt3428990	90.0	9.0	28	252.0	9.000000
3	Joe Russo	nm0751648	tt1843866	136.0	7.8	30	247.4	8.246667
4	Anthony Russo	nm0751577	tt1843866	136.0	7.8	30	247.4	8.246667
5	Pauli Janhunen Calderón	nm6216421	tt3477564	52.0	7.5	23	187.5	8.152174
6	S.S. Rajamouli	nm1442514	tt1582546	125.0	7.4	22	175.7	7.986364
7	Sukumar	nm1335875	tt1869226	141.0	7.0	25	199.2	7.968000
8	Declan Lynch	nm3371703	tt3295964	90.0	6.8	26	199.9	7.688462
9	Rich Moore	nm0601781	tt1772341	101.0	7.7	23	174.7	7.595652
```
```python
#Displaying the director name and total rating
df5_1 = df5.drop(["Director_id","movie_id","runtime_minutes","averagerating","numberOfRatings","sumOfRatings"],axis=1)
df5_1
primary_name	total_rating
0	Dennis Korbylo	9.000000
1	Erin Korbylo	9.000000
2	Sylvia Broeckx	9.000000
3	Joe Russo	8.246667
4	Anthony Russo	8.246667
5	Pauli Janhunen Calderón	8.152174
6	S.S. Rajamouli	7.986364
7	Sukumar	7.968000
8	Declan Lynch	7.688462
9	Rich Moore	7.595652
```
Having created a data frame showing the top 10 movie directors we will now create a bar graph showing the best movie director.

```python
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
''
```
![Directors](https://github.com/JIMWAMAE/dsc-phase-1-project/blob/master/img/Movie%20directors.png)

From this data we can identify the directors whose movie average ratings are highest from a sample of 20 movies directed as the minimum movie count. This brings us to the conclusion that movie succes is related to the choice of director. We will now proceed with investigating the effect of writter selection on movie success.

```python
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
primary_name	Writter_id	movie_id	runtime_minutes	averagerating	numberOfRatings	sumOfRatings	total_rating
0	Andrés Gómez D.	nm7553102	tt4980150	100.0	8.1	21	170.1	8.100000
1	Juan Gordon	nm7553098	tt4980150	100.0	8.1	21	170.1	8.100000
2	Jörn Röver	nm1841673	tt1779471	91.0	7.8	22	170.1	7.731818
3	Jack Kirby	nm0456158	tt0458339	124.0	6.9	27	197.2	7.303704
4	Sara Hribar	nm2543803	tt2255032	100.0	7.6	25	180.6	7.224000
5	Mark Monroe	nm0598531	tt1262986	90.0	7.9	42	303.2	7.219048
6	Ryan Henry Knight	nm9160937	tt4050462	142.0	7.2	85	613.6	7.218824
7	Vlatka Vorkapic	nm1204572	tt1723793	72.0	9.0	24	173.2	7.216667
8	Pete Navarro	nm8343598	tt4050462	142.0	7.2	83	597.6	7.200000
9	Julien Dinse	nm8337609	tt4050462	142.0	7.2	83	597.6	7.200000
```
```python
#Displaying the writter name and total rating
df6_1 = df6.drop(["Writter_id","movie_id","runtime_minutes","averagerating","numberOfRatings","sumOfRatings"],axis=1)
df6_1
primary_name	total_rating
0	Andrés Gómez D.	8.100000
1	Juan Gordon	8.100000
2	Jörn Röver	7.731818
3	Jack Kirby	7.303704
4	Sara Hribar	7.224000
5	Mark Monroe	7.219048
6	Ryan Henry Knight	7.218824
7	Vlatka Vorkapic	7.216667
8	Pete Navarro	7.200000
9	Julien Dinse	7.200000
```
```python
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
''
```
![Writters](https://github.com/JIMWAMAE/dsc-phase-1-project/blob/master/img/Writters.png)

This data represents the top 10 writters that have significantly had succesful movie writting carreers from a minimum sample of 20 movies. Equally the choice of a writter will have effect on the movie success.

##### Conclusion
In the movie industry there are various directors that are in the industry. The directors have all had different experience in the industry and different movie ratings. The choice of the director will affect the movie rating and thus one will need to select the director base on their success.

Similarly in the movie industry there are various writters that are in the industry. The writters have all had different experience in the industry and different movie ratings. The choice of the writter will affect the movie rating and thus one will need to select the director base on their success.

#### 4. What is the correlation between budget and revenue generated for a given movie.
This aims at establishing the relationship between budget assigned to a movie and the profits that the movie will generate. We will evaluate the significance of increace in budget towards the revenue generated and also cunduct a correlation analysis to identify the relationship between the two and how they affect each other.



We need to create a new column known as revenue that combines the domestic gross with the worldwide gross so as to help us tabulate the total revenue generated and another column profits that will get the difference between revenues and the budget.

```python
#Creating the column revenue and profits
mov_budget['revenue']= mov_budget.domestic_gross + mov_budget.worldwide_gross
mov_budget['Profits']= mov_budget.revenue - mov_budget.production_budget
```
```python
# Sorting the values so as to order them in descending order based on profits
mov_budget = mov_budget.sort_values(by=["Profits"],ascending=False)
mov_budget
id	release_date	movie	production_budget	domestic_gross	worldwide_gross	revenue	Profits
0	1	Dec 18, 2009	Avatar	425000000	760507625	2.776345e+09	3.536853e+09	3.111853e+09
5	6	Dec 18, 2015	Star Wars Ep. VII: The Force Awakens	306000000	936662225	2.053311e+09	2.989973e+09	2.683973e+09
42	43	Dec 19, 1997	Titanic	200000000	659363944	2.208208e+09	2.867572e+09	2.667572e+09
6	7	Apr 27, 2018	Avengers: Infinity War	300000000	678815482	2.048134e+09	2.726950e+09	2.426950e+09
33	34	Jun 12, 2015	Jurassic World	215000000	652270625	1.648855e+09	2.301125e+09	2.086125e+09
...	...	...	...	...	...	...	...	...
352	53	Apr 27, 2001	Town & Country	105000000	6712451	1.036477e+07	1.707722e+07	-8.792278e+07
404	5	Aug 16, 2002	The Adventures of Pluto Nash	100000000	4411102	7.094995e+06	1.150610e+07	-8.849390e+07
193	94	Mar 11, 2011	Mars Needs Moms	150000000	21392758	3.954976e+07	6.094252e+07	-8.905748e+07
341	42	Jun 14, 2019	Men in Black: International	110000000	3100000	3.100000e+06	6.200000e+06	-1.038000e+08
2	3	Jun 7, 2019	Dark Phoenix	350000000	42762350	1.497624e+08	1.925247e+08	-1.574753e+08
5234 rows × 8 columns
```

We will now proceed and visualize the distribution of movies with regards to the budget and the profits.

```python
plt.hist(mov_budget.production_budget,color='green', bins=20)
plt.xlabel('Budget(in tens of millions)')
plt.ylabel('movies')
plt.title('Production Budget')
plt.show()
plt.hist(mov_budget.Profits,color='blue', bins=20)
plt.xlabel('profit(in tens of millions)')
plt.ylabel('movies')
plt.title('Movie Profits')
plt.show()
```
![Budget](https://github.com/JIMWAMAE/dsc-phase-1-project/blob/master/img/Production%20budget.png)
![Profit](https://github.com/JIMWAMAE/dsc-phase-1-project/blob/master/img/Movie%20Profits.png)

This visualisation shows that most movies have been produced within a tight budget . We will have to perfom a correlation analysis so as to identify whether the big expenditures in return generated high profits.

Correlation between profits and budget.
Now having the profits generated by the movies we need to identify the correlation between the budget and the profit that the movies make.

```python
#Correlation analysis budget vs profits
correlationn_matrix = np.corrcoef(mov_budget.production_budget,mov_budget.Profits)
correlationn_matrix
array([[1.        , 0.63879082],
       [0.63879082, 1.        ]])
```
From the analysis we find that the correlation is at 0.64 wich signifies strong positive correlation.This implies that movies that had high budgets equally had high profits. This can be visualised by a scatter plot below.

```python
#ploting a scatter plot to visualize correlation.
fig,ax = plt.subplots(figsize=(12,10))
ax.scatter(mov_budget.production_budget,mov_budget.Profits)
plt.xlabel('Budget(in tens of millions)')
plt.ylabel('Profit')
plt.title('Profits Vs Budget')
Text(0.5, 1.0, 'Profits Vs Budget')
```
![prof_budg_corr](https://github.com/JIMWAMAE/dsc-phase-1-project/blob/master/img/Prof%20vs%20budg.png)
This scatter plot helps us visualize a possitive correlation between budget and profit.

```python
# Now that we are done with writting queries we close the connection to the database
conn.close()
```
##### Conclusion
The budget assigned to a movie production is directly proportional to the profits generated.
We would rather incurr a high budget towards production so as to generate more income from the movie.
#### Findings
1. Runtime is inversely related to movie average ratings. Long movies have lower ratings compared to short movies.
2. Different genres perfom differently in the movie industry with regard to the ratings.
Documentary(7.3)
Drama(6.5)
Comedy,Drama(6.4)
Comedy,Drama,Romance(6.3)
Drama,Romance(6.2)
Drama,Thriller(6.1)
Comedy,Romance(5.8)
Comedy (5.8)
Action(5.6)
Thriller(5.5)
Horror,Thriller(4.7)
Horror(4.7)
3. Different writters and dirrectors perfom differently based on factors such as experience and expertise in the industry by looking at their average rating movies perfomance.
4. Movie budget and revenue genaration are directly related where high budget generates more revenue.
#### Recommendations
1. The studio should aim at producing movies with relatively short runtime as it is inversely related to average rating. This is because the movies with short runtimes ended up having more average ratings. This will entail avoiding unnecesary scenes and being brief and on point with the shooting.
2. The studio should aim at producing the top preffered genres that have a high average rating which means they will generate high profits compared to the relatively unwatched genres.
3. The studio should aim at using the top directors and writters according to experience in the industry according to their average ratings as they will enable the studio to produce top tier movie quality that will in turn generate better returns for the business.
4. The studio should aim at assigning the required budget and not under funding their movie projects since from the data movies with high budgets generated higher revenue. This entails using the best technology and tools possible to get the best quality.
#### Summary
In this project we have familiarized with the movie industry and the factors that affect the movie success so as to advise microsoft movie studios in their start up.