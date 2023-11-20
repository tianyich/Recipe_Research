# Recipe Research 
Author: Tianyi Chen
## Introdutcion
As food becomes more and more abundant, people begin to pursue the deliciousness and nutritional value of food on the basis of eating enough. As a data scientist, we now have two dataset `RAW_recipes.csv` on 83,782 recipes for research and `RAW_interactions.csv` that records 731,927 reviews from customs that records their responses to distinct recipes. 

From studying these two datasets, I want to figure out the trend of people’s food choices. 
I come out with several research question:

1. Will higher-calorie foods taste better (i.e., will they be rated higher)?
2. Will foods made from more ingredients have higher nutritional value?
3. Will higher calorie foods take longer to cook?
4. Do the receipt get higher ratings when the cooking time is longer?

After a careful consideration, I want to develope into question 4. 
I find this question interesting since if the food’s cooking time is short, it has a tend to be simpler and more plain than average food and probably lead to a lower rating; however, taking too much time to cook may increase the risk of failing. Also, people tends to have higher expectation on recipes that takes longer, so they are less likely to give high rating for food that take longer to cook.
Therefore, the result of question4 really intrigues me. And I believe the results will be applicable for chefs and cooking enthusiasts to better adjust their cooking time on specific dishes. The columns in the dataset that will be useful for our research question will be the `minutes` column in the `RAW_recipes.csv` dataset and `rating` column in the `RAW_interations.csv`

## Cleaning and EDA
During this section, we need to: 
    1. Merge the two data set
    Our research question need two columns from two separate dataset. We need to merge the two dataset which so that the columns are in the same dataframe.

    2. Convert each column to appropriate datatype and deal with the potential empty entries or NAN in the data set
    This step helps our further analysis for the dataset.

    3. Aggregate and create a new column to better help study the research question. 
    One recipe may get multiple ratings. For our research question, we aggregate the ratings of each distinct recipe and create a new column `avg_rating` indicating the average rating for each recipe

We first perform a inner merge between the two dataset `recipe` and `interaction` by recipe id. We choose inner merge because recipes that does not have rating and reviews for recipes that we do not have information on will not help our research project. 

After merging, we need to look at the datatypes and missing values in the merged dataframe.

|                |                |
|:---------------|:---------------|
| name           | object         |
| id             | int64          |
| minutes        | int64          |
| contributor_id | int64          |
| submitted      | datetime64[ns] |
| tags           | object         |
| nutrition      | object         |
| n_steps        | int64          |
| steps          | object         |
| description    | object         |
| ingredients    | object         |
| n_ingredients  | int64          |
| user_id        | int64          |
| recipe_id      | int64          |
| date           | datetime64[ns] |
| rating         | int64          |
| review         | object         |

We then convert the `submitted` and `date` columns to `pd.datetime` object.

|                |             |
|:---------------|:---------------|
| name           | object         |
| id             | int64          |
| minutes        | int64          |
| contributor_id | int64          |
| submitted      | datetime64[ns] |
| tags           | object         |
| nutrition      | object         |
| n_steps        | int64          |
| steps          | object         |
| description    | object         |
| ingredients    | object         |
| n_ingredients  | int64          |
| user_id        | int64          |
| recipe_id      | int64          |
| date           | datetime64[ns] |
| rating         | int64          |
| review         | object         |

Then we need to look at the missing values in the merged dataframe. 

|                |   0 |
|:---------------|----:|
| name           |   1 |
| id             |   0 |
| minutes        |   0 |
| contributor_id |   0 |
| submitted      |   0 |
| tags           |   0 |
| nutrition      |   0 |
| n_steps        |   0 |
| steps          |   0 |
| description    | 114 |
| ingredients    |   0 |
| n_ingredients  |   0 |
| user_id        |   0 |
| recipe_id      |   0 |
| date           |   0 |
| rating         |   0 |
| review         |  57 |

We see that all the missing values are non-numeric values. Especially, the two columns we most care about (`minutes` and `rating`) does not have any missing value. So we will not deal with them now. And the missingness in other columns will be addressed in the our missingness analysis section.

