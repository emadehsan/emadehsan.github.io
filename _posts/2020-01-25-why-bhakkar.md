---
layout: "post"
title: Why and How - Navigation for Visually Impaired
permalink: /p/why-bhakkar
---

I had a relative who lost their eyesight due to Diabetic Retinopathy. Diabetic retinopathy affects up to 80% of those who have had diabetes for 20 years or more. It occurs when blood vessels in the back of the eye, the retina, become damaged and slowly cause the loss of vision. It is painful to watch your loved ones go through this experience. Tony Stark, Dr Jessica Mega and team did a great job of covering <a href="https://youtu.be/V5aZjsWM2wo?t=959" target="_blank">Diabetic Retinopathy in 'Age of AI'</a>.

<p align="center">
  <img src="/object-detection/dr-clear.jpg" height="200"/>
  <img src="/object-detection/dr-view.jpg" height="200" />
  <p align="center">
    View for a person with clear vision vs for a person with Diabetic Retinopathy 
    (<a href="https://en.wikipedia.org/wiki/Diabetic_retinopathy" target="_blank">Wikipedia</a>)
  </p>
</p>

My relative is not with us today. Diabetes is common in our area (<a href="https://en.wikipedia.org/wiki/Bhakkar_District" target="_blank">Bhakkar</a>). About 30% of people above 40 are diagnosed with it. Many probably don't know it yet. 

If you know a visually impaired person, you are probably familiar with the constant struggle they and their family goes through every day. 

## Problem Statement
> ### How can we make visually impaired people independent to move around

I've not been a student of biology, so my first instinct was to employ technology to solve this problem. I envisioned a smartphone app that would assist a visually impaired person in navigation, in real time. Microsoft had released a tremendous app called SeeingAI - only for iOS - that helps one make sense of their environment and recognize things. The app 'Be My Eyes' employs a network of volunteers who assist visually impaired people in small tasks, by watching live video from their cameras.

## The Plan
I named this project <a href="/bhakkar">Bhakkar</a>. Even though I was planning to work on this problem for a long time, I completed <a href="https://www.deeplearning.ai/" target="_blank">DeepLearning.ai</a> specialization very recently. My naive plan for Bhakkar is

> ### Take a model that can detect plane and depthmap from a single photo, tweak it to work on mobile, in real time

Thus, I went through many papers, barely understanding them. Then I decided to focus on <a href="https://arxiv.org/abs/1812.04072" target="_blank">PlaneRCNN</a> by Liu et al. which seemed to do comparatively better at planar surface detection and depthmap estimation. PlaneRCNN is based on <a href="https://arxiv.org/abs/1703.06870" target="_blank">Mask R-CNN</a> - an object detection and instance segmentation framework by Facebook AI Research. Conceptually, MaskRCNN is similar to <a href="https://arxiv.org/abs/1506.01497" target="_blank">Faster-RCNN</a>. But in addition to Object Detection, it also outputs a mask of where exactly the detected object is in the image.


<p align="center">
  <img src="/object-detection/mask-rcnn.png"/>
  <p align="center">
    Object detection and masks predicted by Mask R-CNN
    (<a href="https://github.com/matterport/Mask_RCNN" target="_blank">Matterport</a>)
  </p>
</p>

The authors of PlaneRCNN treat the planes of scene in an image as object instances. They use Mask R-CNN for detection and segmentation of these objects (i.e. planes). They train the model using a dataset they created from [ScanNet](http://www.scan-net.org). ScanNet is an RGB-D video dataset. An RGB-D image is an RGB image plus information about depth / distance of each pixel from image plane. They took every 20th frame from the video as an RGB-D image, to create an annotated dataset of RGB-D images. They skipped the frames in between because consecutive frames often aren't much different. Although ScanNet is a dataset of indoor scenes, PlaneRCNN generalizes to outdoor scenes comparetively well.

<p align="center">
  <img src="/object-detection/planercnn.jfif"/>
  <p align="center">
    PlaneRCNN output (<a href="https://github.com/NVlabs/planercnn" target="_blank">NVIDIA Labs</a>)
  </p>
</p>

Woohoo... an algorithm that can detect surfaces + objects and can output the depth information of the scene. We are good to go.

Except the problem is that MaskRCNN can run at only 5 frames per second on a GPU. PlaneRCNN adds a component on top of it for depth estimation which adds overhead to an already slow algorithm. 

A naive solution: swap the backend of Mask R-CNN from ResNet to MobileNet. Trade accuracy with speed.

> ### Ignorance is a bliss

## Progress
See the project status at <a href="/bhakkar">Bhakkar homepage</a>.