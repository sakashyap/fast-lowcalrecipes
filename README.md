# Fast Nutritious Recipes for College Students!
Final Project for DSC80 at UC San Diego

Project Title: Analyzing the relationship between cook time and food composition

# Introduction

**Welcome to Fast and Nutritious recipes, from college students for college students!**

Throughout college, we have come to understand the importance of quick and easy meals that help us stay energetic and nourished, catered to body & mind-specific needs. This motivated us to think about how we can use this dataset, Recipes and Ratings that contains critical information such as nutrition, cook time, number of steps and more for a variety of recipes. Through our project and website, we have tried to find correlations and patterns between different nutritional components and cook time for various recipes, to try to predict the cook time based on composition of nutrients (mainly protein and calories) in each meal. 

The dataset that we use here contains 234,427 rows (woah!), and 26 columns, out of which we focus on the following columns (explanations based on the recipes and ratings dataset):

| Columns | Description |
| ----------- | ----------- |
| name | recipe name |
| minutes | Minutes to prepare recipe |
| contributor_id | User ID who submitted this recipe |
| nutrition | nutritional information which we used to derive the following columns from (as integers): protein, calories, total fat, sugar, sodium, saturated fats,carbs|
| n_ingredients | number of ingredients |
| ratings | rating given to each recipe |




Since this dataset had information from two different datasets, we first pooled all of our information through merging the two dataframes we were given. 
The first dataset, Recipes, had the following information: 

