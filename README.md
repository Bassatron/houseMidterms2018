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

![Average Votes by State](https://github.com/Bassatron/housemidterms2018/blob/master/figures/Distributions_of_Change_in_House Seats.png?raw=true)
