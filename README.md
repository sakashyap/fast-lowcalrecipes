# Fast Nutritious Recipes for College Students!
Final Project for DSC80 at UC San Diego

Project Title: Analyzing the relationship between preparation time and food composition

# Introduction

**Welcome to Fast and Nutritious recipes, from college students for college students!**

Throughout college, we have come to understand the importance of quick and easy meals that help us stay energetic and nourished, catered to body & mind-specific needs. This motivated us to think about how we can use this dataset, Recipes and Ratings that contains critical information such as nutrition, preparation time, number of steps and more for a variety of recipes. Through our project and website, we have tried to find correlations and patterns between different nutritional components and preparation time for various recipes, to try to predict the preparation time based on composition of nutrients (mainly protein and calories) in each meal. 

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

For our univariate analysis, the variable we focused on was the `ingredients` column, which lists all the ingredients required to make a particular recipe. First, we began by filtering the recipes dataset to create a subset containing only the top 25% of recipes based on calorie count. We then cleaned the `ingredients` column by removing brackets, quotes, and commas, splitting the strings into individual ingredients and exploding them into separate rows for accurate counting. This allowed us to calculate the frequency of each ingredient in high-calorie recipes and visualize the 30 most common ones in the final pie chart as seen below:

<iframe
  src="assets/univariateplot.html"
  width="720"
  height="540"
  frameborder="1"
></iframe>

## Bivariate Analysis

The scatter plot we created for this bivariate analysis explores the relationship between recipe calories and preparation time, with both axes log-transformed for better visual interpretation. While there is significant spread, the slight upward slope of the trendline suggests a weak positive correlation - higher-calorie recipes tend to take slightly longer to prepare.

<iframe
  src="assets/bivariateplot.html"
  width="720"
  height="540"
  frameborder="1"
></iframe>

## Interesting Aggregates

To better understand how nutritional composition varies across recipe ratings, we first applied min-max normalization to all relevant nutrient columns, ensuring comparability across different scales. We then grouped the data by rating (1–5) and plotted the average nutrient values for each group.

While most nutrient levels remain relatively consistent across ratings, a subtle but notable trend emerges with calories: higher-rated recipes tend to have slightly lower normalized calorie values. This suggests that users may prefer recipes that are lighter in calories, potentially indicating a preference for healthier options.



<iframe
  src="assets/aggplot.html"
  width="720"
  height="540"
  frameborder="1"
></iframe>

# Assessment of Missingness 

The data generating process resulted in missingness in the rating, review and description columns. We believe that while rating can indeed be missing at random (say people have not tried the recipe yet), review cannot be missing at random, since it is likely that if a user did not enter a rating, they did not enter a review either. Hence, we think that review can be dependent on the rating. It is additionally dependent on the nature of the recipe and user. If there was nothing that stood out about utilizing the recipe, a user may not feel comfortable publicly expressing their opinion on the website.

Before conducting further analysis, we wanted to understand if this missingness in the description column is due to the values themselves, or due to another column that it could be related to, or MCAR (missing at random). Upon looking deeper, we found a pattern that recipes only from certain contributors were missing a description. 

To test if these missing values were truly linked to certain contributors, we performed a permutation test where we created a new column, `has_description`, that continued True if a description was present in the row, and False if it was a NaN value. There were no empty strings in the description column. On shuffling the `has_description` column, and using TVD as our test statistic to device is there was a significant difference in the proportion of samples with and without description across contributors, we found a p value of 0.0. This is statistically significant on both a 0.05 and 0.01 level, and we can reject the null that the contributor_id is not linked to the missingness of description. The alternative hypothesis is more likely to be, ie, that the `description` missingness is linked to ‘contributor_id’. Since we are not using this information further in our project, we did not choose to impute the values.

The following plot **ADD DESC HERE? MAYBE?**
<iframe
  src="assets/missplot.html"
  width="720"
  height="540"
  frameborder="1"
></iframe>

# Hypothesis Testing

