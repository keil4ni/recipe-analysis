# Recipe Length Analysis
Authors: Casey So & Keilani Li

## Introduction
When looking for a recipe online, one of the first things people notice besides from the ingredients is how long it takes to cook. Some users are looking for quick meals they can prepare in under 30 minutes, while others are willing to invest time in more complex dishes. But does the time required to cook a recipe actually affect how well it's rated?

This project explores the connection between cooking time and other factors such as user ratings or the number of steps in a recipe. The goal is to find out whether recipes that take longer to make generally receive better ratings, or if users prefer faster, simpler options. To do this, we will be working with a dataset of recipes that includes details like total cooking time, ingredients, steps, and user ratings.

By analyzing these variables, we want to see if there's a pattern, do people reward effort with higher ratings, or do they value convenience more? The results might help explain what makes a recipe more appealing to home cooks, and whether time investment is actually reflected in how satisfied users are with the outcome.

### Datasets

We are analyzing two datasets from [food.com](https://www.food.com/), containing recipes and user ratings posted between 2008 and 2018. These datasets were originally compiled for a research paper on recommender systems titled "Generating Personalized Recipes from Historical User Preferences" by Majumder et al.

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

With our datasets, we can investigate whether the duration of recipes have a positive impact on recipe ratings. The first step was to classify what is considered a "long" recipe or a "short" recipe. We believed there are more people that look for short recipes to follow for time convenience, so we decided that a **short** recipe would be **less than 60 minutes** long while a **long** recipe would be involve all other recipes, that is, **at least 60 minutes** long. To make it easy to distinguish between short and long recipes, we created an additional column that classifies a recipe by its duration called ``'recipe_type'``. We will heavily rely on this column, along with other relevant columns—``'rating'``, ``'minutes'``—in the dataset to answer our question.

## Data Cleaning and Exploratory EDA

talk abt cleaning steps + why we cleaned like that

show clean df

### Univariate Analysis
In this section we will be conducting univariate analysis, which is looking at a single variable in the dataset.

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
Null Hypothesis (H₀): The average cooking time is the same for rated and unrated recipes.

Alternative Hypothesis (H₁): The average cooking time is different for rated and unrated recipes.

<iframe src = "assets/missing-permutation.html" width = "800" height = "600" frameborder = "0"></iframe>

After running a permutation test, we have concluded that recipes without ratings take a lot longer to make compared to recipes that do have ratings. This tells us that longer recipes are less likely to get rated. So the missing ratings probably aren't random. Maybe people just don’t finish them, or don’t feel like leaving a review after spending a long time cooking.

### Handling Missingness

Since our project will be using ratings, we will be removing the recipes that do not have a rating. This will reduce the missingness in average rating and prevent our data from being skewed.

## Hypothesis Testing

**Null Hypothesis:** The average rating of long recipes is less than or equal to the average rating of short recipes.

**Alternate Hypothesis:** The average rating of long recipes is greater than the average rating of short recipes.

**Test Statistic:** Difference of mean ratings between long recipes and short recipes

Key definitions: 
Long recipes: greater or equal to 60 minutes

Short recipes: less than 60 minutes

<iframe src = "assets/hypothesis_test.html" width = "800" height = "600" frameborder = "0"></iframe>

#### Conclusion of Permutation Test
Based on the analysis, there is no evidence to support the claim that recipes with longer cooking times (60 minutes or more) have higher ratings than those with shorter cooking times. The observed data shows that long recipes tend to have slightly lower average ratings compared to short recipes. The one-sided permutation test yielded a p-value of 1.0, indicating that the observed difference is not consistent with the hypothesis that long recipes are rated higher. Therefore, cooking time does not appear to positively influence user ratings in this dataset.

## Framing a Prediction Problem
In our initial hypothesis testing, we were unable to find a statistically significant relationship between the length of a recipe and its rating. Therefore, we concluded that it would not be appropriate to create a model that predicted a recipe's average rating based on it's length in minutes.

Our new plan is to **predict how long a recipe will take to finish** using a **regression** model. We decided to approach this as a regression problem rather than classification because it involves continuous values, so it would be tedious to treat each numeric value as a category. In particular, we will be utilizing a simple linear regression model to solve our problem. Compared to other regression models, (i.e decision trees) simple linear regression is easier to interpret and it makes more sense to find a linear relationship between these two variables. 

We will be evaluating our model with the R<sup>2</sup> value, also known as the coefficient of determination. We decided to use this value because it can be used to explain how much of variation in recipe's duration is explained by the number of steps it has. Additionally, the square-root of R<sup>2</sup> gives us the correlation coefficient for our model which helps measure the strength and direction between a recipe's duration and the number of steps it has.

At the time of prediction, we will utilize the dataset we've created which contains all of the columns from the ``recipes`` and ``interactions`` files—all of which are related to their corresponding recipes and are referenced in the Introduction. To train our model, we will heavily rely on the review and rating columns, both of which originally come from the ``interactions`` csv file.

## Baseline Model
Our baseline model will be looking at predicting time of recipe based on the number of steps. In order to do this we are splitting our data into a training and test data set using the features `minutes` and `n_steps`. Then, using mean squared regression we predict using the training data.

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

Based on the linear regression analysis, there is no meaningful relationship between the number of steps in a recipe and its total cooking time. The model produced an R² score of 0.00, which indicates that the number of steps explains none of the variation in cooking time across the dataset. Additionally, the mean squared error was high (645,937.88), suggesting that the model’s predictions are not accurate. This relationship is not reliable due to the poor model fit.

## Final Model 
In order to remedy this dilemma we decided to try and get rid of some of the outliers that may be influencing this model. In order to do this we used IQR to create a lower and upper bound, removing anything that took to much time or too little time. Additionally, added `n_ingredients` to help make the model more accurate.

Since cooking times were skewed, we applied a log transformation to the target variable (minutes) to better meet the assumptions of linear regression.

Afterwards, we use a Random Forest regression model. We evaluated the model by predicting on the test set, transforming predictions back to the original scale, and calculating the Mean Squared Error (MSE) and R² score.

From this our result was:

Mean Squared Error: 472.77

On average, the squared difference between the actual cooking times and our model’s predictions is about 472.77.

R² Score: 0.23

This means our model explains about 23% of the variation in cooking times.

<iframe src = "assets/final-model.html" width = "800" height = "600" frameborder = "0"></iframe>

#### Final Model Conclusion
Our final model was a major improvement from the base model with the MSE decreasing dramatically from 450036.54 to 472.77 and our R-squared value going from 0.00 to 0.23. While the model captures some relationship between the number of steps, number of ingredients, and cooking time, much of the variation remains unexplained, suggesting that other factors also influence cooking time.

## Fairness Analysis
In this section, we will look at whether our cooking time prediction model performs equally across different types of recipes, comparing recipes with shorter cooking times to those with longer cooking times.

<iframe src = "assets/fairness.html" width = "800" height = "600" frameborder = "0"></iframe>

When attempting to predict minutes based on number of steps, we can see that there is a major discrepency between the 2. While short recipes that are under 60 minutes have a mean absolute error of around 13 minutes, longer recipes 60 minutes and above have a mean absolute error of about 35 minutes. This means that the prediction is not as accurate for longer cooking times rather than short ones, which may be caused by more complex steps, longer cooking times, or other factors.
