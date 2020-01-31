## Predicting Midterm Elections

## Team Members: Group 11

Justin Bassey, Jake Boll, Chris Lewis, Seb Shwartz

## Exploratory Data Analysis

- What data are we dealing with?
- How have we explored the data (initial explorations, data cleaning and reconciliation, etc)?
- Visualizations and captions that summarize the noteworthy findings of the EDA.
- A revised project question based on the insights you gained through EDA (specific to data).
- A baseline model.

### Problem Statement and Motivation

The primary goal of this project is to explain the outcomes of the 2018 midterm
elections in the House. Any piece of information from before election day can be used to perform
this analysis, but the suggested feature set should include past elections, polling data, and demographic information of congressional districts.

### Introduction and Description of Data

By creating an accurate model to predict house elections, this can be very beneficial to politicians. By understanding the outlook of their party, politicans can strategically manuever their resources to better their chances. While creating these models can be very benefial, it still comes with challenges such as changing economic and social issues, unpredictability of young voters, and flexibility of the model. By using exploritory data analysis we aim to overcome these challenges and make accurate predictions.


### House District Candidate Data

We sourced raw candidate data detailing the total votes for each candidate in each district in the country going back to 1976. This data also included the party affiliation of each candidate which allowed us to obtain the total votes for each party by district for the last 40 years. We then cleaned the data extensively so that it would be in a useable form to analyze.

### How do states traditionally vote?

For the graph below we looked at the average difference between votes for Democratic candidtats and Republican candidates for a given state, averaged across all of a given state's districts and the years that were available. This allows us to get an initial understanding of a state's political trends.

![Party Voting Averages Image](ChrisLewis0.github.io/Party Voting Averages by State.png)

### House Seat Changes by President

**Are there differences in House seats lost by Democratic vs Republican Presidents?**

It is a commonly understood phenomenom of American politics that the president's party members in congress often suffer in midterm elections. This can be seen in the graph below as Republicans and Democrats both lose around 25 seats in the House of Representatives during midterm elections when their party controls the White House. This general swing against the president's party in congress could indicate that the American public is consistently fustrated with the actions of the majority of presidents.

We wanted to determine if the negative effect to those congressmen in the president's party differed significantly between the two parties. This would allow us to alter the way we understood the effect to a congressmen of being of the president's party, and how we incorporated that effect into our models. In the graph below, we can that while Democrats lose slightly more seats in the midterms when the sitting president is a Democrat and the variance of the seats lost by Democrates is slightly larger, the two parties show generally the same trend of losing about 25 seats when their party's president is in office.

![](ChrisLewis0.github.io/Distributions of Change in House Seats.png)

### Data Cleaning and Merging

**Trim our features into something useable for an exploratory regression**

We noticed that candidates outside the major two parties basically never win, so we decided to focus our attention on the two major parties: Democrats and Republicans.

We also need to merge rows for tickets that were competing in the same election, where district and year are equal, as our predictive task will be predicting the winner.

We then assinged boolean values for party, 0 for Democrats and 1 for Republicans. We changed our winner variable here from representing success to representing the party that won, once we merge the tickets, making the data cleaner and easier to use.

We needed to transition some of the demographic variables such as sex and ethnicity into percentages instead of absolute numbers. We did this by dividing the absolute values by the total population values. Otherwise any normalization applied across the whole data set won't account for differences in population between elections.

## Related Work

**Data Collection**

We got our congressional district demographic data from American Community Surveys (ACS) on Census.gov, dating back to 2010. We then merged this demographics data with the House election results from Harvard's Dataverse, as well as the data we scrapped from the American Presidency Project about seats lost and gained during each sitting president's midterm.

## Modeling the Data

### Exploratory Baseline Models

**Linear Model**

We produced a simple exploratory linear regression to get a feel for what predictors would provide the most influence on the results, and thus would be the most useful predictors. After producing this exploratory regression, we employed bootstrapping to determine p values for each of our predictors. This allowed us to determine quantitatively which predictors were statistically significant in a linear regression, which allowed us to get a sense of which predictors will be the most important for us as we moved towards more advanced models.

We were able to determine that many of our predictors were not statistically significant. To look into this further, we bootstrap across many samples of the data to produce a more accurate picture of which predictors are significant in our linear regression.

