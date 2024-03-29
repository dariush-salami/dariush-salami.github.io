---
layout: post
title: "Pantomime: Mid-Air Gesture Recognition with Sparse Millimeter-Wave Radar Point Clouds"
tags: gesture rf-sensing human-computer-interaction ml ai machine-learning mmwave radar
categories: PhD Research
---

## Demo video of Pantomime
Before we dive into the technical explanations, let's watch the demo video of Pantomime. In this demo, we have implemented an interface to control the PowerPoint presentation using Pantomime.
<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="https://www.youtube.com/embed/1_3q1mMtcFs" allowfullscreen></iframe>
</div>

## Presentation of the paper in UBICOMP 2021
Now it is time to understand the technical details of the Pantomime system. Here is the video presented in UBICOMP 2021:
<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="https://www.youtube.com/embed/V2DI2Q51yIQ" allowfullscreen></iframe>
</div>

## How to run Pantomime model?
To be able to run Pantomime on your own dataset or to reproduce the results there are three steps that should be done.

### Dataset
First, you need to download the dataset that we have made publicly available. It is published on Zenodo:

<pre>https://zenodo.org/record/4459969#.YnT9n3VBw5k</pre>

Having downloaded the dataset, you will have a tree-like folder structure:

```
+-- {dataset_date}
|   +-- primary_exp
|       +-- {enviornment}
|           +-- {distance}
|               +-- {angle}
|                   +-- {speed}
|                       +-- {user}
|                           +-- {gesture}
|                               +-- {trial}.pkl
|   +-- supp_exp
|       +-- {enviornment}
|           +-- {distance}
|               +-- {angle}
|                   +-- {speed}
|                       +-- {user}
|                           +-- {gesture}
|                               +-- {trial}.pkl
```
where `{dataset_date}, {enviornment}, {distance}, {angle}, {speed}, {user}, {gesture},` and `{trial}` are the date of the dataset release, the enviornments in which the dataset is collected (open, office, industrial, multi_people, occluded, and restaurant), the distance from the participant to the radar (1m, 2m, 3m, 4m, and 5m), the angle w.r.t. radar (-45, -30, -15, 0, 15, 30, and 45), the speed of the gesture performance (slow, normal, and fast), the anonymous ID of the participant, the gesture class, and the trial, respectively.

As you may have noticed, the format used for the data is `pickle`. [Pickle](https://pypi.org/project/pickle5/){:target="_blank"} is a library used for serializing data into files in Python. After deserializing the pickle file, you will have a [Numpy](https://numpy.org/){:target="_blank"} array. The array has the following shape:
<pre>{frames}x{points}x{3 or 4}</pre>
The first dimension is the frames in the gesture trial. Note that the number of frames is not fixed. It varies from trial to trial. The second dimension is the points in frame. Again, keep in mind that each frame can have a different number of points. Finally, the last dimension is `x, y, z`, and `intensity`. The intensity can be missing for some gestures. Now, you have the dataset and you know how to process the files :) Let's go to the next step.

### Preprocessing Pipeline
In the second step, we probably need to perform preprocessing on the dataset. Again, you need to clone the repository for preprocessing script:

<pre>https://version.aalto.fi/gitlab/salamid1/rf-point-cloud-gesture-recognition</pre>

The preprocessing pipeline is written using a workflow management system called [Snakemake](https://snakemake.readthedocs.io/en/stable/){:target="_blank"}. Make sure you are familiar with Snakemake. There are three main files in the preprocessing pipeline:
```
+-- Snakefile - the entry file for the pipeline where the steps are defined
+-- primary_exp_config.json - the configuration file for the primary experiments
+-- supp_exp_config.json - the configuration file for the supplementary experiments
```
You can use the pre-defined steps that we have developed for the model. Needless to say, you can come up with your own preprocessing steps in this part to make a more efficient model! This preprocessing pipeline is used in all my papers from `Pantomime` to `Tesla`.

### Training Pantomime Model
Now, you hopefully have your data preprocessed using the pipeline and looking forward to training Pantomime on your data! As we have mentioned in the paper, the model is pretty huge. So, at first, make sure you have a strong enough GPU to train the model. Then, you need to download the source code of the model from the link below:

<pre>https://zenodo.org/record/4459969#.YnT9n3VBw5k</pre>

The next step is to compile the custom tf operators that we have used in the model. Here is the link in `PointNet++` repository that you can use to learn how to compile the tf operators:

<pre>https://github.com/charlesq34/pointnet2</pre>

After compiling the operators, you can run `train.py` with different parameters to train your model. But before we finish this post, there are a few things that you need to pay attention to:

- The training scripts read the dataset file in a different format namely [H5PY](https://www.h5py.org/){:target="_blank"}. But fortunately, we already have that final step of converting pickle file to h5py file in the preprocessing pipeline.
- The training script read a txt file pointing out to the h5py file mentioned in the previous step. As a result, you need to create a text file and then simply put the path to the h5py file in it to be able to run the model.
- Since Pantomime applies PointNet++ on each frame of the dataset, using many frames will increase the computational load significantly. If your processing power is low, it is highly recommended to use less number of frames per gesture. Or, you can check out my recent paper entitled `Tesla` which is way more computationally efficient than Pantomime.

In the end, if you have any further questions or feedback, you can reflect them in the comments section below or drop me emails directly :)
