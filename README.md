# Random-Forests

Random forests are a collection of decision trees. First we need to understand decision trees.

-----------------------------------------------------------------------

## Decision trees
First lets start with basic structure.
![alt text](1https://imgur.com/vwQofl5)
![alt text](https://imgur.com/2qkgqxs)
![alt text](https://imgur.com/YfnAJv2)

Now lets look at what is being considered and decided.
![alt text](https://imgur.com/x9EWMAQ)

Note that.... 
1) It combines numeric data with yes/no data.
2) The cutoff for resting heatrate isn't always the same (100bpm on left, 120bpm on right)
3) Questions on left doesn't have to be same as right (reasting heatrate and doughnuts questions are flipflopped)
4) Classifications can be repeated.


### Phrased as (ignoring the log): 
"The probability of some class k given some train/test example x<sup>1</sup>, is proportional to the probability of that class k out of all classes<sup>2</sup>, multiplied by the joint probabilities of the words in that example<sup>3</sup>. 

<sup>1</sup> In other words, if you are classifying text into one of two classes, this is asking, "for some new piece of text, what's the probability that it is class k?"

<sup>2</sup> This will be equal to 1/(num_classes)

<sup>3</sup> The probability of each word is in base form equal to the count of occurances of that word in that class divided by the total number of words in that class. That would be equal to (5)/(8) in the picture below. But smoothing operators can be added where 1 is a constant, and 6 is the
