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