To better study our reseach question, we aggregated the merged dataset by recipe Id and created a new column `avg_rating` indicating the average rating for each recipe. We also drop the other columns other than `Id`, `Minutes` and `avg_rating` as they are not relevant to our research question.

The result is show below

|    |     id |   avg_rating |   minutes |
|---:|-------:|-------------:|----------:|
|  0 | 275022 |            3 |        50 |
|  1 | 275024 |            3 |        55 |
|  2 | 275026 |            3 |        45 |
|  3 | 275030 |            5 |        45 |
|  4 | 275032 |            5 |        25 |

We also noticed that some of the recipe takes unusually long time to finish. A recipe takes 1051200 minutes to cook, which is almost 17000 years! Recipes like this are obviously invalid and we need to get rid of them. 

After some analysis, we decided to only keep the recipes that takes less than 12 hours (720 minutes). That reduces our dataframe from 83781 rows to 82891 rows

 ## Univariate Analysis

During this step, we want to take a look at the distribution of relevant columns. With our research question, we need to check the distribution of column `minutes` and the column `avg_rating`.

We first create a new column `time_period` that classfy each recipe's cooking time to a time period of 30 minutes. So we are able to give a better visualization of the data.
    
|    |     id |   avg_rating |   minutes |   time_period |
|---:|-------:|-------------:|----------:|--------------:|
|  0 | 275022 |            3 |        50 |             2 |
|  1 | 275024 |            3 |        55 |             2 |
|  2 | 275026 |            3 |        45 |             2 |
|  3 | 275030 |            5 |        45 |             2 |
|  4 | 275032 |            5 |        25 |             1 |

We then plot the distribution of recipes in each 30-minute time period

<iframe src="plots/time_period_hist.html" width=800 height=600 frameBorder=0></iframe>

We see that most of the recipes have cooking time under 1 hour. And few takes longer than 2 hours.

<iframe src="plots/rating_hist.html" width=800 height=600 frameBorder=0></iframe>

Over half of the recipe (56.66%) receives avg rating higher than 4.75

## Bivariate Analysis 

In this section, we want to study the association of the two data type we just checked in previous step. 

We create a bar plot and a line graph to see the mean of rating in each time period of 30 minutes
<iframe src="plots/bi_bar.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="plots/bi_line.html" width=800 height=600 frameBorder=0></iframe>

We see that the average rating for recipe in each time period is almost the same, we can say that it has a very weak negative relation but we cannot conlude anything more than that without further analysis.

## Interesting Aggregation

We would like to investigate whether the complexity of recipes grows as time goes. We assess the complexity of a recipe by the number of ingredients and number of steps.

We first create a pivot table to show the mean/median of number of ingredients and number of steps of recipes in each year. 

|   year |   ('mean', 'n_ingredients') |   ('mean', 'n_steps') |   ('median', 'n_ingredients') |   ('median', 'n_steps') |
|-------:|----------------------------:|----------------------:|------------------------------:|------------------------:|
|   2008 |                     8.90138 |               9.66866 |                             8 |                       8 |
|   2009 |                     9.05131 |               9.91732 |                             9 |                       9 |
|   2010 |                     9.01983 |               9.79332 |                             9 |                       9 |
|   2011 |                     9.171   |               9.96863 |                             9 |                       9 |
|   2012 |                    10.0428  |              11.6352  |                            10 |                      10 |
|   2013 |                     9.33759 |              11.5148  |                             9 |                      10 |
|   2014 |                     9.07695 |              11.8915  |                             8 |                      11 |
|   2015 |                    10.6525  |              13.7651  |                            10 |                      12 |
|   2016 |                     9.54545 |              12.442   |                             9 |                      10 |
|   2017 |                    10.1939  |              14.4357  |                            10 |                      13 |
|   2018 |                    12.328   |              18.0354  |                            12 |                      16 |

Then we create a line plot to see the trend
<iframe src="plots/aggre.html" width=800 height=600 frameBorder=0></iframe>

An interesting observation is that there is a drastic increase in the complexity of recipes after 2016. 


