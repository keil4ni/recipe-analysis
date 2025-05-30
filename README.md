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

test
