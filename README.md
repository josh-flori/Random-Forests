# Overview
This repository exists as a personal summarization and guide to understanding Random Forests in laymans terms.  

# Random-Forests

Random forests are a collection of decision trees. First we need to understand decision trees.

#### Bagging: Bootstrapping + aggregating results

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
3) Questions on left don't have to be same as right (resting heartrate and doughnut questions are flipflopped in vertical order)
4) Classifications can be repeated.

How do you go from raw data to a decision tree? Lets look at catagorical data first.

Imagine you have three predictors: Chest Pain, Good Blood Circulation, Blocked Ateries. How do you structure the questions in your tree? Which should you pick as the root node (you want to pick the one with most predictive power to the outcome class).

![alt text](https://imgur.com/3puCMGa.png)

What you do is go through each of your predictors (in this case our three columns: Chest Pain, Good Blood Circulation, Blocked Ateries). Then you tally up all the permutations between that column and the outcome variable. So our permutations for (Chest Pain, Heart Disease) are: (yes,yes), (yes,no), (no,yes), (no,no). This corresponds to the two uppermost green blocks below. The left green block corresponds to the permutations (yes,yes) and (yes,no). We can see that of the people that DID have chest paid, 105 had heart disease and 39 did not. And so on... for all predictors.

![alt text](https://imgur.com/F7hBu3M.png)

To figure out which has the best predictive power, you calculate the gini impurity for each predictor, which is 1-(P("yes"))^2-(P("no"))^2, for each side of each predictor, like so...

![alt_text](https://imgur.com/eyVGHQz.png)

Then calculate the total impurity for the ENTIRE predictor (in other words, above, we looked at the impurity of each "side" of the predictor (yes, no), but below we are looking at their total effect, or in other words the total power of the predictor as a whole) like so:

![alt_text](https://imgur.com/CQ0Y28G.png)

You take the total number of samples/datapoints in both sides of the predictor (yes,no) and divide by the total number of people in both sides of the predictor (yes,no) (144+159). So the total Gini impurity for chest pain is the weighted average of both impurities. 

You pick the predictor with the lowest gini impurity as the root node. To figure out which predictors should come next in the tree structure, you repeat the same process on the subsets of data created by each split for the rest of the predictors. For a more exact visualization, watch https://youtu.be/7VeUPuFGJHk?t=619

#### So that's how you pick catagorical predictors, how do you use numeric data? 
First, sort numeric data low-high. 

![alt_text](https://imgur.com/gQFOqTC.png)

Then calculate average value for each adjacent value

![alt_text](https://imgur.com/LVWiyue.png)

Then calculate the gini impurity for each side of the predictor as well as the impurity for the entire predictor, which in this case is the average value between each adjacent value. I guess you would need to make sure that you don't have as many numeric values as N, but maybe that doesn't really matter. Seems like it would.

![alt_text](https://imgur.com/UAADexv.png)

Repeat for all average values and find the lowest value and use that as the highest node, the repeat all the way down.

![alt_text](https://imgur.com/1ADTTWE.png).


## Random Forests.
But trees have one aspect that prevents them from being the ideal tool for predictive learning, namely inaccuracy. In other words, they work great with the data used to create them, but they are not flexible when it comes to classifying new samples. Random Forests combine the simplicity of decision trees with flexibility resulting in a vast improvement in accuracy. 

First, create a bootstrapped data set. WHAT IS A BOOTSTRAPPED DATASET? It is one where we randomly select samples from the original dataset. We're allowed to pick the same sample more than once. On that subset, rather than considering every predictor as a condidate for the root node, you only consider a subset of predictors. Then when you have determined a root node of that subsample of predictors, remove that predictor from possible consideration and expand the number of predictors you consider for the next node. (You can vary the number of predictors you consider in each tree as a hyperparameter to tune). Repeat this process many times.  Specifically, in review, 1) you start with your data, 2) you take a bootstrapped sample 3) you create a decision tree following the rules rescribed above but not considering all of the predictors as a root node. Repeat steps 1:3 on many bootstrapped samples from the dataset, creating many different trees based on both the unique sample and the unique predictors considered for the root node. This is a regularization technique. 

Here's a simple outcome example of many trees that may be created from this process. That is your forest of trees.

![alt_text](https://imgur.com/bo3fAlh.png)

## NEW PREDICTIONS
To use this forest in the wild to make a new prediction, you run any new sample through every decision tree in the forest. You then tally, or aggregate the outcome class predictions from all trees. Then your prediction is whichever number is highest of your possible classes.

## VALIDATION
How do you know if your forest is any good? Just do some standard cross validation and testing. 

## MISSING DATA
How do you deal with missing data (missing from original training set) & (missing from new sample that you want to catagorize)? In the first case, create a random forest (without the missing data I think..)... then for every missing value in the training set, set the missing value to be equal to the most frequent value (if text data) or average value (if numeric). Then run every sample (data point) down every tree in said created forest. Count the number of times a given data point (row) in your training set ended up being classified the same as your sample with missing data. In other words, for every tree in your forest, you run every row of data down that tree and in a matrix of ROWxROW data, put a "1" where those two rows were classified the same by that given tree. Repeat for all trees in forest and +=1 if those two rows were classified the same by that next tree. You end up with a matrix that looks like this

![alt_text](https://imgur.com/GwjWBO5.png) 

where the values 1,2,3,4 are the samples/rows in your training set (you will have as many as M unless you use a fancy ass heuristic approach). The counts are the number of trees that sent those pairs of samples to the same outcome. After you have this, you divide each count by the total number of trees to normalize it. 

### Catagorical
If you are dealing with missing text/catagorical data, then what you do next is get the frequency of each text possibility (in the training set) as shown below (where frequency of yes is 1/3 and frequency of no is 2/3)

![alt_text](https://imgur.com/f0jFI7l.png)

and multiply that frequency by (PROXIMITY OF THAT TEXT / ALL PROXIMITIES). 

The (PROXIMITY OF THAT TEXT) is actually the summation of every column in the proximity matrix row for the training sample that was missing data (in this example case, 4) which had that given text value. In this case that would be the row/column value (4,2), which has a proximity weight of .1 (derived after dividing the counts by number of trees). 

(ALL PROXIMITIES) is equal to the summation of the entire row of your training sample (in this case, 4). 

So to make this clear the other direction, for text "no" in this example case, the frequency is 2/3, then (proximity of that text) is .1+.8 yielding .9, then (all proximities) is the same as above. 

When you multiply those things out, you see that the probability, so to speak, of a missing value of "yes" is .03 and the probability for a missing value of "no" in the blocked arteries column is .9, which is much higher, so that's our final value. 

### Numeric
For numeric data you get the weighted average. So multiply each numeric value for all training examples for the predictor by the corresponding proximity weight in the matrix. Then sum them together to get the missing value, like so

![alt_text](https://imgur.com/AT0WbGU.png)


# cool stuff

But now lets talk about some cool stuff. So the proximity matrix shows weights from 0-1 (after dividing by the number of trees). So a value of 1 is the maximum value. So what happens when you subtract every value from 1? You get a distance matrix. So a resulting value of 0 tells you that those two samples are as close together as possible given those features and that forest. That allows us to create heatmaps like this

![alt_text](https://imgur.com/DDVLutp.png)

and mds plots like this

![alt_text](https://imgur.com/N7EziSk.png)