## Assessment of Missingness
By looking at the missing value in our original merged dataframe. 

|                |     |
|:---------------|----:|
| name           |   1 |
| id             |   0 |
| minutes        |   0 |
| contributor_id |   0 |
| submitted      |   0 |
| tags           |   0 |
| nutrition      |   0 |
| n_steps        |   0 |
| steps          |   0 |
| description    | 114 |
| ingredients    |   0 |
| n_ingredients  |   0 |
| user_id        |   0 |
| recipe_id      |   0 |
| date           |   0 |
| rating         |   0 |
| review         |  57 |

The column that is possibly NMAR is the `description` column. The missingness is probably due to the decision of the creator of the recipe other than depending on other columns. The recipe creator may think that the detail of the recipe gives enough information of the food it makes, so they think it's unnecessary to give a description. If we collect data on the recipe creator's self-assessment score for the recipe, we may  be able to make it MAR.

## Missingness Dependency
The other column that has missing values is the review column. We suppose that people who doesn't like the recipe are more likely to give reviews than those who like the recipe. So the missingness of review probably depends on the rating column.

Null hypothesis: The mean rating of people who gives a review is the same as the mean rating of people who does not give a review.

Alternative hypothesis: The mean rating of people who gives a review is less than the mean rating of people who does not give a review.

We use permutation test and use difference in mean as our test statistics.

We first look at the distribution of rating for the two group(with & withou review)
<iframe src="plots/missingness_rating.html" width=800 height=600 frameBorder=0></iframe>

We see that for lower score (0-3), the percent of people who give review is higher than the percent of people who does not give review.

The observed test statistics is 0.383. And we performed 1000 permutation of the rating column. The test result shown below. The red line is our observed stats. 

<iframe src="plots/permu_test1.html" width=800 height=600 frameBorder=0></iframe>

By our test, we get p-value of 0.0 which means we reject the null hypothesis that The mean rating of people who gives a review is the same as the mean rating of people who does not give a review. Therefore, we cannot say that the missingness of `review` is independent from `rating`. Thus, the missingness of `review` is probably MAR.

We then want to check if the missingness of `review` depends on number of ingredients of the recipe. We classify recipes into two groups, one group with 10 or more ingredients and one group with less than two ingredients

We first look at the distribution of the two groups 

<iframe src="plots/missing_ingre.html" width=800 height=600 frameBorder=0></iframe>

Null Hypothesis: People choose to not give review regardless the number of ingredients.

Alternative Hypothesis: People tends to not reviewing recipes with more ingredients.

We perform 1000 permutation tests and use TVD as our test statistics.

The distribution of our result is shown below:
<iframe src="plots/permu_test3.html" width=800 height=600 frameBorder=0></iframe>

We get a p-value of 0.68 which means we failed to reject the null hypothesis. Then the `review` column highly likely to be independent from number of ingredients of the recipe (MCAR).

## Hypothesis Test

In this section, we go back to our research question 

**Do the receipt get higher ratings when the cooking time is longer?** 

We classify recipes into two categories, recipes takes longer than 60 minutes and recipes take less than or equals to 60 minutes.We believe this classification is resaonable because recipes takes less than or equals to 60 minutes are usually simple, people will have lower expectation on these recipes and willing to give higher rating.

Null Hypotheisis: People gives same rating for recipes regardless the cooking time.

Alternative Hypothesis: People gives higher rating for recipes that takes less than or equals to 60 minutes

We use permutation test and use difference in mean as our test statistics.

The observed test statistics is 0.113. And we performed 1000 permutation of the rating column. The test result shown below. The red line is our observed stats. 

<iframe src="plots/permu_test2.html" width=800 height=600 frameBorder=0></iframe>

We get p-value of 0.0 for our test which means we reject the null hypothesis that people gives same rating for recipes regardless the cooking time. 

## Conclusion
The result indicates that people are not giving the same rating for food that takes longer to cook and food that takes less time. While we prefer the alternative hypothesis that people gives higher rating for recipes that takes less time, we cannot directly confirm this statement. But it is still reasonable that people are willing to try out and being satisfied by food that takes less time to cook.


