---
title: 'Show Off Your Computer Vision Model with Gradio'
date: 2022-08-12
venue-type: blog
permalink: /posts/2022/12/gradio-demo/
tags:
  - blog-post
  - research
  - tooling
---

You can also read this article on [dev.to](https://dev.to/haoliyin/show-off-your-computer-vision-model-with-gradio-1k3o)

Most of machine learning development originates from academia, which is often very technical and hard to understand for the layperson. Personally, I enjoy being able to interact with the end result with my own inputs without having to understand the technicalities of the model until later. Another motivation to write this piece is that I haven't seen many public articles or examples using Gradio and HuggingFace hosting to demo computer vision models with image input and output as HuggingFace is traditionally a hub for natural language processing (NLP) knowledge (but they're shifting to incorporate more of other ML domains like computer vision). 

## What is HuggingFace and Gradio
![Gradio and HuggingFace Logos](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1u64v34tov7a3tdqitrz.png) 
No, HuggingFace is not those face grabbers from the movie [Aliens](https://en.wikipedia.org/wiki/Aliens_(film)) (although that's what I first thought it was). It's actually the hugging emoji that's now at the top of my most-used emoji keyboard list! HuggingFace is not only a hub for ML models and demos but also a Python library that allows anyone to create and train select model architectures without having to write it all from scratch (e.g. transformers). For our case, HuggingFace Spaces allows us to freely host our model for public deployment without any extra fees, full-stack development knowledge, or headaches using other platforms. Note that demoing using GPU does cost extra, so I will be showing a CPU-only example. 

Gradio, on the other hand, is the Python library that allows for user-friendly functions and front-end development using a single line of code. This interface can be used to create simple visual demos like mine (link in next paragraph) to complex interfaces like [this one](https://huggingface.co/spaces/skytnt/full-body-anime-gan).

## Demoing my Research Project
In this tutorial, I'll show you how to quickly deploy a demo of a model that takes an image input and outputs an instance segmentation map overlayed onto the image. This demo is related to a portion of my research project to detect [specular reflection](https://en.wikipedia.org/wiki/Specular_reflection) regions created from light reflected off wet organ surfaces and blinds the camera to the true colors and features in that region. The generated segmentation map is later used to restore the region using information from adjacent frames of the endoscopy video. **Trigger Warning**: Blood, Biological Tissue. You can view my demo [here](https://huggingface.co/spaces/Nano1337/SpecLab). 

## Creating a Skeleton of the Code
For any code I work on, I like to first write comments and create a version control system to use such as Github (HuggingFace has their own version of Git in this case that's hosted on their platform). To get started, create an account on HuggingFace and make a [Spaces](https://huggingface.co/spaces). From there, you can specify a repository name and license type and select Gradio as the display option. 
![Making spaces](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ceb1zszkjcg3wii6hdxb.png)
Then, you can either clone the repo to your local storage using git bash or directly modify your code on HuggingFace. Next, there are two important files to create: requirements.txt and app.py. The requirements text file will store all the pip library dependencies that need to be installed in the HuggingFace hosting environment to run all the Python libraries you use in your code. The app.py file will define how you want your interface to look like and function. The most basic app you can first make is kind of like a "hello world" program using Gradio, which they show you after you make the repository. 

```
import gradio as gr

def greet(name):
    return "Hello " + name + "!!"

iface = gr.Interface(fn=greet, inputs="text", outputs="text")
iface.launch()
```
Let's dissect this code a bit. The import statement is telling Python that you want to use all of Gradio's functionality (assuming that you have put this into requirements.txt). The function "greet" is where you will put all the machine learning model logic to process an input and return a prediction or output from your model to be displayed. The Interface is the main function that defines how you want your demo to look - we will dive more into the options later in this post. Then, all you need to do is to launch the demo and HuggingFace will deploy all this code for you automatically. It's as easy as that!

## Defining model behavior 
Now that we have a skeleton of the code we want to write, we will fill it in with the desired functionality. Instead of using the greet function, we want to create a function that will accept an image (read by Gradio as a NumPy or PIL image), preprocess it to be fed into the model, run it through the model with a forward pass for prediction, and output a segmentation map overlayed onto the original image. 

```
def speclab(img):

    # initialize the model
    model = torch.hub.load('Nano1337/SpecLab', 'srdetect', force_reload=True) # for some reasons loads the model in src rather than demo
    model.eval()

    # preprocess image to be used as input
    transforms = A.Compose([
        A.Normalize(mean=(0.5, 0.5, 0.5), std=(0.5, 0.5, 0.5)),
        ToTensorV2()
    ])
    input = transforms(image=img)['image']
    input = input.unsqueeze(0)

    # model prediction
    output = model(input)

    # overlay output onto original image
    img[output==255] = [0, 255, 0]

    return img
```
A small detail that you may notice is that I used torch.hub to load my model from a saved location in my personal Github account. You can see how I made a [hubconf.py file](https://github.com/Nano1337/SpecLab/blob/main/hubconf.py) there to specify how I wanted to load my model for external usage. 

# Importing Example Images
Since the subject I'm working on is quite niche, users won't always have the appropriate data to input into the demo. We can define ready-to-be-used examples to be shown. I stored the images in the same personal Github repo as shown here and I imported the raw image address as shown below. 

```
from urllib.request import urlretrieve

# get image examples from github
urlretrieve("https://github.com/Nano1337/SpecLab/blob/main/examples/05829.png?raw=true", "05829.png") # make sure to use "copy image address when copying image from Github"
urlretrieve("https://github.com/Nano1337/SpecLab/blob/main/examples/10384.png?raw=true", "10384.png")
examples = [ # need to manually delete cache everytime new examples are added
    ['05829.png'], 
    ["10384.png"]
```
Another small detail you may notice is that I specified later on in the code that I wanted these examples and their outputs to be cached so that users won't have to wait for the example images to be run through the model (which can take significantly more time since the model is run on CPU only). 

# Creating the Interface
Now comes the front-end work, which is thankfully really easy for a noob webdev like me. Below is my code to define the demo's look using the Interface function, but Gradio also has more customizable and advanced functionality if you want more front-end flexibility in their documentation. 

```
# define app features and run
title = "SpecLab Demo"
description = "<p style='text-align: center'>Gradio demo for an ASPP model architecture trained on the SpecLab dataset. To use it, simply add your image, or click one of the examples to load them. Since this demo is run on CPU only, please allow additional time for processing. </p>"
article = "<p style='text-align: center'><a href='https://github.com/Nano1337/SpecLab'>Github Repo</a></p>"
css = "#0 {object-fit: contain;} #1 {object-fit: contain;}"
demo = gr.Interface(fn=speclab, 
                    title=title, 
                    description=description,
                    article=article,
                    inputs=gr.Image(elem_id=0, show_label=False), 
                    outputs=gr.Image(elem_id=1, show_label=False),
                    css=css, 
                    examples=examples, 
                    cache_examples=True,
                    allow_flagging='never')
demo.launch()
```
Let's walk through this code together. The title and description are self-explanatory and the article shows some more text at the bottom of the screen. If you're front-end savvy (which I definitely am not), you can also define custom CSS to pretty up your interface. We specify both the input and output components to be Gradio images, but these can be any one of the [components](https://gradio.app/docs/#components) that Gradio supports (similar to the diverse components found in React.js components if you're familiar with that framework). Other parameters are just nice-to-haves that can be found in the [Interface documentation](https://gradio.app/docs/). 
![End Result](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8syiqyelvvw9u7qagn4k.png)
 
## Congrats
There you have it! After about 50 lines of code (or even less), you have a functioning demo that doesn't cost anything to deploy. Feel free to share this article, and if you like this content, let me know what you would like to see next from me! Happy deep learning my friends :))







 