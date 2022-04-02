# Some context

I've been doing the ORL/IA challenge for a few months and we've got a pretty cool model out. To make it sound fancy, we built a "we did a mutli-modal interpretable convolutional network based on siamese similarity pretraining model" aka we found a pretty cool model which did the job and trained it further.

We must've been doing something right because we wound up winning the leaderboard. However, our first attempt... wasn't like this. It did too well? 

We investigated it a bit further...

# Performance comparison against regular ResNet, 10Percent and model built from scratch
![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn/blob/main/images/performancevsmodels.png)

Originally for the challenge, we based used transfer learning using the 10Percent model. After doing some more training, we found it was a significant improvement on the original ResNet model! Furthermore, the model performs marginally better than a model trained from scratch. However, it is interesting to note that 10Percent and Eln-AI-ny performed similarly.

This is suspicious! 

### Investigating what is going on here

Was using a pre-trained model trained on histological data truly the deciding factor when explaining the performance? 

In an experiment, we retrain our models but replace 10Percent with ResNet. The aim of this experiment is to understand the impact of using a pre-trained model. Furthermore, we include 10Percent but do not allow training on the CNN (which means only the part of the model where the tabular and images are combined is being trained), which proves similar performance.

![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn/blob/main/images/startingpointtraining.png)

We find that using 10Percent as a base only marginally performs better on its validation set compared to using ResNet as a base. 

## Visualising what is going on here

### Using GRADCAM to investigate what the model is seeing
GRADCAM is an algorithm used to debug models by generating heat-maps over earlier layers in the model to help us visualise what a model is looking at when it's making it's decisions

When using GRADCAM on other pre-trained models (top to bottom in order: Alexnet, VGG, DenseNet, SqueezeNet, ResNet and 10Percent), result in the following heat-maps

![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn-Source-Code/blob/main/images/alex_cam.png)
![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn-Source-Code/blob/main/images/vgg_cam.png)
![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn-Source-Code/blob/main/images/dense_cam.png)
![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn-Source-Code/blob/main/images/squeezenet_cam.png)
![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn-Source-Code/blob/main/images/resnet_cam.png)
![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn-Source-Code/blob/main/images/tenpercent_cam.png)

El-nAI-ny performs the following evaluation when making it's decision:

![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn/blob/main/images/best-cam.png)

As one can see, El-nAI-ny keeps hold of some it's Res-Net characteristics (similarly like 10Percent) suggesting it hasn't been able to fully learn features in the data much further than 10Percent.


***

This performance can be explained by two things: 

### 10Percent is just that good
If that was the case, why did the Scratch network train well?

### Model is more dependent on features taken from tabular than literature 
If this is the case, then this would mean the combined model is learning more from the tabular features. This could possible be due to learning a relationship between the dates and OS. 

# Role of Pre-trained Models in training

We switch the original pre-trained model between ResNet and 10Percent to see if there is any major difference. Furthermore, we remove the tabular training.

![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn/blob/main/images/justimages.png)

Interesting... it can't learn the images... 

Training with tabular provides results identical to the patterns we see above however.

To ensure there is no data leak, we print data during train time:

```OMS                0```
```sexe (0=F 1=M)     0```
```DDN               61```
```Date biopsie       9```
```Age au diag       52```
```Name: 582, dtype: object```
```74```
```OMS                1```
```sexe (0=F 1=M)     1```
```DDN               81```
```Date biopsie       7```
```Age au diag       74```
```Name: 141, dtype: object```
```15```
```OMS                0```
```sexe (0=F 1=M)     0```
```DDN               63```
```Date biopsie       9```
```Age au diag       54```
```Name: 150, dtype: object```
```70```

No OS. However, it appears our feature engineering actually creates a short cut for the AI to exploit!

### Experiment 2
Our aim was to optimise on the tabular. We use a simpler XgBoost alongside our engineered features due to time constraints and easy explainability. This model scored 8.40696 on its validation set (MAE) suggesting attributes of the patient such as how old they are, their gender and how many years ago their biopsy was taken, are a bigger indicator of OS than images of the microenvironment.

![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn/blob/main/images/feature_importance_tabular.png)
![](https://github.com/BurgundyIsAPublicEnemy/ORLIAn/blob/main/images/sampleTrainingHead.png)

Feature 2 is our modified DDN (which shows the current age of the patient had they survived up to now). Feature 4 is our modified age they would've been when they got their diagnosis. The graph shows they had a large role to play in the predictions.

## Take-away
There are right answers. And then there are the answers people want. Yes, the model had somehow reverse engineered the target but in the grand scheme of things, that was kinda useless. So Take 2 had to remove it. So we didn't get this situation again.

# Performance on Challenge Macro
Eln-AI-ny scored 0.83 against the macro using this submission when we allowed the leaky features in. 

But scored 5.78 when taking them out. The visualizations are a lot better.
