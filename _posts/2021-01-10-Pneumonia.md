---
title: "Building a convolutional neural network to identify pneumonia"
date: 2021-01-10
tags: [Deep Learning, Classification, backpropagation, Keras, Medical Imaging]
header:
 image: 
excerpt: "Building and deploying a convolutional neural network architecture to classify patient chet x-ray scans (pneumonia vs. healthy) using an Amazon AWS GPU."
mathjax: "true"
---

Let's take a look at the following x-rays:

![alt]({{ site.url }}{{ site.baseurl }}/figures2/pneumvshealth.png)

Can you guess which patient has pneumonia ? I could not... but maybe my computer can be trained to become a part time radiologist ?

In 2017-2018, there was a 30% increase in job openings for radiologists - according to the Association of American Medical Colleges (AAMC). And now with the coronavirus, it might have become even more.

In this article, I build a convolutional neural network that classifies such x-rays into either 'pneumonia' or 'healthy' on Keras. I have used data from 
a [Kaggle Chest x-ray dataset](https://www.kaggle.com/paultimothymooney/chest-xray-pneumonia) to train the model. I'd also like to explicitly refer to an amazing book that has taught me everything on Deep Learning (Deep Learning on Python by François Chollet).


{:toc}

## Setting up the GPU on Amazon AWS 

The first step was to setup a GPU: though not exactly strictly necessary, I wanted to learn how to setup an instance on Amazon AWS. I selected a p2.xlarge instance, to hopefully speed up some computations on GPU.

![alt]({{ site.url }}{{ site.baseurl }}/figures2/instance.PNG)

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

Now that the directories are created, I proceed with filling them with corresponding xrays.

```python
fnames = ['norm{}.jpg'.format(i) for i in range(1,949)]
for fname in fnames:
    src = os.path.join(original_dataset_dir, 'normal', fname)
    dst = os.path.join(train_norm_dir, fname)
    shutil.copyfile(src,dst)
    
fnames = ['norm{}.jpg'.format(i) for i in range(949, 1265)]
for fname in fnames:
    src = os.path.join(original_dataset_dir, 'normal', fname)
    dst = os.path.join(validation_norm_dir, fname)
    shutil.copyfile(src,dst)
    
fnames = ['norm{}.jpg'.format(i) for i in range(1265, 1581)]
for fname in fnames:
    src = os.path.join(original_dataset_dir, 'normal', fname)
    dst = os.path.join(test_norm_dir, fname)
    shutil.copyfile(src,dst)

fnames = ['pneum{}.jpg'.format(i) for i in range(1,949)]
for fname in fnames:
    src = os.path.join(original_dataset_dir, 'pneum', fname)
    dst = os.path.join(train_pneum_dir, fname)
    shutil.copyfile(src,dst)
    
fnames = ['pneum{}.jpg'.format(i) for i in range(949, 1265)]
for fname in fnames:
    src = os.path.join(original_dataset_dir, 'pneum', fname)
    dst = os.path.join(validation_pneum_dir, fname)
    shutil.copyfile(src,dst)
    
fnames = ['pneum{}.jpg'.format(i) for i in range(1265, 1581)]
for fname in fnames:
    src = os.path.join(original_dataset_dir, 'pneum', fname)
    dst = os.path.join(test_pneum_dir, fname)
    shutil.copyfile(src,dst)
```



### A data augmentation technique 


Since the dataset contains relatively few samples, I'm going to resort to data augmentation, using the ImageDataGenerator. This technique allows one to obtain an augmented dataset by performing random geometric transforms to the data.


```python
from keras import layers
from keras import models 
from keras import optimizers
from keras.preprocessing.image import ImageDataGenerator

datagen = ImageDataGenerator(
    rotation_range=50,
    width_shift_range=0.2,
    height_shift_range=0.2,
    shear_range=0.2,
    zoom_range=0.2,
    horizontal_flip=True,
    fill_mode='nearest')
```

For each xray that passes through, it will be rotated by a random number of degrees (between 0 and 40 degrees here), horizontal_flip means the xray might be randomly flipped. Shearing and zooming, shifting the xrays allows me to obtain more data artificially.

While this may not be enough to stage off overfitting, a dropout layer will be included in my architecture (see below).



## The ConvNet Architecture

I'm going to use the following common convolutional neural network architecture:
 
![alt]({{ site.url }}{{ site.baseurl }}/figures2/architecture.png)


```python
model = models.Sequential()
model.add(layers.Conv2D(32, (3,3), activation='relu', input_shape=(150,150,3)))
model.add(layers.MaxPooling2D((2,2)))
model.add(layers.Conv2D(64, (3,3), activation='relu'))
model.add(layers.MaxPooling2D((2,2)))
model.add(layers.Conv2D(128, (3,3), activation='relu'))
model.add(layers.MaxPooling2D((2,2)))
model.add(layers.Conv2D(128, (3,3), activation='relu'))
model.add(layers.MaxPooling2D((2,2)))

model.add(layers.Flatten())
model.add(layers.Dropout(0.5))
model.add(layers.Dense(512, activation='relu'))
model.add(layers.Dense(1, activation='sigmoid'))

model.compile(loss='binary_crossentropy', optimizer=optimizers.RMSprop(lr=1e-4), metrics=['acc'])
```

## Training the ConvNet


The test and training set are augmented but I don't do that for the validation set. Images are resized to a standard 150x150. 

```python
test_datagen = ImageDataGenerator(rescale=1./255)

train_generator = train_datagen.flow_from_directory(
    train_dir,
    target_size=(150,150),
    batch_size = 32,
    class_mode = 'binary')

validation_generator = test_datagen.flow_from_directory(
    validation_dir,
    target_size = (150,150),
    batch_size = 32,
    class_mode = 'binary')

history = model.fit_generator(
    train_generator,
    steps_per_epoch = 100,
    epochs = 100,
    validation_data = validation_generator,
    validation_steps = 50)
```

For the sake of comparing, I ran the training on my CPU and compared it to Amazon's GPU. Though nothing was done to further optimize the code on tensorflow, there was already a difference in performance:


![alt]({{ site.url }}{{ site.baseurl }}/figures2/slow.PNG)


![alt]({{ site.url }}{{ site.baseurl }}/figures2/fast.PNG)


## Exploring the trained model

### Performance measures of the model
Here are the results obtained for this basic model:


![alt]({{ site.url }}{{ site.baseurl }}/figures2/cnnpneumonia.JPG)

Though the dataset was quite modest, I was able to get some pretty good results. But I can't help but notice the large leaps that aren't necessarily expected: it might be interesting to see if these performance measures could be further improved.


### Visualizing some layer activations 

I would like to end this article by visualizing the activation of some convolutional layers of the network to see what it has learned.
These are the activations of the first and third channels of the first convolutional layer for an xray I selected:

![alt]({{ site.url }}{{ site.baseurl }}/figures2/cnnk1.JPG)

This channel appears to encode the general thoracic cavity shape.


![alt]({{ site.url }}{{ site.baseurl }}/figures2/cnnk3.JPG)

This channel appears to encode the heart's outlines.




## Takeaway

A simple deep learning model has allowed me to identify pneumonia xrays from the comfort of my home. While  more can be done to improve this model, it was a fun test run for me. Maybe I could apply this same model for covid-19 cases, once some data is available ?


