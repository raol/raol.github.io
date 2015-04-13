---
layout: post
title: "Machine Learning in Azure. Publishing."
description: ""
category: 
tags: ["machine learning", "azure"]
---
{% include JB/setup %}

In the [previous](/2014/07/21/machine-learning-in-azure/) post I explained how to create new 
Azure machine learning experiment and how to use linear regression to make predictions.
The experiment is pretty cool itself as any other one, but there is one minor problem with it.
It's totally useless. Oh, you certainly can open it up and run all steps manually each time you need
to predict profit (I'm still talking about previous post with synthetic experiment and synthetic dataset)
based on city population, but, agree, it's not very suitable. It would be nice if you had a way
to store your trained model and supply data you need to analyze, and get back results.

<!-- more -->

Fortunately guys from Microsoft foresaw, that someone might want to use their ML results 
at the website, to predict, let's say, what pages with kittens might be interesting to see for 
certain users. Something like Twitter or Facebook suggestions. So you can run the experiment, save 
the model you designed and publish it via Web Service. Let's do it.

I'm publishing old experiment from the previous post with slight modifications added.
This is not a puzzler game, so I'll explain what was changed and how I did it.

![changed-experiment](/assets/images/azure_ml_2/changed_exp.png)

First of all I added new _Project Columns_ task and chosen only population column to be its output
or input of the _Score Model_ task. In the previous version of the experiment there was no this task
so _Score Model_ accepted two columns from our training set, both Population and Profit. It led to incorrect
Web Service contract. Since there is only one feature column we have to tell our model which one is.

Next, you've probably noticed blue and green circles around input and output ports of the _Score Model_ task.
I'll talk about them later.

Next thing you need to do is to save your trained mode. So, run experiment again. After all steps are completed
right click output port of the _Train model_ task and in the drop down menu choose _Save as Trained Model_ option.
Now your model (or in other words, linear regression function coefficients) is saved and you're almost ready to
publish your experiment. Only one thing is left.

You need to specify what ports of the experiment are input and what ones are output.
So right click input port of the _Score Model_ task and choose _Set as Publish Input_. Do the same 
for the output port, but this time choose _Set as Publish Output_ option.

Now in the large bottom menu _Publish Web Service_ item should be enabled. If it's not, just run experiment
once again and it will be enabled. At least for me it worked fine.

Just click the button and Web Service will be created automatically. After it's published you will be 
redirected to the Web Service page which looks like below. I removed API key. 

![changed-experiment](/assets/images/azure_ml_2/web_service_configuration.png)

So it's time to consume the web service.

The simplest way is to create console application. So create it, then in the _Stagin Services_ list click
the row named as your web service and with type _Request/Response_. I'm writing in normal case. :) Don't know
who told Microsoft designers that upper case IS BRILLIANT IDEA! The sample code will be opened.

Simply follow instructions on the scree, i.e. install `Microsoft.AspNet.WebApi.Client` package into 
console application (if you created ASP.NET application as I did, you can ignore package install step) 
and copy-paste the code from the example. Don't forged to change the API key 
to the one you have at your web service configuration page. Now you can call your web service, supply data
to it and get back predicted/classified/clustered results.