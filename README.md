# Predicting House Midterm Elections 2018
**Team Members:**
Justin Bassey, Jake Boll, Chris Lewis, Sebastian Schwartz

## Project Goal
The primary goal of this project is to explain the outcomes of the 2018 midterm elections in the House of Representatives. Any piece of information from before election day is fair game to use, but the suggested feature set should include past elections, polling data, and demographic information of congressional districts.

Our team is specifically interested in what demographic data best explains the results of the 2018 midterms. To do so, we sourced congressional district demographic data from American Community Surveys (ACS) on Census.gov, that dates back to 2010. We then merged this demographics data with the House election results from Harvard's Dataverse, as well as the data we scrapped from the American Presidency Project about seats lost and gained during each sitting president's midterm.

## Data Cleaning and Merging
**Trim our features into something useable for regression analysis**

We noticed that candidates outside the major two parties basically never win, so we decided to focus our attention on the two major parties: Democrats and Republicans.

We also needed to merge rows for tickets that were competing in the same election, where district and year are equal, as our predictive task will be to predict the winner. Thus, we assigned boolean values for party, 0 for Democrats and 1 for Republicans, and changed our winner variable from representing the candidate who won to the party that won.

Lastly, we needed to transition some of the demographic variables such as sex and ethnicity into percentages instead of absolute numbers. We did this by dividing the absolute values by the total population values. Otherwise any normalization applied across the whole data set won't account for differences in population between elections.

## Results from EDA
Given that our data was sourced and scrapped from multiple sources, and was often improperly formatted, we had to perform extensive cleaning, and manipulation to wrangle the data into a usable format for our modeling. Nonetheless, we were still able to extract some initial insights from an exploratory data analysis.

Primarily, we were able to see how each state has traditionally voted over the past 40 years, and how the distribution of seat changes in the house varies depending on the party of the sitting president.

![Average Votes by State](https://github.com/Bassatron/housemidterms2018/blob/master/figures/Party_Voting_Averages_by_State.png?raw=true)

![Average Votes by State](https://github.com/Bassatron/housemidterms2018/blob/master/figures/Distributions_of_Change_in_House_Seats.png?raw=true)

## Models
### Exploratory Baseline Models
**Linear Model**

We produced a simple exploratory linear regression to get a sense for what predictors would provide the most influence on the results. After producing this exploratory regression, we employed bootstrapping to determine p values for each of our predictors. This allowed us to determine quantitatively which predictors were statistically significant in a linear regression, which allowed us to get a sense of which predictors will be the most important for us as we moved towards more advanced models.

We were able to determine that many of our predictors were not statistically significant. To look into this further, we bootstrap across many samples of the data to produce a more accurate picture of which predictors are significant in our linear regression.

**Ridge Model**

We use a Ridge linear regression model to further get a sense of which predictors were influential in a regression. Ridge regression provides protections against overfitting to the train data by penalizing excessively large coefficients. This increases the predictive accuracy on the test data, and provides coefficients that we can use to more accurately predict the importance of each predictor. We were only able to achieve accuracy of approximately 33% with the ridge model. Only being able to correctly predict the outcome of midterm elections in 33% of midterm elections is not particularly useful and demonstrates the need for more advanced models.

**Lasso Model**

We use a Lasso linear regression to try perform some variable selection on the data. Lasso regression is notorious for its tendency to prefer the removal of insignificant predictors. Thus, by using Lasso regression, we can get an even better sense of which predictors are effectively irrelevant. We discarded those predictors and focused on the remaining ones. Similarly to the ridge model, the lasso model only achieved 33% classification accuracy and solidified the need for more nuanced models.

### Random Forests

After creating basic models to get a general sense of the significance and direction of the predictors, we created more nuanced models to better predict the 2018 midterm results. The first of these models is a Random Forest Classifier. In order to better tune our Random Forest to the data, we tuned the parameters that control the number of trees in the model and the max depth of those trees, until we arrived at the model that recorded the best accuracy on the test data. We found that approximately 65 trees of depth 10 generated the most reliable model, which had 82.0% accuracy.

The bar graph below shows how frequently each of the predictors was used as the most important split for a given tree. We can see that Total population, president_party and seats to defend are never the most important split as those numbers would be the same across all districts for a given year.

![Random Forest Top Predictors](https://github.com/Bassatron/housemidterms2018/blob/master/figures/rf_top_preds.png?raw=true)

### Boosting

Continuing with our more advanced modeling, we created a boosting model through Ada Boost Classifier. The boosting model we employ generates successive Decision Tree Classifiers that outweigh the data points that the previous model struggled with. Thus through multiple iterations we can develop a model that performs better on the challenging data points and thus performs better overall. In order to tune our boosting model, we toggled the base depth of the underlying Decision Tree Classifiers as well as the learning rate which controls the degree to which a model will outweigh the challenging data points from the previous model. The graph below shows the accuracy of our best tuned boosting model on the 2018 midterm test data. As you can see in the graph, the model improves significantly in accuracy during the initial iterations, reaches peak accuracy, and then begins to excessively focus on the challenging data and lose accuracy. We reached our maximum classification accuracy of about 84.5% around approximately 60 iterations of boosting.

![Boosting Iterations](https://github.com/Bassatron/housemidterms2018/blob/master/figures/boost_test_150.png?raw=true)

The graph below also shows classification accuracy by iteration, but includes the accuracy on the train data to demonstrate how the possible dangers of overfitting increase significantly with increased iterations.

![Boosting Accuracy](https://github.com/Bassatron/housemidterms2018/blob/master/figures/boost_both_150.png?raw=true)

### Neural Networks

Our final model that we tested was a Neural Network. We believed that it would be able to properly model such a complex problem; however, it would have the drawback of losing interpretability from the decision tree ensemble methods. When testing the neural network we tried many different configurations of number of layers, size of hidden layers, regularization, and activation functions. The best configuration that we ended with was 3 hidden layers, each with 100 nodes, and using a ReLu activation function with L1 regularization. When training the net we had a 30% validation split with 2000 epochs and used batches in size of 64. The graph illustrates the accuracy and loss over the training and the final accuracy on the net was right around the ensemble methods. We believe with more data it could keep increasing; however, we decided not to use data augmentation due to the variability in voters and not wanting to create bad data.

![Neural Network Training](https://github.com/Bassatron/housemidterms2018/blob/master/figures/neuralNet_train.png?raw=true)

## Interpretation

From our modeling analysis we have determined that the most effective models in predicting the results of the 2018 midterm elections were the boosting model and the artificial neural network, whose classification accuracy around 85%. This result was dramatically better than the 33% accuracy achieved by the our baseline linear model. We found the ethnicity variables to be the most effective predictors, while the sex and age variables were also strong predictors.

## Conclusion

From our analysis, we learned the importance of diligence when sourcing and cleaning data, how isolating the important features and data points dramatically increases the performance of any model using that data. We were able to tune our best models to correctly predict the winner in approximately 85% of districts nationally in the 2018 midterm elections. Nevertheless, our 85% accuracy leaves significant room for future improvement and highlights the challenge of predicting election results through the lens of demographics. 
