# Recipe Length Analysis
Authors: Casey So & Keilani Li

## Overview
This project explores the relationship between a recipe's cooking time and its user rating and the relationship between cooking time and the number of steps.

## Introduction
When looking for a recipe online, one of the first things people notice aside from the ingredients is cooking time. Some users look for quick meals that can be done in under 30 minutes, while others are willing to invest time into more complex dishes. This raises an important question we have: does the cooking time influence how well it's rated?

This project explores the two relationships related to cooking time. First, we try to look for a significant correlation between cooking time and user ratings. Do users tend to give higher ratings for longer, effortful recipes, or do they value fast and convenient options?

In the second part of our project, we focus on the a component of the recipe itself, that is, the number of steps involved. We specifically explore whether cooking time can be predicted based on the number of steps a recipe has. To do this, we will utilize a linear regression model to evaluate this relationship.

By using recipe datasets that involve total cooking time, steps, and user ratings, and instructions, we plan to uncover patterns in how a recipe's time and complexity relates to quality and preparation. The results may help explain what makes a recipe more appealing to home cooks and whether time investment is reflected in how satisfied users are with the outcome.

In order to explore these two relationships, we will analyze two datasets from [food.com](https://www.food.com/) which contain recipes and user ratings posted between 2008 and 2018. These datasets were originally compiled for a research paper on recommender systems titled "Generating Personalized Recipes from Historical User Preferences" by Majumder et al.

The first dataset, ``recipes``, includes 83,782 entries where each row is a recipe. The dataset contains 10 columns that capture various attributes of each recipe, such as:

|  Column             | Description |
|  -------------------|------------------ |
|  ``'name'``	            | Recipe name |
|  ``'id'``	              | Recipe ID |
|  ``'minutes'``          | Minutes to prepare recipe |
|  ``'contributor_id'``   | User ID who submitted this recipe |
|  ``'submitted'``        | Date recipe was submitted |
|  ``'tags'``             | Food.com tags for recipe |
|  ``'nutrition'``	      | Nutrition information in the form [calories (#), total fat (PDV), <br> sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), <br> carbohydrates (PDV)]; PDV stands for “percentage of daily value" |
|  ``'n_steps'``	        | Number of steps in recipe |
|  ``'steps'``            | Text for recipe steps, in order |
|  ``'description'``	    | User-provided description |
|  ``'ingredients'``	    | Text for recipe ingredients |
|  ``'n_ingredients'``    | Number of ingredients in recipe |

The second dataset, ``interactions``, includes 731,927 entries where each row is a user's interaction with a recipe—typically a review or rating. This dataset helps capture user preferences and engagement over time. The 5 columns included are:

|  Column             | Description |
|  -------------------|------------------ |
|  ``'user_id'``	    | User ID |
|  ``'recipe_id'``	  | Recipe ID |
|  ``'date'``         | Date of interaction |
|  ``'rating'``       | Rating given |
|  ``'review'``       | Review text |

With our datasets, we can investigate whether the recipe durations have a positive impact on its ratings. The first step was to classify what is considered a "long" recipe or a "short" recipe. Based on the assumption that more people look favor short recipes for convenience, we decided that a **short** recipe should take **less than 60 minutes** while a **long** recipe should take **at least 60 minutes** long. For easier distinction, we created an additional column to indicate this classification called ``'recipe_type'``. We will heavily rely on this column, along with ``'rating'`` and ``'minutes'``, to answer our initial question.

## Data Cleaning and Exploratory EDA

Before working with our datasets, it's important to clean invalid or insignificant entries and create additional columns to make the analysis process more efficient. Thus, we have cleaned our data in the following steps:

1. Left merge the ``recipes`` and ``interactions`` datasets together.
   - More specifically, we join them together using the ``id`` and ``recipe_id`` columns to link recipes together with its corresponding user interactions.

2. Fill all ratings of 0 with ``np.nan``
   - This is a crucial step because recipes without reviews may have a default rating of 0. Rating scales typically range from 1 to 5, so many recipes with a rating of 0 may introduce bias in our data. This is not something we want since we will explore the relationship between cooking time and ratings, so we replace these 0 values with NaN.

3. Find the average rating per recipe
   - A recipe might have many reviews, so it is better to look at an overall rating of a recipe instead of each individual review.

4. Drop the following columns
   - ``'id'``: Recipe ID
   - ``'contributor_id'``: User ID who submitted this recipe
   - ``'user_id'``: User ID
   - ``'date'``: Date of interaction
   - We mainly dropped these columns because they are not relevant to our analysis, so there is no use for us to keep them.
  
5. Investigate the longest recipes & drop as needed
   - Recipe ID ``109931`` and ``109932``: [How to preserve a husband](https://www.food.com/recipe/how-to-preserve-a-husband-447963)
   - Recipe ID ``106700``: [Homemade fruit liquers](https://www.food.com/recipe/homemade-fruit-liquers-291571)
   - Recipe ID ``107394``: [Homemade vanilla](https://www.food.com/recipe/homemade-vanilla-425681)
   - We dropped these rows because their cooking times are significantly long, meaning they are also major outliers in our dataset. We looked into the husband preservation recipe and decided that it is not a real recipe because it refers to actual relationships, which is not related to our project. Contrarily, the other two recipes are actual recipes, but take a very long time to make. That is, they both take over 250,000 minutes to complete.

6. Add `recipe_type` column
   - ``recipe_type`` is a column that classifies each recipe as one of two groups: long (≥1 hr) or short (<1 hr). This makes it easier to compare any differences between long and short recipes.

After cleaning, our resulting dataframe is left with 234,425 rows and contains these 15 columns:

|  Column             | Description |
|  -------------------|------------------ |
|  ``'name'``         | Recipe name |
|  ``'minutes'``      | Minutes to prepare recipe |
|  ``'submitted'``    | Date recipe was submitted |
|  ``'tags'``	        | Food.com tags for recipe |
|  ``'nutrition'``	  | Nutrition information in the form [calories (#), total fat (PDV), <br> sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), <br> carbohydrates (PDV)]; PDV stands for “percentage of daily value" |
|  ``'n_steps'``	    | Number of steps in recipe |
|  ``'steps'``	      | Text for recipe steps, in order |
|  ``'description'``	| User-provided description |
|  ``'ingredients'``	| Text for recipe ingredients |
|  ``'n_ingredients'``| Number of ingredients in recipe |
|  ``'recipe_id'``	  | Recipe ID |
|  ``'rating'``	      | Rating given |
|  ``'review'``	      | Review text |
|  ``'avg_rating'``	  | Average rating of recipe |
|  ``'recipe_type'``	| Classifies long vs. short recipes |

As there are many columns, we will showcase the first 5 rows of our cleaned dataframe along with the most relevant columns for our project:

| recipe_id | name                               | minutes | n_steps | rating | avg_rating  | recipe_type   |
|-----------|------------------------------------|---------|---------|--------|-------------|---------------|
| 333281.0  | 1 brownies in the world best ever  | 40.0    | 10      | 4.0    | 4.0         | short (<1 hr) |
| 453467.0  | 1 in canada chocolate chip cookies | 45.0    | 12      | 5.0    | 5.0         | short (<1 hr) |
| 306168.0  | 412 broccoli casserole             | 40.0    | 6       | 5.0    | 5.0         | short (<1 hr) |
| 306168.0  | 412 broccoli casserole             | 40.0    | 6       | 5.0    | 5.0         | short (<1 hr) |
| 306168.0  | 412 broccoli casserole             | 40.0    | 6       | 5.0    | 5.0         | short (<1 hr) |

### Univariate Analysis
In this section we will be conducting univariate analysis, which includes looking at a single variable in the dataset.

#### Rating Distribution

For our first graph, we wanted to look at the distribution of ratings. In the graph below, it is evident that there are more 5 star ratings than any other rating, creating a left-skewed distribution. This also indicates that there are less recipes with lower-ratings than recipes with high ratings.

<iframe src = "assets/rating-distrib.html" width = "800" height = "600" frameborder = "0"></iframe>

#### Short vs Long recipe Distribution

Our analysis below concludes that there are more recipes that take less than an hour to prepare compared to recipes that take an hour or more to make.

<iframe src = "assets/duration-distrib.html" width = "800" height = "600" frameborder = "0"></iframe>

### Bivariate Analysis
In this section we will be looking at graphs that look at two variables
explanation

#### Distribution of long and short recipes' rating
The graph below indicates that there is a left skew for both time durations.

<iframe src = "assets/duration-rating-distrib.html" width = "800" height = "600" frameborder = "0"></iframe>

### Interesting Aggregates
For this section we will be looking at interesting things that we found from our EDA after cleaning the data.

#### Average Number of Minutes per Rating

<iframe src = "assets/avg-minutes-per-rating.html" width = "800" height = "600" frameborder = "0"></iframe>

After pivoting the table to find the average time for each rating, we see that one star ratings had the longest average time of about 99 minutes compared to any other rating. This is interesting as typically longer meals "should" in theory make better meals, however this data contradicts this sentiment as 3 star ratings had the least amount of time at about 87 minutes while 5 star ratings was near 94 minutes average.

#### Number of Recipes based on Recipe Type (short/long) per Rating

<iframe src = "assets/total-recipes-rating.html" width = "800" height = "600" frameborder = "0"></iframe>

In this analysis, we looked at the count of recipes per rating depending on short or long recipes. We found that 5 star reciepes were the most common for both time scenarios. This could be because if the meal was not good people are less likely to rate the recipe. Furthermore, we can tell that there is a larger amount of long recipes that take over an hour to make compared to short recipes that take less than an hour.

#### Average Number of Minutes based on Recipe Type (short/long) per Rating

<iframe src = "assets/avg-minutes-recipe-type.html" width = "800" height = "600" frameborder = "0"></iframe>

In this analysis we, we examined the distribution of the average minutes of each rating between short and long times. From this we see that the average amount of minutes for short ratings are about the same no matter which rating it is given, whereas the average minutes for longer recipes vary more.

## Assessment of Missingness
Not every recipe in the dataset has a rating, so it’s important to think about why some ratings are missing.

In theory, there are a few possible reasons:

Completely random (MCAR): like a glitch that caused some ratings not to be recorded. That’s unlikely here.

Related to other stuff we can see (MAR): for example, maybe people are less likely to rate really long or complicated recipes.

Related to the rating itself (NMAR): like someone trying a recipe, not liking it, and deciding not to leave a review.

Missing by design: where it is a choice to not have a rating

In this case, it’s most likely that the missing ratings are Not Missing At Random (NMAR). People are generally more likely to leave a review if they either loved a recipe or really disliked it. If a recipe was just okay or they didn’t finish making it, they might not rate it at all. So the missing ratings might actually reflect lower satisfaction, we just don’t see it and are unable to confirm it.

### Missingness Dependency
In order to examine the large missingness from `ratings`, we decided to look at two of the other columns (`minutes` and `description`) to see if there is a dependency between them. In order to this, we conducted permutation tests to depcit whether their presense created a change in the ratings 

#### Rating and Cooking Time

Null Hypothesis (H₀): The missingness of ratings is not dependent on cooking time.

Alternative Hypothesis (H₁): The missingness of ratings is dependent on cooking time.

Test Statistic: Difference in mean between recipes with ratings and recipes without a rating

Significance Level: 0.05

<iframe src = "assets/missingness-dep1.html" width = "800" height = "600" frameborder = "0"></iframe>

After conducting a permutation test, we found that our observed difference in average cooking time of missing ratings and not missing ratings is about -64 minutes. 

With a significance level of 0.05, our p-value was 0.00. 

Since the p-value is less than the significance level, we **reject the null hypothesis**. There is evidence that the missingness of ratings is dependent on cooking time. Perhaps users thought the recipe they followed took more time than expected and decided not to rate it.

#### Rating and Descriptions
Null Hypothesis (H₀): The missingness of ratings is not dependent on descriptions.

Alternative Hypothesis (H₁): The missingness of ratings is dependent on descriptions.

Test Statistic: Difference in mean between recipes with ratings and recipes without a rating

Significance Level: 0.05

<iframe src = "assets/missing-dep2.html" width = "800" height = "600" frameborder = "0"></iframe>

After conducting a permutation test to evaluate whether the missingness of a recipe's description had an effect on the missingness of ratings. We found the observed difference to be about -0.0148.

With a significance level of 0.05, our p-value was 0.5570.

Since the p-value is greater than the significance level, we **fail to reject the null hypothesis**. There is not enough evidence that the missingness of results is dependent on recipe descriptions.

### Handling Missingness

Since our project will be using ratings, we will be removing the recipes that do not have a rating. This will reduce the missingness in average rating and prevent our data from being skewed.

## Hypothesis Testing
**Question:**
Do Long Recipes Recieve Higher Ratings?

**Null Hypothesis:** The average rating of long recipes is equal to the average rating of short recipes.

**Alternate Hypothesis:** The average rating of long recipes is greater than the average rating of short recipes.

**Test Statistic:** Difference of mean ratings between long recipes and short recipes

_Key definitions:_ 
Long recipes: greater or equal to 60 minutes

Short recipes: less than 60 minutes

<iframe src = "assets/hypothesis_test.html" width = "800" height = "600" frameborder = "0"></iframe>

#### Conclusion of Permutation Test
Based on the analysis, there is no evidence to support the claim that recipes with longer cooking times (60 minutes or more) have higher ratings than those with shorter cooking times. The observed data shows that long recipes tend to have slightly lower average ratings compared to short recipes. The one-sided permutation test yielded a p-value of 1.0, indicating that the observed difference is not consistent with the hypothesis that long recipes are rated higher. Therefore, cooking time does not appear to positively influence user ratings in this dataset.

#### Justification
- We compared means because the rating variable is quantitative. 
- We conducted a one-sided test because we wanted to know if longer recipes had higher ratings.
- We used a permutation test because it is well-suited to compare group means and is non-parametric 

## Framing a Prediction Problem
In our initial hypothesis testing, we were unable to find a statistically significant relationship between the length of a recipe and its rating. Therefore, we concluded that it would not be appropriate to create a model that predicted a recipe's average rating based on it's length in minutes.

Our new plan is to **predict how long a recipe will take to finish** using a **regression** model. We decided to approach this as a regression problem rather than classification because it involves continuous values, so it would be tedious to treat each numeric value as a category. In particular, we will be utilizing a simple linear regression model to solve our problem. Compared to other regression models, (i.e decision trees) simple linear regression is easier to interpret and it makes more sense to find a linear relationship between these two variables. 

We will be evaluating our model with the R<sup>2</sup> value, also known as the coefficient of determination. We decided to use this value because it can be used to explain how much of variation in recipe's duration is explained by the number of steps it has. Additionally, the square-root of R<sup>2</sup> gives us the correlation coefficient for our model which helps measure the strength and direction between a recipe's duration and the number of steps it has.

At the time of prediction, we will utilize the dataset we've created which contains all of the columns from the ``recipes`` and ``interactions`` files—all of which are related to their corresponding recipes and are referenced in the Introduction. To train our model, we will heavily rely on the review and rating columns, both of which originally come from the ``interactions`` csv file.

## Baseline Model
Our baseline model will be looking at predicting time of recipe based on the number of steps. In order to do this we are splitting our data into a training and Our baseline model will be looking at predicting time of recipe based on the number of steps. In order to do this we are splitting our data into a training and test data set using the features `minutes` and `n_steps`. Then, using mean squared regression we predict using the training data.

Model Type:
- Linear Regression 
  predict cooking time (`minutes`) from number of steps in the recipe *(`n_steps`)

Features:
- Quantitive: 
  `n_steps`
- Ordinal: None
- Nominal: None

Intercept: 35.55

A recipe with zero steps would (theoretically) take about 36 minutes, which is not true realistically.

Slope (minutes per step): 5.70

Each additional recipe step adds about 5.70 minutes on average.

Mean Squared Error: 450036.54

This is huge, which suggests the model’s predictions are very far off on average.

R² Score: 0.00

This means the number of steps has no meaningful linear relationship with the total cooking time in this dataset.

Leaving us with:

`minutes` = 38.02 + 5.74 × `n_steps`

<iframe src = "assets/baseline-model.html" width = "800" height = "600" frameborder = "0"></iframe>

#### Baseline Model Conclusion

Based on the linear regression analysis, there is no meaningful relationship between the number of steps in a recipe and its total cooking time. The model produced an R² score of 0.00, which indicates that the number of steps explains none of the variation in cooking time across the dataset. Additionally, the mean squared error was high (450036.54), suggesting that the model’s predictions are not accurate. This relationship is not reliable due to the poor model fit.

## Final Model
In order to remedy this dilemma, we decided to try and get rid of some of the outliers that may be influencing this model. In order to do this, we used IQR to create a lower and upper bound, filtering out data points that took too much or too little cooking time. 

New Features:

- `log_minutes`: log-transformed `minutes`
  
  Since cooking times were skewed, we applied a log transformation to the target variable (`minutes`). This helps stablize variance and normalizes the distribution.
  
- `complexity`: `n_steps` * `n_ingredients`
  
  This gives a numerical value to represent the complexity of each step based on the amount of ingredients.

Model: RandomForestRegressor

   We evaluated the model by predicting on the test set, transforming predictions back to the original scale, and calculating the Mean Squared Error (MSE) and R² score.

Hyperparameter Tuning with GridSearch CV:

The following list represents the combination of hyperparameters that best optimizes our model.
- `n_estimators` (number of trees): 100
- `max_depth` (prevent overfitting): None
- `min_samples_split`: 5
- `max _features`: `sqrt`

Improvement:
- RandomForestRegressor captures complex interactions (not only linear)
- `n_ingredients` add more predictors that will help make it more accurate
- transforming `minutes` reduces the skew
- taking out outliers, makes predicting more accurate

From this, our result was:

Mean Squared Error: 472.74

On average, the squared difference between the actual cooking times and our model’s predictions is about 472.58.

R² Score: 0.23

This means our model explains about 23% of the variation in cooking times.

<iframe src = "assets/final-model.html" width = "800" height = "600" frameborder = "0"></iframe>

#### Final Model Conclusion
Our final model was a major improvement from the base model with the MSE decreasing dramatically from 450036.54 to 472.74 and our R-squared value going from 0.00 to 0.23. While the model captures some relationship between the number of steps, number of ingredients, and cooking time, much of the variation remains unexplained, suggesting that other factors also influence cooking time.

## Fairness Analysis
To make sure that our model is fair we will conduct a fairness model to ensure that the model predicts the cooking time (`minutes`) from `n_steps` fairly for both short (under 60 minutes) and long (60 minutes or longer) recipes. 

To do this we will be comparing the absolute prediction errors from these two groups to identify if there ia a bias. 

Null Hypothesis: Our model is fair. Its precision for short recipes and long recipes are about the same.

Alternative Hypothesis: Our model is unfair. Its precision forhort recipes and long recipes are different.


<iframe src = "assets/fairness.html" width = "800" height = "600" frameborder = "0"></iframe>

When attempting to predict minutes based on number of steps, we can see that there is a major discrepency between the 2. While short recipes that are under 60 minutes have a mean absolute error of around 13 minutes, longer recipes 60 minutes and above have a mean absolute error of about 35 minutes. This means that the prediction is not as accurate for longer cooking times rather than short ones, which may be caused by more complex steps, longer cooking times, or other factors.
