# Understanding Baby Weight Pre-Birth

Albert Um DS Flatiron Cohort 06/22/20<br>

# Project
I am looking to extract coefficients of independent variables that can explain baby weight(pre-birth). Coefficients will be extracted using linear regression with regularization. 



## Structure of Repository
- 'US_Birth_Columns': description of columns and what each value means<br>
- 'Final.ipynb': Lengthy notebook describing my work<br>
- 'Adjustments.ipynb': adding notebooks post graduation <br>
- '/Images': Folder containing plotted images
- 'README.md'

# Business Case
1. Target/Identifying Stakeholders
    - To whom does it matter?
        1. Hospitals: Under weight babies can be healthy even though they are small, however, low-weight babies can have serious problems.
        2. Expecting Mothers: Looking at 
2. Focusing on coefficients that have a large impact in determining weight





# Data
The csv data can be downloaded from [here](http://www.nber.org/data/vital-statistics-natality-data.html). It has been compiled by the National Center for Health Statistics(NCHS) National Vital Statistics System division. The 2018 Natality dataset contains 240 columns with 3.8 million rows. I have reduced the columns to 79 columns and I random sampled 750,000 rows for this project.



# Visualizations

![](Images/WeightDistribution.png)
The distribution of the dependant variable, baby weight. There seems to be a slight left skew as the median is greater than the mean. 


![](Images/WeightAge.png)
The vertical lines represent the 95% confidence intervals.

![](Images/WeightGest.png)
In general, if birth is before the completion of the 37th week than it can be considered "pre-term". Week 37 to Week 42 can be considered normal and births after week 42 can be considered "post-term".


# Model Evaluation
I will use R2 as my metric, as I'm going for a interpretability approach to baby weight. I first split the dataset using sklearn's train_test_split. The X_test will be used to evaluate the model. <br>

## Dummy Regressor
I need a baseline when trying to evaluate my model. The dummy regressor will predict the baby weight mean across all instances.

R2 of Dummy: 0.000
RMSE of Dummy: 587.60

## Lasso
To extract coefficients, I'll use linear regression with l1 regularization. I chose lasso because it acts as feature selection by punishing certain coefficients to zero.
R2 of LassoCV: 0.462
RMSE of LassoCV: 430.91

![](Images/lassocvcoef.png)

The most significant feature was the gestation period. For every one std move of gestation (2.5 weeks), equates to baby weight increase by 227 grams. Considering the "normal" pregnancy length is 38 weeks, this feature alone can explain the weight of the baby.

Also, please note mothers who are white have a positive coefficient value while mothers who are black have negative coefficients.

## Gradient Boosted Decision Tree
Due to the size of the dataset, I was unable to successfully create interactions of features. I will now implement a gradient boosted decision tree because decision trees are inherent feature engineers. The boosted decision tree takes what was learned from the previous model to further tune its predictors. In short,the gbdt results can be interpretted as a linear combination of all the trees. 
For this project, I lowered, the tree depth to 4 for interpretability.


Image of the first tree:
![](Images/gbdt0.png)

The model takes the results from the first tree and builds on top of it creating the second tree, then third, until the loss (in this case rmse) can no longer be lowered on the validation set.

Image of the last tree:
![](Images/gbdt999.png)

Because it's exhaustive to look at more than 1000 trees to come to a conclusion. I can just look at the feature importance of gain and splits. Gain represents the information gain after splitting and splits represent the number of times a feature was split.
![](Images/FeatureImportanceGain.png)
<br>

![](Images/FeatureImportanceSplits.png)

Combgest (Combined Gestation Period) is the most important feature for both gains and splits. 






# Results


## Key Insights
Calculating a human baby's weight can be very complex. Nonetheless, we can still try to draw some understanding of what may determine a babies weight. <br>
In a simple world, everything would be normal and even. But unfortunately, many things aren't. Looking back to the coefficients of Prenatal Care, why does the number of monthly checkups determine baby weight? Is prenatal care easily avaiable for all classes or is there some economic hurdle that is creating this difference? This model isn't supposed to create predictions, it's to address findings so that we can eventually break predictors.
