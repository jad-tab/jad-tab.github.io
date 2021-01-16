---
title: "Building a convolutional neural network to identify pneumonia"
date: 2020-04-06
tags: [Deep Learning, Classification, backpropagation, accuracy]
header:
 image: 
excerpt: "Building and deploying a convolutional neural network architecture to classify patient chet x-ray scans (pneumonia vs. healthy) using an Amazon AWS GPU.
mathjax: "true"
---

Let's take a look at the following x-rays:

![alt]({{ site.url }}{{ site.baseurl }}/figures2/pneumvshealth.png)

Identifying which chest x-ray reveals pneumonia in the patient may prove difficult for the untrained eye (like mine!). Between 2017 and 2018, there was a 30% increase in job openings for radiologists - according to the Association of American Medical Colleges (AAMC). And now with Covid-19, it may be even more.

In this article, I build a basic convolutional neural network that classifies such x-rays into either 'pneumonia' or 'healthy'. I have used data from the 
a [Kaggle Chest x-ray dataset](https://www.kaggle.com/paultimothymooney/chest-xray-pneumonia) to train the model. I'd also like to explicitly refer to an amazing book that has taught me everything on Deep Learning (Deep Learning on Python by François Chollet).


## Setting up the GPU on Amazon AWS

The first step was to setup a GPU: though not exactly strictly necessary, I wanted to learn how to setup an instance on Amazon AWS. I selected a p2.xlarge instance GPU, to hopefully speed up some computations.
![alt]({{ site.url }}{{ site.baseurl }}/figures2/instance.png)















## Dealing with missing values



### Dealing with the Seasons
 
 

```python
complete_data['DateTime'] = pd.to_datetime(complete_data['DateTime'], format='%Y-%m-%d %H:%M:%S')
complete_data['FractionOfYearFromBeginningOfYear'] = complete_data['DateTime'].map(
    lambda x: (x-pd.Timestamp(year=x.year, month=1, day=1)).days/365
)
complete_data['FractionOfDay'] = complete_data['DateTime'].map(lambda x: x.hour/24)

complete_data = complete_data.loc[:, complete_data.columns != 'DateTime']

```


![alt]({{ site.url }}{{ site.baseurl }}/figures/algo4.PNG)

### Converting the Animal Type Column's labels: 

A quick check of the 'AnimalType' column allows us to determine that we're classifying cats and dogs. 
I then convert the True/False boolean mask into "0"'s and "1"'s.

```python

complete_data['AnimalType'].unique()

complete_data['AnimalType'] = (complete_data['AnimalType']=='Dog').astype(np.int)

```

### Cleaning the messy 'AgeuponOutcome' column:

In the Ageuponoutcome column there are numerical values and different units: years, weeks. Furthermore sometimes for the same unit we have a singular or a plural. I'm going to implement a "period string to years" function as follows:
* First, split the column at the space
* Take the resulting integer n value as well as the associated string (the unit)
* Add an -s to this string  (make all units in plural)
* Convert the associated n to years (by distinguishing cases where string is years, months, weeks or days) using the above conventions






```python

def period_str_to_years(s):
    if pd.isnull(s):
        return None
    n, unit = s.split(' ')
    n = int(n)
    if unit[-1] != 's':
        unit = unit + 's'
    if unit == 'years':
        return n
    if unit == 'months':
        return n/12
    if unit == 'weeks':
        return n/52
    if unit == 'days':
        return n/365
    return None

#Apply the function str_to_years to our AgeuponOutcome column and delete it subsequently)
complete_data['AgeInYears'] = complete_data['AgeuponOutcome'].map(period_str_to_years)
complete_data = complete_data.loc[:, complete_data.columns != 'AgeuponOutcome']
complete_data.dropna(subset=('AgeInYears',), inplace=True)

```

We can now see each animal's age in years:

![alt]({{ site.url }}{{ site.baseurl }}/figures/algo5.PNG)




### Animal 'Breed' and 'Color' columns

Let us now deal with the Breed and Color columns. For the Breed, we shall look at the presence of the word 'Mix' as well as the presence of the separator ("/"). This indicates a mixed breed animal.
For the color, some animals are labelled as 'tabby' and other animals have multiple colors separated by a '/'. So I shall split Breed and Color according to these two cases. 
Then, I assign a binary variable for each column: 'The animal is tabby' or  'the animal is not tabby'.
The animal has 'mixed colors' or the animal has 'no mixed colors'.

```python
complete_data['Mixedbreed'] = (complete_data['Breed'].str.contains(' Mix', case=False) 
                               | complete_data['Breed'].str.contains('/')).astype(np.int)
complete_data = complete_data.loc[:, complete_data.columns != 'Breed']

complete_data['Tabby'] = complete_data['Color'].str.contains('Tabby', case=False).astype(np.int)
complete_data['MixedColors'] = complete_data['Color'].str.contains('/').astype(np.int)
complete_data = complete_data.loc[:, complete_data.columns != 'Color']
```

This is where we're at, by this point: 
![alt]({{ site.url }}{{ site.baseurl }}/figures/algo6.PNG)


### The final step of preprocessing: Converting the Outcomes' column into categorical variables

Since clearly $$M=5$$ (we have 5 possible outcomes for each animal), we can simply convert these labels using the following dictionary for convenience.:

```python
outcome_type_dict = {
    'Return_to_owner': 0,
    'Euthanasia': 1,
    'Adoption': 2,
    'Transfer': 3, 
    'Died': 4
}
outcome_type_dict_inv = {v: k for k, v in outcome_type_dict.items()}

complete_data['OutcomeType'] = complete_data['OutcomeType'].map(lambda x: outcome_type_dict[x])
```


![alt]({{ site.url }}{{ site.baseurl }}/figures/algo7.PNG)

Finally, that's much cleaner ! We can now proceed to building the predictive models.

## Splitting the training and testing sets (the old school way)

Of course, we could use the train_test_split function from scikit-learn ! But we could also do it old-school:

```python
np.random.seed(0)
train_idx = np.random.choice(complete_data.index, size=3*complete_data.index.size//4, replace=False)
test_idx = complete_data.index.difference(train_idx)
train = complete_data.loc[train_idx]
test = complete_data.loc[test_idx]

X_train, Y_train = train[features].values, train['OutcomeType'].values
X_test, Y_test = test[features].values, test['OutcomeType'].values
```
This small following technical step is just a way to adapt the form of Y_test to be able to use it as a parameter for the log_loss function I introduced earlier (similar to the $$y_{ij}$$)

```python
Y_test_bis = (Y_test[:, np.newaxis] == np.arange(5)[np.newaxis, :]).astype(np.int)
```

## Building some models, and using GridSearchCV:

I'm going to perform below a Grid Search to determine the best values of the hyperparameters for the upcoming model.

```python
# the following dictionary and we will use the GridSearchCV (crossvalidation) to find the best
# logloss value.
max_depth_guesses = (5, 10, 15, 20, 30, 40, 50)
param_grid = {
    'max_depth': max_depth_guesses
}
```

I'm going to first build a basic decision tree:

```python
decision_tree = GridSearchCV(DecisionTreeClassifier(random_state=0), param_grid)
decision_tree.fit(X_train, Y_train)
decision_tree_logloss = log_loss(Y_test_bis, decision_tree.predict_proba(X_test))
print('Log-loss of decision tree classifier: {0}'.format(decision_tree_logloss))
```

A random forest:

```python
decision_tree = GridSearchCV(DecisionTreeClassifier(random_state=0), param_grid)
decision_tree.fit(X_train, Y_train)
decision_tree_logloss = log_loss(Y_test_bis, decision_tree.predict_proba(X_test))
print('Log-loss of decision tree classifier: {0}'.format(decision_tree_logloss))
```

Finally, here's an implementation of gradient boosting.

```python
grad_boosting = GridSearchCV(GradientBoostingClassifier(random_state=0, n_estimators=100), 
                             param_grid=param_grid, verbose=True)
grad_boosting.fit(X_train, Y_train)
grad_boosting_logloss = log_loss(Y_test_bis, grad_boosting.predict_proba(X_test))
print('Log-loss of gradient boosting classifier: {0}'.format(grad_boosting_logloss))
```

Results:
```python
Log-loss of decision tree classifier: 0.9210953350935193
Log-loss of random forest classifier: 0.8467709000339786
Log-loss of gradient boosting classifier: 0.8334824132643417
```

It seems that the best model (among those three) is gradient boosting, as it achieves the lowest loss. 
But ! The big downside is that it took... 23 minutes to train ! While the random forest only took 25.4s to finish.

What's more, is that when I look at how these classifiers perform on subsets of the data (on cats, and dogs):


![alt]({{ site.url }}{{ site.baseurl }}/figures/algo8.PNG)

I get that the classifier performs much better on cats. Maybe I haven't extracted enough features for the dogs ?
Or maybe more data has to reveal some more patterns for the dogs. 
Ensemble learning methods could be tried, maybe by training a model exclusively for the dogs. But of course, the feature selection and extraction steps above can be discussed more in depth.


## The Destiny of Gus and Skooter

And now, the answer we've all been waiting for. What's next in store, for Gus and Skooter?
By applying the gradient boosting classifier trained above to the new data, we can compute the probabilities of each outcome for Gus and Skooter.

![alt]({{ site.url }}{{ site.baseurl }}/figures/gus1.PNG)


![alt]({{ site.url }}{{ site.baseurl }}/figures/gus2.PNG)


Looks like Gus is most likely to be transferred to another shelter/facility, while Skooter has a good chance at getting adopted ! Or even returned to their original owner. Unfortunately, not all animals are so lucky. That is the case of Summer, the first dog in the above table, very likely to be euthanized. 

But don't be sad: our prediction model - as we saw - isn't very accurate on dogs. That can be a good or a bad thing (mainly for poor Skooter). However, while more data could be gathered and the procedure could be made better, there's one sure procedure we can implement to guarantee a beautiful outcome for every animal (with a 100% accuracy!): Why don't you stop by at [Animals Lebanon's website](http://www.animalslebanon.org/) (or any other shelter), and adopt one of those cute little munchkins ?

![alt]({{ site.url }}{{ site.baseurl }}/figures/animals.PNG)

This is one way we could easily beat the highly performant Gradient Boosting algorithm. 