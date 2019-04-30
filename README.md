# Random-Forests

Random forests are a collection of decision trees. First we need to understand decision trees.

-----------------------------------------------------------------------

## Decision trees
First lets start with basic structure.
![alt text](1https://imgur.com/vwQofl5.png)
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

