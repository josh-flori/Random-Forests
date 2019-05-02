# Random-Forests

Random forests are a collection of decision trees. First we need to understand decision trees.

-----------------------------------------------------------------------

## Decision trees
Lets start with basic structure.
![alt text](https://imgur.com/vwQofl5.png)
![alt text](https://imgur.com/2qkgqxs.png)
![alt text](https://imgur.com/YfnAJv2.png)

Now lets look at what is being considered and decided.
![alt text](https://imgur.com/x9EWMAQ.png)

Note that.... 
1) It combines numeric data with yes/no data.
2) The cutoff for resting heatrate isn't always the same (100bpm on left, 120bpm on right)
3) Questions on left doesn't have to be same as right (reasting heatrate and doughnuts questions are flipflopped)
4) Classifications can be repeated.

How do you go from raw data to a decision tree? Lets look at catagorical data first.

Imagine you have three predictors: Chest Pain, Good Blood Circulation, Blocked Ateries. Which should you pick as the root node (should pick one with most predictive power to the outcome class).

![alt text](https://imgur.com/3puCMGa.png)

What you do is go through each of your predictors. Then for every training example, you ask: "is the predictor value yes or no?" then you look at the outcome value and ask "is it yes or no" (or whatever your values are). Then you tally everything up.

![alt text](https://imgur.com/F7hBu3M.png)

To figure out which has the best predictive power, you calculate the gini impurity for each leaf node, which is 1-(P("yes"))^2-(P("no"))^2, for each side of each predictor, like so...

![alt_text](https://imgur.com/eyVGHQz.png)

Then calculate the total impurity for that predictor like so:

![alt_text](https://imgur.com/CQ0Y28G.png)

Where you take the total number of people in the left leaf node (144) and divide by the total number of people in both leaf nodes (144+159). So the total Gini impurity for chest pain is the weighted average of the leaf node impurities. You pick the predictor with the lowest gini impurity as the root node. To figure out which predictors should come next in the tree structure, you repeat the same process on the subsets of data created by each internal leaf. For a more exact visualization, watch https://youtu.be/7VeUPuFGJHk?t=619

#### But how do you use numeric data? 
First, sort numeric data low-high. 

![alt_text](https://imgur.com/gQFOqTC.png)

Then calculate average value for each adjacent value

![alt_text](https://imgur.com/LVWiyue.png)

Then calculate the gini impurity for each side of the node and the impurity for the entire predictor, which in this case is the average value between each adjacent value

![alt_text](https://imgur.com/UAADexv.png)

Repeat for all average values and find the lowest value and use that as the highest node, the repeat all the way down.

![alt_text](https://imgur.com/1ADTTWE.png).


## Random Forests.
But trees have one aspect that prevents them from being the ideal tool for predictive learning, namely inaccuracy. In other words, they work great with the data used to create them, but they are not flexible when it comes to classifying new samples. Random Forests combine the simplicity of decision trees with flexibility resulting in a vast improvement in accuracy. 

First, create a bootstrapped data set. To create a bootstrapped dataset that is the same size as the original, we just randomly select samples from the original dataset. We're allowed to pick the same sample more than once. On that subset, rather than considering every predictor as a root node, you only consider a subset of predictors. Then when you have determined a root node of that subsample of predictors, remove that predictor from possible consideration and expand the number of predictors you consider for the next node. (You can vary the number of predictors you consider in each tree as a hyperparameter to tune). Repeat this process many times. 

Then you run any new sample through every decision tree. Tally, or aggregate the outcome class predictions from all trees. Then go with whatever has the most. 

#### Bagging: Bootstrapping + aggregating results

How do you know if your forest is any good? Just do some standard cross validation and testing. 

How do you deal with missing data (missing from original training set) & (missing from new sample that you want to catagorize) ? Baically, create a random forest (without the missing data I think..)... then for every missing value in the data, set the missing value to be equal to the most frequent value (if text data) or average value (if numeric). Then run every sample (data point) down every tree. Count the number of times a given sample ended up being classified the same as your sample with missing data. You end up with a matrix that looks like this

![alt_text](https://imgur.com/GwjWBO5.png) 

where the values 1:4 are the samples (you will have as many as M unless you use a fancy ass heuristic approach). The counts are the number of trees that sent those pairs of samples to the same outcome. Then you divide each count by the total number of trees. Then for text data what you do is get the frequency of each text possibility as shown below 

![alt_text](https://imgur.com/f0jFI7l.png)

and multiply it by (proximity of that text / all proximities). The (proximity of that text) is actually the summation of every column in the row for that sample (in this example case, 4) which had that text value, in this case that would be the row/column value (4,2), which has a proximity weight of .1. (all proximities) is equal to the summation of the entire row of your sample (in this case, 4). 

So to make this clear the other direction, for text "no" in this example case, the frequency is 2/3, then (proximity of that text) is .1+.8 yielding .9, then (all proximities) is the same as above. 

When you multiply those things out, you see that the probability, so to speak, of a missing value of "yes" is .03 and the probability for a missing value of "no" in the blocked arteries column is .9, which is much higher, so that's our final value. 

For numeric data you get the weighted average. So multiply each numeric value by the corresponding proximity weight in the matrix. Then sum them together to get the missing value, like so

![alt_text](https://imgur.com/AT0WbGU.png)



But now lets talk about some cool stuff. So the proximity matrix shows weights from 0-1 (after dividing by the number of trees). So a value of 1 is the maximum value. So what happens when you subtract every value from 1? You get a distance matrix. So a resulting value of 0 tells you that those two samples are as close together as possible given those features and that forest. That allows us to create heatmaps like this

![alt_text](https://imgur.com/DDVLutp.png)

and mds plots like this

![alt_text](https://imgur.com/N7EziSk.png)

