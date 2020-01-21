---
layout: "post"
title: Building a Food Classifier using Google Cloud Vision API
date: December 2019
permalink: /p/food-classifier
---

# Food Classifier using Google Cloud Vision API
Classification algorithms are interesting problems in Deep Learning and Computer Vision. One limitation though, is the number of classes they can predict from. You read that right. If the classifier hasn't seen a class / category, it won't be able to classify it. That's the case with every thing in Supervised Learning. They only know what you train them for.

A few services are testing this limit of the number of 

## Google Cloud Vision APIs
Google Cloud Vision APIs are pretrained, high accuracy Deep Learning APIs to help you make sense of photos.
Cloud Vision APIs help you
* Identify the objects in images
* Recognize text (OCR)
* Label Images. This feature is great for labeling your training data.
* Filter Explicit / Adult content
* Indentify logos and popular places
* Product Search. 

## Advantage of Using Cloud Vision API
No need to go through the learning curve of understanding Neural Networks, collecting data, training on expensive hardware and then optimizing your models for accuracy and fast predictions.
If you are already a Front-end or Mobile Developer, Vision API is can help 

Vision API is doing for Computer Vision what Serverless frameworks did for Mobile apps. i.e. eliminating the need for Deep Learning Expert (No offense to my DL Expert friends, I myself am trying to become one) (like Bank-end Developer).
Keep in mind that the value provided by Vision API is higher compared to what Serverless provided. Back-end Developers are mostly involved in API development. But Deep Learning Experts have to go through the whole process of defining NN arch, training, optimizing.

But! You might know the cost of running is not very economical.

## What else can you develop with Cloud Vision API?
- Use Vision API to label your images. Great for compiling training Dataset if (ironically) you want to build a Supervised learning algorithm.
But they accuracy is not always great. For 

- Product Search
Developing an E-comerce SaaS? Build a great product search experience for your customers. They supply the image they've seen taken and you give them the most look alike product - Or if you're amazon - the exact copy of the product.


## Labeling Images with Cloud Vision SDK
### Create a Google Cloud Project
TODO: link to Cloud Console Page
### Enable Cloud Vision for your project

Google Provides $300 in the free tier, avaiable for usage for up to a year. Don't worry if you have burnt through that credit. Cloud Vision gives 1000 free requests every month.


## Labeling image with Node.js SDK

TODO: Link to getting started
Install Cloud Vision library for Node.js
```bash
$ npm i -S @google-cloud/vision
```

Set the env variable

Label images
```js

async function getLabels(image) {

  const vision = require("@google-cloud/vision");
  const client = new vision.ImageAnnotatorClient();

  // Performs label detection on the image file
  const [result] = await client.labelDetection(image);
  
  const labels = result.labelAnnotations;
  labels.forEach(label => console.log(label.description));
}
```
It's that easy. 

## Link to Code

## Demo
https://emadehsan.com/e/food-classifier
