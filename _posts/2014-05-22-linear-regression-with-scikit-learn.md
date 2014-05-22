---
layout: post
title: "Linear regression with scikit learn"
description: ""
category: 
tags: ["machine learning", "python", "scikit learn"]
---
{% include JB/setup %}

It's been a while since the last post was written. So it's time to create new one. I know, I promised to explain
how to choose $ \\alpha $ parameter and why it matters, but not this time. 

Let's solve our linear regression problem using some libraries which already have all algorithms implemented. It's not necessary
to implement all stuff manually, because it's easy to make a mistake and sped tons of hours debugging code. With libraries you 
can just feed the data and get result. Moreover some kind of stuff is not as easy as linear regression (for instance Support Vector Machines)
and to write gradient descent code for them, lot of time should be spent to solve math equations.

We will use [Scikit Learn](http://scikit-learn.org) module. It already has [Linear Regression](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LinearRegression.html#sklearn.linear_model.LinearRegression) model so we can just pass the data and get our $ \\theta $ values returned.

I don't want to copy/paste module documentation in here, you can open website and read everything. Only counterintuitive parts (that were for me, obviously)
will be explained. Data preparation, vectors...etc are all the same. There is only thing that is differnt. Remember, for our model
we added $ X\_0 = 1 $ values as first column to get our hypothesis function looks like $ h\_\\theta(x) = \\theta\_0 + \\theta\_1 \* x $ . 
In scikit learn there is no need to add that column to our data. We just pass $ X $ and $ Y $ column vectors to get model parameters.

All code for linear regression looks as:

```python
from __future__ import print_function
import numpy as np
from sklearn import datasets, linear_model

data = np.loadtxt('ex1data1.txt', delimiter=',')
X = data[:, 0:1]
Y = data[:, 1:2]

regr = linear_model.LinearRegression()
regr.fit(X, Y)

print('Coefficients: Theta_0: {0}, Theta_1: {1}'.format(regr.intercept_[0],  regr.coef_[0][0]))

```

Pretty easy, isn't it? $ X, Y $ are column vectors, our $ \\theta\_0 $ value returned in the `intercept_` property and
all other $ \\theta $ values (we have only one for now, since we're dealing with only one variable) are in the `coef_`
property.

And our model is: $$ h\_\\theta(x) = -3.89578087831 + 1.19303364419*x $$
which is pretty close to one we got by hand.