We are interested in the preparation time and we would like to know the different factors that influence it. To identify if the nutrient content would influence the preparation time, we focused first on the calorie content in each recipe. We established a null hypothesis that there is no difference in the preparation time between high and low calorie recipes. Based on studies we found online such as [this](https://www.dshs.wa.gov/sites/default/files/ALTSA/stakeholders/documents/duals/toolkit/Reading%20Food%20Nutrition%20Labels.pdf), we declared any recipe that contains more than 400 calories as a high calorie recipe. We recognize that some recipes can be for food items made in bulk, and in future versions of our project, we plan to address this. Our alternative hypothesis is that the preparation time is dependent on the calorie content in the food. To simulate our null, we use permutation testing to shuffle the time taken to prepare a recipe. Using a test statistic as the mean difference in the preparation time for high and low calorie food, we found a p-value of 0.0. At a significance level of both 0.01 and 0.05, we are able to reject a null hypothesis, and it is likely that the preparation time is dependent on the calorie content. Since we are interested in predicting preparation time based on the components of food, it is helpful to use one such component to identify if there is likely a correlation between the two.


# Framing a Prediction Problem

Here, we predict the normalized time it takes to prepare a recipe based on the nutrition contents of the food. We use a linear regression, and further a lasso regression model to predict the minutes taken based on the nutrition of the recipe. Here, we know the nutrient composition information from our dataframe, and we utilize these values by transforming them to find the best predictor of the preparation time. Our response variable is `minutes` on a log scale, since standardization was necessary to eliminate skewness from outliers. We chose to measure our accuracy using root mean square error, since this is a standard linear regression accuracy measure that we believe reflects the error in our data well. 


# Baseline Model

We build a linear regression model utilizing two of the features that we believe college students care about the most—calories and protein. Both of our quantitative variables needed some preprocessing. First, we encoded protein as a polynomial feature, by testing polynomials from 1 through 9, and found 4 to perform the best. We utilized RMSE to identify the best performing feature. Based on the graph showing our train and test error, we see that there is very little difference between the two, which allowed us to pick 4, since that had the lowest error in both. 

<iframe
  src="assets/baseline1.html"
  width="720"
  height="540"
  frameborder="1"
></iframe>
Next, based on our literature survey, we found that 400 calories is considered the inflection point between low and high calories, and hence binarized the calories column with a threshold at 400. Using these two features, we predicted the minutes to prepare the recipe on a log scale. We find that this model performs moderately well, since the root mean square error is low at close to 1 unit. However, since this is on a log scale, this is still a high error, and we think that more optimization is necessary to increase the accuracy. Additionally, this current model only fits the data on a basic scale without fine tuning the parameters, and we think it is important to consider a model that can evaluate the importance of different features while utilizing it. 

<iframe
  src="assets/baseline2.html"
  width="720"
  height="540"
  frameborder="1"
></iframe>
As shown in the graph above, there are many fast recipes with low preparation times that our baseline model is able to predict. Based on the log preparation times predicted, one can pick a recipe that falls in the lower sections or higher ones to pick a slow or fast recipe. The RMSE for our baseline model was approximately 1.02 for both training and testing erros. Since there was very minute difference between the two, we can say that our model generalizes well to unseen data. 


# Final Model

In our final model, we chose to add other nutrient information, since these are important components of the recipe, and contribute to its preparation method. We also added the number of ingredients to one of our features to test if having information about the number of ingredients contributes to the preparation time. We see that proteins and calories still perform the best, but its performance is improved by utilizing a more improved regression model: Lasso Regression.

We utilize Lasso Regression to ensure that it selects the features that are best for the prediction with lowest RMSE, by dropping the coefficients that do not improve the model significantly. To ensure that we use the best hyperparameter, we iteratively tested 6 alpha values, and found the lowest value across all our testing features to be for alpha = 1 value. Across all our features, we also found that our feature that predicts based on transformed calories performs the best with lowest RMSE value of [0.993,0.982]. The RMSE for transformed calories and proteins was also the same, suggesting that coefficient for proteins is probably being restricted. Similar to our baseline model, the difference between the RMSE values for the training and testing error is very minute, suggesting that our final model generalizes well to unseen data. 

We think that this model performed better as compared to the baseline since the addition of a hyperparameter changed the weightage for each parameter, and prioritized accuracy. We see that as compared to our baseline model, our final has a lower RMSE by 0.027 for the training error and 0.038 for the testing error, for our best performing feature (alpha level 1). 


# Fairness Analysis

To analyze the fairness of our model, we selected to look at the prediction differences in the number of steps. On identifying the mean and median to be about 10 steps, we created two groups so that Group X contains those recipes with ‘low’ steps (<10) and Group Y contains those recipes with ‘high’ steps (>10). Our null hypothesis is that there is no difference in the performance of the model between the low and high recipes, and we simulate this through a permutation test. We shuffle our `high_or_low` column that contains True if it is a low step recipe and False if it is a high step recipe, and calculate the absolute difference between the RMSE. On generating this 500 times, we compare it to the true difference between the RMSEs for high and low step recipes. We find a p value of 0.0, which shows that we can reject our null in favour of our alternative hypothesis that the model does not predict with the same fairness for high and low step recipes. We conclude that this bias may arise from low step recipes that have outliers in the number of minutes taken, since we did not filter for recipes such as baking bread, which have fewer steps, but a large amount of prep time. Additionally, we must consider that the way steps are split up vary from person to person, and some people may compress many steps into a single one whereas some may break every step down for a rather simple recipe. In future iterations of our model, we plan to filter for different types of recipes and build a model that can intake a parameter to specify this information.














