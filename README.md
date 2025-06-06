# Fast Nutritious Recipes for College Students!
Final Project for DSC80 at UC San Diego

Project Title: Analyzing the relationship between cook time and food composition

# Introduction

**Welcome to Fast and Nutritious recipes, from college students for college students!**

Throughout college, we have come to understand the importance of quick and easy meals that help us stay energetic and nourished, catered to body & mind-specific needs. This motivated us to think about how we can use this dataset, Recipes and Ratings that contains critical information such as nutrition, cook time, number of steps and more for a variety of recipes. Through our project and website, we have tried to find correlations and patterns between different nutritional components and cook time for various recipes, to try to predict the cook time based on composition of nutrients (mainly protein and calories) in each meal. 

The dataset that we use here contains 234,427 rows (woah!), and 26 columns, out of which we focus on the following columns (explanations based on the recipes and ratings dataset):


| Columns     | Description |
| ----------- | ----------- |
| name        | Recipe name |
| minutes     | Minutes to prepare recipe |
| contributor_id | User ID who submitted this recipe |
| nutrition   | Nutritional information which we used to derive the following columns from (as integers): protein, calories, total fat, sugar, sodium, saturated fats,carbs|
| n_ingredients | Number of ingredients |
| ratings | Rating given to each recipe |


Since this dataset had information from two different datasets, we first pooled all of our information through merging the two dataframes we were given. 

The first dataset, ***Recipes***, had the following information: 


|Column          | Description                                                                                                                 |
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
| `name`         | Recipe name                                                                                                                 |
| `id`           | Recipe ID                                                                                                                   |
| `minutes`      | Minutes to prepare recipe                                                                                                   |
| `contributor_id` | User ID who submitted this recipe                                                                                         |
| `submitted`    | Date recipe was submitted                                                                                                   |
| `tags`         | Food.com tags for recipe                                                                                                    |
| `nutrition`    | Nutrition info in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV = “percentage of daily value” |
| `n_steps`      | Number of steps in recipe                                                                                                   |
| `steps`        | Text for recipe steps, in order                                                                                             |
| `description`  | User-provided description  

The second dataset, ***Ratings***, had the following information:

| Column        | Description             |
|---------------|-------------------------|
| `user_id`     | User ID                 |
| `recipe_id`   | Recipe ID               |
| `date`        | Date of interaction     |
| `rating`      | Rating given            |
| `review`      | Review text             |



# Data Cleaning and Exploratory Data Analysis

We first performed a left merge to include all of the Ratings information into recipes by aligning the recipe ID number. Next, in the merge dataset, we filled all the recipes without a rating with np.nan instead of leaving it as a 0. We performed this since we did not think that it is fair to assume recipes without ratings as bad recipes (ie, rating 0 that doesn’t exist on the 1-5 scale), and so replaced it with nan values. We then wanted to have an idea of the overall average rating for each recipe, and so computed recipe wise ratings, by grouping it by the ID. We ensured to add this to our dataset as a new column (‘avg_rating’) so that this could be used for downstream analysis. 

Once we had our overall merged dataframe, we cleaned it through the following steps:

- First, we dropped the `recipe_id` column, since it was duplicated with `id`. One of the rows in this dataframe had a `NaN` value for `recipe_id`. However, many of the other columns also had `NaN` values for the same row, and hence lacked a lot of information and would not be useful for our analysis, and hence we dropped it.
- To maintain consistency and readability, we renamed `id` to `recipe_id`, so that it is not confused with `contributor_id`.
- We then converted both ID columns to strings (`str`), since they are not mathematically informative, but were rather identifiers.
- We then created a `date` column that refers to the date that each recipe was added, in the datetime format. We extracted this information from the `submitted` column, and dropped the `submitted` column to avoid redundancy.
- We then split the `nutrition` column to make 7 new columns as mentioned in our introduction so that we could isolate and individually analyze the nutritional components.
  - We also converted all of these values to `float` from `str` so that we can perform mathematical operations on them.
- We then found outlier recipes that were irrelevant and not depicting food items, and hence dropped them.


## Univariate Analysis

For our univariate analysis, the variable we focused on was the `ingredients` column, which lists all the ingredients required to make a particular recipe. First, we began by filtering the recipes dataset to create a subset containing only the top 25% of recipes based on calorie count. We then cleaned the `ingredients` column by removing brackets, quotes, and commas, splitting the springs into individual ingredients and exploding them into separate rows for accurate counting. This allowed us to calculate the frequency of each ingredient in high-calorie recipes and visualize the 30 most common ones in the final pie chart as seen below:


<iframe
  src="assets/univariateplot.html"
  width="720"
  height="540"
  frameborder="1"
></iframe>


## Bivariate Analysis

The scatter plot we created for this bivariate analysis explores the relationship between recipe calories and cooking time, with both axes log-transformed for better visual interpretation. While there is significant spread, the slight upward slope of the trendline suggests a weak positive correlation - higher-calorie recipes tend to take slightly longer to prepare.






