---
title: "Building a convolutional neural network to identify pneumonia"
date: 2021-01-10
tags: [Deep Learning, Classification, backpropagation, Keras]
header:
 image: 
excerpt: "Building and deploying a convolutional neural network architecture to classify patient chet x-ray scans (pneumonia vs. healthy) using an Amazon AWS GPU."
mathjax: "true"
---

Let's take a look at the following x-rays:

![alt]({{ site.url }}{{ site.baseurl }}/figures2/pneumvshealth.png)

Identifying which chest x-ray reveals pneumonia in the patient may prove difficult for the untrained eye (like mine!). In 2017-2018, there was a 30% increase in job openings for radiologists - according to the Association of American Medical Colleges (AAMC). And now with the coronavirus, it might have become even more.

In this article, I build a convolutional neural network that classifies such x-rays into either 'pneumonia' or 'healthy' on Keras. I have used data from 
a [Kaggle Chest x-ray dataset](https://www.kaggle.com/paultimothymooney/chest-xray-pneumonia) to train the model. I'd also like to explicitly refer to an amazing book that has taught me everything on Deep Learning (Deep Learning on Python by François Chollet).


## Setting up the GPU on Amazon AWS 

The first step was to setup a GPU: though not exactly strictly necessary, I wanted to learn how to setup an instance on Amazon AWS. I selected a p2.xlarge instance, to hopefully speed up some computations on GPU.

![alt]({{ site.url }}{{ site.baseurl }}/figures2/instance.png)

Running on Windows, I've installed and setup Putty. The trickiest part for me was to learn to forward some ports to be able to run Jupyter Notebooks on my browser, but that was not very difficult as it could be easily done here:

![alt]({{ site.url }}{{ site.baseurl }}/figures2/port.png)




## Data preprocessing

### Adjusting training, testing and validation file size:

I first noticed that the original xray dataset on Kaggle wasn't very well split between training, testing and validation. So I proceeded with renaming all files and merging them and recreating my own split. I then created the directories that will include the necessary files. Creating the pneumonia and normal directories can be easily done as follows:

```python
import os, shutil
original_dataset_dir = 'C:/Users/Jad/Downloads/chest_xray/alldata'

base_dir = 'C:/Users/Jad/Desktop/medicalimage'
os.mkdir(base_dir)

train_dir = os.path.join(base_dir, 'train')
os.mkdir(train_dir)

validation_dir = os.path.join(base_dir, 'validation')
os.mkdir(validation_dir)

test_dir = os.path.join(base_dir, 'test')
os.mkdir(test_dir)

train_norm_dir = os.path.join(train_dir, 'norm')
os.mkdir(train_norm_dir)

train_pneum_dir = os.path.join(train_dir, 'pneum')
os.mkdir(train_pneum_dir)

validation_norm_dir = os.path.join(validation_dir, 'norm')
os.mkdir(validation_norm_dir)

validation_pneum_dir = os.path.join(validation_dir, 'pneum')
os.mkdir(validation_pneum_dir)

test_norm_dir = os.path.join(test_dir, 'norm')
os.mkdir(test_norm_dir)

test_pneum_dir = os.path.join(test_dir, 'pneum')
os.mkdir(test_pneum_dir)



```



### A data augmentation technique 





## The ConvNet Architecture
 
