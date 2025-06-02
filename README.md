# Recipe Length Analysis
Authors: Casey So & Keilani Li

## Introduction
When looking for a recipe online, one of the first things people notice besides from the ingredients is how long it takes to cook. Some users are looking for quick meals they can prepare in under 30 minutes, while others are willing to invest time in more complex dishes. But does the time required to cook a recipe actually affect how well it's rated?

This project explores the connection between cooking time and other factors such as user ratings or the number of steps in a recipe. The goal is to find out whether recipes that take longer to make generally receive better ratings, or if users prefer faster, simpler options. To do this, we will be working with a dataset of recipes that includes details like total cooking time, ingredients, steps, and user ratings.

By analyzing these variables, we want to see if there's a pattern, do people reward effort with higher ratings, or do they value convenience more? The results might help explain what makes a recipe more appealing to home cooks, and whether time investment is actually reflected in how satisfied users are with the outcome.

### Datasets

We are analyzing two datasets from [food.com](https://www.food.com/), containing recipes and user ratings posted between 2008 and 2018. These datasets were originally compiled for a research paper on recommender systems titled "Generating Personalized Recipes from Historical User Preferences" by Majumder et al.

The first dataset, ``recipes``, includes 83,782 entries where each entry is a recipe. The dataset contains 10 columns that capture various attributes of each recipe, such as:

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

## Data Cleaning and Exploratory EDA

continue here

### Univariate Analysis
continue

### Bivariate Analysis

### Interesting Aggregates

## Assessment of Missingness
Not every recipe in the dataset has a rating, so it’s important to think about why some ratings are missing.

In theory, there are a few possible reasons:

Completely random (MCAR): like a glitch that caused some ratings not to be recorded. That’s unlikely here.

Related to other stuff we can see (MAR): for example, maybe people are less likely to rate really long or complicated recipes.

Related to the rating itself (NMAR): like someone trying a recipe, not liking it, and deciding not to leave a review.

Missing by design: where it is a choice to not have a rating

In this case, it’s most likely that the missing ratings are Not Missing At Random (NMAR). People are generally more likely to leave a review if they either loved a recipe or really disliked it. If a recipe was just okay or they didn’t finish making it, they might not rate it at all. So the missing ratings might actually reflect lower satisfaction, we just don’t see it and are unable to confirm it.

(show graph here)

### Missingness Dependency
Null Hypothesis (H₀): The average cooking time is the same for rated and unrated recipes.

Alternative Hypothesis (H₁): The average cooking time is different for rated and unrated recipes.

(show graph here)

After running a permutation test, we have concluded that recipes without ratings take a lot longer to make compared to recipes that do have ratings. This tells us that longer recipes are less likely to get rated. So the missing ratings probably aren't random. Maybe people just don’t finish them, or don’t feel like leaving a review after spending a long time cooking.

### Handling Missingness

Since our project will be using ratings, we will be removing the recipes that do not have a rating. This will reduce the missingness in average rating and prevent our data from being skewed.

(show result here)

## Hypothesis Testing

### Null Hypothesis
The average rating of long recipes is less than or equal to the average rating of short recipes.

### Alternate Hypothesis
The average rating of long recipes is greater than the average rating of short recipes.

### Test Statistics
Difference of mean ratings between long recipes and short recipes

### Defining
Long recipes: greater or equal to 60 minutes

Short recipes: less than 60 minutes

Based on the analysis, there is no evidence to support the claim that recipes with longer cooking times (60 minutes or more) have higher ratings than those with shorter cooking times. The observed data shows that long recipes tend to have slightly lower average ratings compared to short recipes. The one-sided permutation test yielded a p-value of 1.0, indicating that the observed difference is not consistent with the hypothesis that long recipes are rated higher. Therefore, cooking time does not appear to positively influence user ratings in this dataset.

## Framing a Prediction Problem
In our initial hypothesis testing, we were unable to find a statistically significant relationship between the length of a recipe and its rating. Therefore, we concluded that it would not be appropriate to create a model that predicted a recipe's average rating based on it's length in minutes.

Our new plan is to **predict how long a recipe will take to finish** using a **regression** model. We decided to approach this as a regression problem rather than classification because it involves continuous values, so it would be tedious to treat each numeric value as a category. In particular, we will be utilizing a simple linear regression model to solve our problem. Compared to other regression models, (i.e decision trees) simple linear regression is easier to interpret and it makes more sense to find a linear relationship between these two variables. 

We will be evaluating our model with the $R^2$ value, also known as the coefficient of determination. We decided to use this value because it can be used to explain how much of variation in recipe's duration is explained by the number of steps it has. Additionally, the square-root of $R^2$ gives us the correlation coefficient for our model which helps measure the strength and direction between a recipe's duration and the number of steps it has.

At the time of prediction, we will utilize the dataset we've created which contains all of the columns from the ``recipes`` and ``interactions`` files—all of which are related to their corresponding recipes and are referenced in the Introduction. To train our model, we will heavily rely on the review and rating columns, both of which originally come from the ``interactions`` csv file.

## Baseline Model
Write something

## Final Model 
Write something

## Fairness Analysis
Write something
(add graph)
When attempting to predict minutes based on number of steps, we can see that there is a major discrepency between the 2. While short recipes that are under 60 minutes have a mean absolute error of around 13 minutes, longer recipes 60 minutes and above have a mean absolute error of about 35 minutes. This means that the prediction is not as accurate for longer cooking times rather than short ones, which may be caused by more complex steps, longer cooking times, or other factors.