**Ridge Model**

We use a Ridge linear regression model to further get a sense of which predictors were influential in a regression. Ridge regression provides protections against overfitting to the train data by penalizing excessively large coefficients. This increases the predictive accuracy on the test data, and provides coefficients that we can use to more accurately predict the importance of each predictor. We were only able to achieve accuracy of aproximately 33% with the ridge model. Only being able to correctly predict the outcome of midterm elections in 33% of midterm elections is not perticularly useful and demonstrates the need for more advanced models.

**Lasso Model**

We use a Lasso linear regression to try perform some variable selection on the data. Lasso regression is notorious for its tendency to prefer the removal of insignificant predictors. Thus, by using Lasso regression, we can get an even better sense of which predictors are effectively irrelevant. We discarded those predictors and focused on the remaining ones. Similarly to the ridge model, the lasso model only achieved 33% classification accuracy and solidified the need for more nuanced models.

### Random Forests

After creating basic models to get a general sense of the significance and direction of the predictors, we created more nuanced models to better predict the 2018 midterm results. The first of these models is a Random Forest Classifier. In order to better tune our Random Forest to the data, we tuned the parameters that control the number of trees in the model and the max depth of those trees, until we arrived at the model that recorded the best accuracy on the test data. We found that approximately 65 trees of depth 10 generated the most reliable model, which had 82.0% accuracy.

The bar graph below shows how frequently each of the predictors was used as the most important split for a given tree. We can see that Total population, president_party and seats to defend are never the most important split as those numbers would be the same across all districts for a given year.

![](ChrisLewis0.github.io/rf_top_preds2.png)


### Boosting

Continuing with our more advanced modeling, we created a boosting model through Ada Boost Classifier. The boosting model we employ generates successive Decision Tree Classifiers that outweigh the data points that the previous model struggled with. Thus through multiple iterations we can develop a model that performs better on the challenging data points and thus performs better overall. In order to tune our boosting model, we toggled the base depth of the underlying Decision Tree Classifiers as well as the learning rate which controls the degree to which a model will outweigh the challenging data points from the previous model. The graph below shows the accuracy of our best tuned boosting model on the 2018 midterm test data. As you can see in the graph, the model improves significantly in accuracy during the initial iterations, reaches peak accuracy, and then begins to excessively focus on the challenging data and lose accuracy. We reached our maximum classification accuracy of about 84.5% around approximately 60 iterations of boosting.

![](ChrisLewis0.github.io/boost_test_150.png)

The graph below also shows classification accuracy by iteration, but includes the accuracy on the train data to demonstrate how the possible dangers of overfitting increase significantly with increased iterations.

![](ChrisLewis0.github.io/boost_both_150.png)

### Neural Networks

Our final model that we tested was a Neural Network. We believed that it would be able to properly model such a complex problem; however, it would have the drawback of losing interpretability from the decision tree ensemble methods. When testing the neural network we tried many different configurations of number of layers, size of hidden layers, regularization, and activation functions. The best configuration that we ended with was 3 hidden layers, each with 100 nodes, and using a ReLu activation function with L1 regularization. When training the net we had a 30% validation split with 2000 epochs and used batches in size of 64. The graph illustrates the accuracy and loss over the training and the final accuracy on the net was right around the ensemble methods. We believe with more data it could keep increasing; however, we decided not to use data augmentation due to the variability in voters and not wanting to create bad data.

![](ChrisLewis0.github.io/neuralNet_train.png)

## Interpretation

From our modeling analysis we have determined that the most effective models in predicting the results of the 2018 midterm elections were the boosting model and the artificial neural network, whose classification accuracy around 85% was dramatically better than the 33% accuracy achieved by the linear predictions. In terms of best predictors, we found that the ethnicity variables were highly effective predictors, while sex and age variables could also be significantly effective predictors.

## Conclusion

From our analysis, we learned the importance of diligence when sourcing and cleaning data, how isolating the important features and data points dramatically increases the performance of any model using that data. We used multiple types of models and long lists of predictors to determine the most effective of each. We were able to tune our best models to correctly predict the winner in aproximately 85% of districts nationally in the 2018 midterm elections. Although, even the 85% accuracy we were able to obtain leaves significant room for future improvement and highlights the challange of predicting elections.
