# Design a Self Driving Car/Image Segmentation System
- This problem is a good example of handling multi sensor inputs 
- Primarily focuses on computer vision though
	- Object classification
	- detection
	- segmentation 
# Problem Statment
_Design a self driving car focusing on its perception component_
(Semantic image segmentation in particular)
- Most multi sensory computer vision problems can be solved in similar ways

Image => Perception Component => Understanding of the surrounding (labelling the images)

## Limiting Scope + Key Questions
1.  How would you approach a computer vision-based problem in terms of the self-driving car? 
	1. **Subtasks**
    
2.  How would you train a semantic image segmentation model for autonomous driving?
	1. **Architecture**
    
3.  How will the segmentation model fit in the overall autonomous driving system architecture?
	1. **Architecture**
    
4.  How would you deal with data scarcity in imaging dataset?
	1. **Training Data Generation**

5.  How would you best apply data augmentation on images?
	1. **Training Data Generation**
    
6.  What are the best model architectures for image segmentation tasks?
	1. **Modelling Lesson**

7.  Your optimized deep learning model gives a high performance on the validation set, but it fails when you take the self-driving car on the road. Why? How would you solve this issue?
	1. **Training Data Generation**




## Hardware support
- Self driving cars need support from sensors to act as the eyes, ears etc 
- Sensors for _eyes_:
	- Camera
	- Radar
	- Lidar
comparison of the 3:
![[Pasted image 20220415090901.png]]
- Sensors for _ears_:
	- Microphone

Say its raining? the road will _sound_ different so the car might slow down


## Subtasks
Okay so build a self driving car ... whoa whoa whoa chill out thats a big ol task. 
We can break this down into multiple different phases

1) Object detection - Find the objects in the scene
	- Draw Bounding Boxes to localize different classes of objects
2) Semantic Segmentation - pixwelwise classification for what each object is
	- okay but say you have MANY MANY objects that are in frame and its overwhemling to draw boxes around everything ... then you need to go a level deeper and do pixwel wise classiciation instead
3) Instance Segmentation - How many of each of the different objects do we have? 
	- Okay you can classify objects but what if there are many instances of objects you need to be able to identify and separate person 1 from person 2 and car 1 vs car 2 3 or 4
4) Scene Understanding - Whats going on in the scene? (driving on a mountain or are you in traffic)
5) Movement Plan - You identified where things are and whats going on so now what do you do?


**WE ARE REDUCING THE PROBLEM DOWN TO:**
_Perform semantic segmentation of the self-driving vehicles surrounding enviornment_

# Metrics
**The problem with pixelwise accuracy**

## [[IoU]] - Offline

## Online
You need an **Online** end to end Metric to test the OVERALL performance of the self driving car system

### Manual Intervention
- If a system rarely has manual intervention then it is considered a good system
- During early phases of testing we can use an expert driver during tests to see when the system would make mistakes and change them 
### Simulation Errors
- You can get an expert driver to go for a drive and collect video footage this footage can then be used to test out your self driving car
- Expert driver is the ground truth


# Architecture 

## ML component design
![[Pasted image 20220415100448.png]]
## Component Explanation
**Object Detection:** The first component will be drawing bounding boxes over each of the seperate images
**Drivable Region Recognition:** Finding out if the road is clear or not is so important it gets it's own CNN model and is deemed a separate subtask
**Semantic Segmentation:** Okay you have your bounding boxes but now it do pixel wise classification to start separating between different people or different cars etc and do a precision classification
**Scene Understanding:** Take the outputs from the camera sensors + the object detection, drivable reigon and semantic segmentation models to form an understanding of wtf is going on
**Depth Measurement:** Use audio sensors to create an understanding of depth but we can also use **Lidar Sensors** to form a 3D model of the world. This will allow us to improve our depth measurement

**Action Predictor:** Take the outputs from the visual understanding components along with the previous action (to make informed decisions show in the image below) to come up with a series of actions or combination of actions for the car to perform like slow down, turn etc



## Design including how the action predictors work at timestep t to t+n
![[Pasted image 20220415101339.png]]

## General System Design - for image segmentation
![[Pasted image 20220415102520.png]]

# Feature Engineering
Features are engineered through the CNN 
# Training Data Generation
**Two methods**
- Collect training data from people who record videos with their cars and have humans label them
- Use open source datasets + GAN (to augment and generate new data)

Given the context of the problem our self driving car has to be INCREDIBLY robust with no margin for error because well someone could die? 

## Human Labelled Data
You can get human labelors to label training data and draw bounding boxes 
Label Box - Tool to do this
## Open Source Datasets
There are plenty of open source datasets with videos and images 
They contain segmented data for full frames and since driving is a fairly universal task we can use this as part of our training data as well

BUT (an example) - These images are potentially only for sunny weather what happens if your model is trained only on sunny weather and your model encounters a snowy day or a heavy rain storm? its gonna flop

So what can we do?
## Augmenting Data - [[GANs]]
Take the training data we have and augment it with the use of GANs - more info about gans explained on the [[GANs]] page. 

What GANs can do
- Generate New training samples to learn from
- Image Translation - so generating the same image with different features like converting a sunny road to a snowy road

## Image to image translation - [[cGANs]]
What exactly is image translation and how is it set up?

Image translation is essentially mapping features from one input to another image

How do cGANs work?
The training data actually consists of image _pairs_ 
![[Pasted image 20220415124546.png]]

![[Pasted image 20220415124615.png]]


cGANs loss function differs to GANs because of the conditioning on the label


## Targetting Data generation

So you've trained your model and are now testing it on the road with an expert driver

During the test the driver had to intervene when say someone was jay walking 

What you can now do is take training data that was specific to that problem and include it in your new training dataset so that the model can start to generalize better since we need to be able to capture as many situations as possible 


# Modelling
## SOTA segmentation models
### [[FCN]]
#todo
### [[U-Net]]
### [[Mask R-CNN]]

## [[Transfer Learning]]
