# Problem Statement

- Since the onset of the COVID-19 pandemic in 2020, Americans, and millennials in particular, have had to reexamine and reprioritize their lives. For many, this resulted in an address change. Twenty-six percent  of American aged 25-40 have relocated during the pandemic, compared to just sixteen percent of the general public. 
- As hopeful future home owners and remote workers depart from America's largest urban centers in search of more comfortable space and affordable housing, it's important they have knowledge of not only the local housing market, but the key factors in determining a home's market value.
- Additionally, appraisers and real estate companies need this insight as they accommodate the increased demand.

# Task

- You've been contracted by Zillow to build a ML model that accurately forecasts home prices in Ames, Iowa given features of the home, the lot and the neighborhood.
- Zillow's plan is to expand this feature nationwide and release it for public use. In the meantime, it will remain in beta while we improve the model and platform.
- Your job is to lay the foundation (no pun intended)


## The data

Our dataset (from [Kaggle](https://www.kaggle.com/c/dsir-712-project-2-regression-challenge/data)) includes 80 columns of home attributes. Besides the home's actual sale price, features include:
- Zoning / property type
- Location
- Land / Lot attributes
- Variety of home quality / condition scores
- Original build/remodel dates
- Utilities
- Etc.

Data types vary, as does the level of subjectivity. Data such as neighborhood and square footage are purely objective, while quality/condition scores may be up to the appraiser's discretion. With that in mind, the range is possible feature possible score is wide.


## The  Process
#### Cleaning / EDA

- Before testing you different models, ensure the feature data is pre-processed and error free.
- Identify the columns as numeric or not. Of the non-numeric columns which are worth engineering or one-hot encoding?
- Identify the list of features appropriate for initial modeling
    - Drop columns with over 100 null values
    - Impute the mean value for columns with only a few nulls
    - Feature engineer (see next paragraph)
    - Assign the cleaned numeric features to a new variable: 'features_df'


#### Reature Engineering

- 'Quality ' and 'Condition' columns are scored on string values ranging from Poor ('po') to Excellent ('Ex'). Convert these values to a numeric range 1 - 5.
- Neighborhood. Since a home's neighborhood is a certain to have a strong correlation to it's value, we'll add a new column calculating each neighborhood's average home sale price.
- Create dummy new dummy columns out of the "Building Type" column which differentiates between single-family homes, townhouses and duplexes.


#### Initial Modeling / Evaluation

- The three models we tested are all linear models coming from the scikit-learn library: Standard Linear Regression, RidgeCV,  LassoCV.
- After scaling the data and running it through each model we observe some clear overfitting. Understandable given the number of features being included.
    - Linear Regression:
        - R-squared training data score: **0.89**
        - R-squared test data score: **0.83**
        - Cross-val scores: **[ 8.76526155e-01 , 8.47271898e-01, -4.79911021e+11,  8.13626415e-01 5.10747322e-01]**
        - Confidence Interval: **+- 383928816881.75**
        
    - RidgeCV:
        - R-squared training data score: **0.89**
        - R-squared test data score: **0.83**
        - Cross-val scores: **[0.87476446, 0.85264051, 0.85614752, 0.8200298,  0.54903676]**
        - Confidence Interval: **+- 0.24**

    - LassoCV:
        - R-squared training data score: **0.89**
        - R-squared test data score: **0.89**
        - Cross-val scores: **[0.86637163, 0.83751991, 0.87975804, 0.85351365, 0.61710012]**
        - Confidence Interval: **+- 0.2**


#### Insights

- The r-squared train and test scores are consistent across all three models. They're also not terrible. However, the cross-val scores are much more varying.
- In each model's list of cross-val scores there is one that is noticeably different than the others. This indicates outliers in the data that our model cannot account for. The linear regression model highlights this well, as it does not apply regularization like the other two models.
- To account for this we'll reduce the number of features used in the model.


#### Feature Reduction
- Identify top features with the strongest correlation to the sale price.
    - After creating a function ('corr_to_score()') that evaluates each model's performance based in the correlation of it's features to the sale price, we identified 0.40 - 0.50 as the ideal minimum correlation rate each feature should have to sale price.
- fter further examining the features that meet that criterion, as well as checking the multicollinearity or our features, we re-ran the models with our newly selected features.


#### Re-evaluation
- The new score are MUCH more consistent, with tighter confidence intervals. Interestingly the train and test r-squared scores are slightly lower.

    - Linear Regression:
        - R-squared training data score: **0.84**
        - R-squared test data score: **0.84**
        - Cross-val scores: **[0.7939811,  0.85703395, 0.84637592, 0.86426172, 0.74936327]**
        - Confidence Interval: **+- .09**
        
    - RidgeCV:
        - R-squared training data score: **0.84**
        - R-squared test data score: **0.84**
        - Cross-val scores: **[0.87476446, 0.85264051, 0.85614752, 0.8200298,  0.54903676]**
        - Confidence Interval: **+- 0.09**

    - LassoCV:
        - R-squared training data score: **0.83**
        - R-squared test data score: **0.84**
        - Cross-val scores: **[0.7966712  0.85342343 0.84035693 0.8623975  0.75714213]**
        - Confidence Interval: **+- 0.8**



## Next Steps / Consideration 
- While our final output answered for the overfitting issue we originally noted, r-squared scores lowered slightly and were below the performance benchmark Zillow would need to begin beta testing. 
- Based on our second set of scores, I would recommend utilizing the Lasso model as it had low variance and the tightest confidence interval.
- Next Steps for improving model performance will include:
    - Consider whether other non-numeric columns could be encoded (as we did with 'quality' and 'condition' columns)
    - Additional dummying of categorical columns
    - Removing multicolinearity from features to widen the range of features used in the model without overwhelming it.
    
    