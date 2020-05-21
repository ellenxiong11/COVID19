# COVID-19 County-level Predictions

## Abstract
Through demographic and case time-series data regarding COVID-19 infections at the county level, we use linear regression to look for the most influential factors in predicting the number of COVID-19 daily cases. We first frame the questions, then conduct EDA which informs our data cleaning. We then perform feature selection and linear regression model to find the factors that lead to the lowest rmse. Lastly, we lay out some reflections from the project and future directions for COVID-19 predictions. 

## Question Framing
We set out to answer these two questions:

    Given past data, can we predict the number of confirmed coronavirus cases in the next week of a US county?

    What factors would help us predict the number of cases in a county?

As some states gradually open up their operations, it is critical to have accurate predictions regarding the number of coronavirus cases to help officials determine when and how to reopen the economy and society. Our two questions will use time-series data by county and county-level demographic information to attempt to make predictions for future cases. We hope to use the factors available to us to predict the number of cases in each county on 4/18/20, the latest date that we have data for.

## EDA
We are given data at the county level. In addition to time-series data on the number of confirmed cases, we have demographic data such as people’s underlying conditions, as well as county policies such as stay at home mandates and federal travel bans.

We began by examining the distribution of the number of coronavirus cases on 4/18/20 through a histogram. This distribution plot illustrates that the distribution is right-skewed and that there are numerous clear outliers in the data. Then, we examined a boxplot, which illustrates that a big portion of the number of cases is below 55, with almost all numbers under 130. 

## Data Cleaning
As a result of EDA, we removed outliers from the data, such as some counties in New York, in order to make our visualizations clear and our model more accurate. If we included outliers such as New York, since the numbers are significantly higher than the data, our model might be skewed. We set an upper bound of 10000 cases and removed counties with more than 10000 cases from our data set through a remove outlier function defined in the notebook. 

Then, we only took in counties within the 50 US states, through filtering by State FIP from 1-56. We subsequently filled in N/A using the fillna() function in order to conduct feature engineering and linear regression, since we believe that 0 would least negatively impact our model’s predictions and minimize data adjustment, so we can focus on working with the model. We then extracted the relevant dates from time_confirmed to model our data, which include the target date of 4/18/20 as well as other data points to calculate weekly and monthly growth rates. The next step we took was converting the county FIPS from type object to int, then merged the two tables (time-series data and county-level information) on the county FIPS. Using the relevant dates 4/11/20 and 3/11/20, we were able to calculate the weekly and monthly growth. Lastly, we split the data into training and test sets, with a training to test set size ratio of 70% to 30%. Now, the data is ready to be used.

## Exploring Associations
Next, we visualize the association between the number of cases and the “Rural-Urban Continuum Code”, which is a classification that distinguishes metropolitan counties by the size of their metro area, and nonmetropolitan counties by the degree of urbanization and adjacency to metro areas. 

This box plot shows that there is a clear association between the level of urbanness and ruralness of a county and its number of cases. It seems like urban counties with high population size and low seclusion have more cases than rural counties with low population size and high seclusion.

Next, we explored the association between the population and the number of cases. There seems to be a slight linear association between the two variables, though there are sparsely scattered outliers in the data. These two graphs informed our determination in models to include certain variables.

## Methods and Experiments
We utilize linear regression models to conduct feature engineering in order to look for factors that best predict the number of daily cases, using the cases on 4/18/20 as the target. We defined a root mean square error function, which found the rmse of our linear model predictions. We use linear regression to allow us to predict a value and take into account many factors at once. We can select the features that minimize rmse and conduct feature engineering to potentially alter some of the features. We assume that the linear regression model is the best way to fit the data since we do not see nonlinear relationships between the regressors and the target variable. Furthermore, we assume that for now, the features we have are at least adequate and relevant as inputs in producing a prediction model for daily cases. Lastly, we assume homoscedasticity, that the variance in the residual is the same for all values in the regressor.

We additionally attempted an SIR model, which is a well-known model in estimating the spread of pandemics. It compartmentalizes the population into three components: susceptible, infected, and recovered. We tried the approach in the further analysis section, where we began with 0 recovered individuals, a contact rate of 0.21 for regular counties and 0.12 for counties with stay at home policies, and a recovery rate of 0.070186 by dividing the total number of recovered cases by the total number of confirmed cases in the US. After carrying out the derivation, we found that the rmse was 162.96, which was higher than that of the linear regression model. This may be because we had no county-level data on recovery rate and the contact rates may be slightly inaccurate. We also do not consider other underlying factors and the counties’ policies that may affect the infection rate. Therefore, we decided to go with the linear regression model for the purposes of this analysis.

