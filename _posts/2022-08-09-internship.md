---
title: 'My Summer 2022 Undergraduate Data Science Internship'
date: 2022-08-09
venue-type: blog
permalink: /posts/2022/08/internship-2022/
tags:
  - blog-post
  - research
  - tooling
---

You can also read this article on [dev.to](https://dev.to/haoliyin/my-summer-2022-undergraduate-data-science-internship-2c1k)
## The Job Hunt

Just like anyone new to internship hunting, I made both mistakes of applying late in April and just throwing my resume everywhere. In the end, I only had two responses, one that ended after a phone screen and a technical screen, and the other one being the company I eventually worked at - Lynntech. The interview process was quite lengthy at the time (they significantly shortened it later) with two take-home assignments and two rounds: one that was two hours and the other being a whopping five hours! 

During the first interview, I learned a bit about the company, but I mostly explained my previous experience and the research paper I had written a couple years ago. Since it was an R&D position, I was glad I could embrace both the academic and professional environment in one setting. Since my interviewers (later my supervisors) were happy with my explanations, I moved on to do the second technical take-home assessment, which consisted of some basic leetcode-esque questions and computer vision applications, which I thought were quite fun to complete. The second round of interviews was something known as a "superday". This was a chain of interviews that started with a more detailed introduction to the company followed by a presentation I had to give to the entire team on the current research I was working on to show my technical thinking and communication ability. After this, I had to sit through an hour-long technical deep learning interview using transfer learning and finally a meeting with HR. This was probably the most difficult interview process that I had ever encountered, and it was even more so since it took place on the day before my first class final! Even though it was difficult, I learned so much just through the interview process, and I'm excited to see what happens this upcoming year. 

## What Lynntech is
![SBIR/STTR logo](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zutxop2bh278cva59nek.jpg) 
Lynntech is an R&D company from software to wetlab biology research that writes proposals for government grant money through the SBIR/STTR program. Essentially, the government posts several topics they're interested in receiving project proposals from throughout the year, and Lynntech tries its best to get this "seed funding" from the government by leveraging their previous work and experiences - it's almost like a project accelerator program for startups. The contracts that they receive are mostly from the department of defense or NASA, but they sometimes also receive grants from the NIH for healthcare-related projects. In the following sections, I'll talk more about the couple of amazing projects that I was able to get involved in (without violating any NDA of course haha).

## Project 1: GPU-accelerated State Estimation
![Lambda Station GPU server used for project development](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6qjmxdxij1b6b32hq5l1.png)
Since I had recently taken a C++ class, the first and main project I focused on throughout my time there was more of a software engineering one.

### The Problem
The objective of this project was to create a way to simulate the trajectory of a vehicle given its initial state and sensor inputs throughout the duration of travel. This project had already been worked on for a couple of years since I had arrived, and a working simulation had been made in MATLAB. The problem was that the Airforce wanted the code to run faster, which required runtime acceleration with GPUs - something that MATLAB doesn't natively support for this particular application. Thus, Lynntech won a grant to create a proof-of-concept that GPU acceleration can speed up the code implemented in C++. This proof-of-concept was already created a couple of years ago before I joined, but as the name implies, it's not a minimum viable product (MVP). 

I worked with two other coworkers to ideate from the very beginning how to create the structure of this backend engine in an organized and efficient object-oriented manner. Once we took a week to decide on the design, we started making the skeleton of the code and divided up the work to get started. 

### MAGMA GPU Acceleration and Utility Functions
The main library we used to enable GPU-accelerated process was a CUDA-wrapper called MAGMA, which handled linear algebra matrix operations on the GPU instead of directly having to work with nasty CUDA code. Using MAGMA, however, was not trivial. The documentation and examples provided were difficult to understand, and this was a very niche topic, so there was not much out there to learn from. To begin, I made a suite of 20+ utility functions for linear algebra operations to wrap the MAGMA functions so that we didn't have to worry about directly using MAGMA. This proved quite helpful in reducing the time spent on debugging and headaches in general. 

### Sensor classes
In the next level, we created the sensor classes to process sensor data into the trajectory estimation model. We had to read in JSON data and make sensor-specific functions to make the sensor data usable. In this process, we also used the utility functions I made to make the code look clean! 

### Weighted Non-linear Least Squares State Estimation
This giant beast of MATLAB code was not fun to implement nor debug. To chunk up this massive piece of code, we split it into 8 parts to work on. We used both the utility functions and sensor classes to input data into this mathematical model. The non-linear least squares model worked using gradient descent to estimate the most-probable trajectory since there were many more features than there were variables. Since we wrote all this code in one go (which took several weeks), debugging, as you can imagine, was quite the nightmare. It took about a month to patch up all the memory access issues that resulted from using MAGMA and the 2D matrices collapsed into 1D pointer arrays it required as an input. Thankfully, we completed the debugging and came out with a functional MVP the day before my internship ended! We generated some preliminary results, and the output matched the MATLAB code exactly! Since this was just the MVP, the GPU acceleration was not yet evident due to the great number of optimizations in code that were left to implement such as batch/multi-threaded processing and multi-GPU usage. 

## Project 2: Fooling Classifiers using Adversarial Patterns
![Figure from paper cited below](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fd6vb0lt4fg4wewgixpe.png) 
You can learn more about this problem from my [previous blog post](https://dev.to/haoliyin/adversarial-ml-making-a-turtle-look-like-a-rifle-4ba9). In this case, we were creating both adversarial attack patterns and defenses for classification models targeting vehicles such as cars and trucks. You can see similar work in this [paper](https://www.aaai.org/AAAI22Papers/AAAI-8153.WangD.pdf).

### Collecting data from scale models
Since this project had been already worked on for several years since I'd arrived, I was initially testing the adversarial attack patterns pasted onto smaller scale models of vehicles. To do this, I captured video footage of the scale model vehicle on a rotating platform in a white box with a camera setup that could be fixed at different angles. Once these videos were captured, I wrote some Python post-processing scripts to extract individual frames from the video for classification by the deep learning models we were trying to attack to test how well the adversarial attack patterns worked. I was also able to test how generalizable a certain pattern was for several models as each pattern was generated to target the weaknesses of a single model. I found that how well the adversarial pattern was able to fool a certain classifier depended on the robustness of the model the pattern was generated from. The higher the robustness of a single model, the better the adversarial pattern performs, and the more generalizable it is to attack other models as well. 

## My overall reflections
I'm thankful for this unique opportunity to see what living and working in the real world was like. I thoroughly enjoyed the newfound independence and routine of life beyond academia while still working on cutting-edge applied research that's both intellectually stimulating and exciting. While it can be stressful at times, I hope to continue on this path of research and dissemination of knowledge to prompt intellectual discussions and motivate product creation through technology.  