In the linear regression model, we defined a process data function that selected columns to serve as predictors, then created training and test data based on these predictors and the response variable of daily cases. Then, we fit our linear model using the training data and applied the model to both the training and test data, then calculated the rmse of both. Lastly, we examine the residuals to confirm that they are randomly scattered and not correlated with the number of cases.

## Analysis of Results
Overall, the rmse was low for both the training and test sets. The training rmse was 54.11 while the test rmse was 93.63. Therefore, our set of factors is capable of predicting the number of daily coronavirus cases. The factors include previously mentioned ones that have strong correlation, such as “PopulationEstimate2018” and “Rural-UrbanContinuumCode2013”, in addition to demographic factors such as diabetes percentage. Moreover, policies also seem to have an effect as we also included factors such as stay at home as well as Medicare enrollment. 

Residuals also seem to be relatively randomly scattered above and below the regression line, barring a few outliers. The linear regression model was mostly accurate due to the low rmse. However, using a linear regression may not address all the complexities of a disease as it does not take into account the contact rate, recovery rate, as well as other factors that could influence the spread of the virus. Furthermore, the regression produced negative predictions, which are impossible in real life.

## Additional Reflection
The Rural-Urban Continuum Code 2013 was interesting, as we initially did not know it existed before conducting EDA. It resulted in better predictions. Moreover, the stay at home indicator helped our model more accurately predict the daily case numbers, since it is a good indicator of the strength of policy solutions. Thirdly, MedicareEnrollment had an effect, potentially influencing how and who were getting tested and treated. Lastly, weekly and monthly growth rates significantly improved our predictions, as illustrated below.

      



Surprisingly, the percentage of smokers did not help reduce our rmse by much, despite news reports that smoking had an effect on getting infected.


We found it challenging to figure out how to deal with the N/A data since we needed to find a way to reasonably incorporate it in our regression without it negatively impacting our analysis. Furthermore, each column had different data types so we had to convert them to the same type. Lastly, choosing which features to include was time-consuming, as there were many factors to pick from. Feature selection and feature engineering were challenging aspects in our modeling.


Since we assume linearity in our model, our model could be incorrect as the data could turn out to not be linear. The coronavirus is a complex disease and its rate of spread could increase or decrease based on a variety of factors. Furthermore, we did not take into account the death rate and recovery rate in our predictions. Our model could potentially also be prone to noise and overfitting. Moreover, filling in N/A with 0 might have negatively impacted the model. Lastly, the demographic data is based on estimates from the census in 2010 or even older data; the predictors might be out of date.


We faced ethical dilemmas in potentially stereotyping with certain data. We conducted feature selection based on our own biases. We recognize that this bias exists despite our best efforts, but nevertheless this posed an ethical dilemma with our prediction model.


Additional data regarding the recovery rate by county would strengthen our prediction model and allow us to include more variables in our set up, as well as make our assumptions more solid. Additional factors such as more up to date information on behavioral patterns could also potentially improve the prediction model and allow us to test whether behavioral changes could be linked with a change in daily cases.


We encountered ethical concerns via privacy issues when collecting more detailed sensitive medical and demographic data, such as underlying medical conditions and financial situations. Although this is data at the aggregate level, there is potential for abuse. We might address these concerns by making sure we don’t directly refer to individuals to ensure privacy. Furthermore, we would only be using this dataset to make predictions for this particular project without nonconsensually using it for another purpose to prevent decontextualization.


## Further Discussion

We explored the SIR model at the end of the project. Although we got a higher rmse, it opens the door to try other types of more complex models to use for predicting daily cases. Furthermore, we can explore data from other countries and use more recent data to make these predictions.

We were surprised to see that certain socioeconomic conditions impacted numbers more so than underlying conditions, illustrating that this virus affects people of different socioeconomic classes differently. In the future, we hope to find additional datasets with more socioeconomic data to join with COVID-19 datasets to further explore the correlation between the two.

