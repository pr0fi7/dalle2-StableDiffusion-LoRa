# Stable Diffusion Tech Talk

## Introduction
Welcome to the repository dedicated to exploring various approaches to generating consistent images of people using DALL-E 2 API, stable diffusion, and LORA stable diffusion. This README provides an overview of each project and directs you to their respective Git repositories for more detailed information.

## Projects
1. **DALL-E 2 API**: This project explores the capabilities of the DALL-E 2 API for generating images of people based on my previous project.
   - [Git Repository:](https://github.com/pr0fi7/dalle2app)

2. **Stable Diffusion**: Utilizing stable diffusion techniques for denoising images and enhancing consistency in people generation.
   - [Git Repository:](https://github.com/pr0fi7/image_inpainter)
   - Additional Resources:
     - [Article 1](https://stable-diffusion-art.com/how-stable-diffusion-work/)
     - [Article 2](https://jalammar.github.io/illustrated-stable-diffusion/)
     - [Inspiring Video](https://www.youtube.com/watch?v=sFztPP9qPRc)

3. **LORA Stable Diffusion**: Investigating LORA stable diffusion methods for further improving image consistency.
You can find `AutoTrain_Dreambooth.ipynb` file with which you can finetune stablediffusion model.

## Presentation
In this repository, you'll also find a `Presentation.pdf` file containing a detailed presentation with supporting images. This presentation delves into the topic of stable diffusion and its applications in image generation.

## The content of presentation

# How does stable diffusion work?

Firstly, it's important to recognize that stable diffusion isn't a singular model but rather a composite of several models within one framework. Let's begin by delving into one of its key components, known as a convolutional network. This network type autonomously learns feature engineering through filters called kernels.  

Images are hard to process and unique in a sense. Imagine if we were creating regular neural network for a 100 by 100-pixel image of a fish. We would have 10,000 inputs and outputs, and three channels for red, green, and blue, it is just too many parameters. In a neural network, each input typically contributes equally to each output, which doesn't make sense for images. Pixels close to each other carry more relevance in forming features like edges compared to 2 randomly positioned pixels. 

That’s why, we use convolutional networks, how are they different, well they learn feature engineering through kernels. What is a kernel? For example, here you can see a kernel for detecting horizontal edges, the kernel is just a square matrix with odd number of rows and columns because basically it works by changing the neighbors of central pixel which is of course impossible when you don’t have a central number (in case of even number matrix). However, even with convolutional networks, images may not be as informative as they can be. In fact, now we capture only 3 dimensions: height, width and 3 channels, but if we could bring it to higher dimension so we can extract additional features like brightness, shadows, textures such as cutting boards or whatever other features that might be present there.  

And let’s say that we added some extra dimension and now we have a kernel that is much deeper 3*3*64 which is incredible, but now we have so many parameters that image is very hard to process and moreover our kernel is not efficient as it cannot really capture any edges in this format, and so what we can do? Make kernel bigger – which again will increase the number of parameters, or we can make an image smaller, which is a brilliant approach. So firstly, we take an image then we make it smaller and extract more features making it higher dimensional, we call it: bringing it to latent space, and then we scale it up. How do we scale up an image back? We just store the information about how we take each step as we bring image to latent space and then we can just slap this information during upscaling stage and in this way, we don’t lose any info. In literature this information is called residual connections. Why is this approach brilliant? It has something to do with what is called manifold theory. The thing is that natural images are not random. They have high regularity: A face follows a specific spatial relationship between the eyes, nose, cheek, and mouth. A dog has 4 legs and is a particular shape. So, we can compress images without losing information. You can check and play with cool webapp where developers converted famous MNIST dataset of handwritten numbers in a latent space using autoencoder. [MNIST Latent Space](https://n8python.github.io/mnistLatentSpace/)

So far, our focus has been on image segmentation, particularly the U-net model, which forms the cornerstone of stable diffusion. Now, let's explore how stable diffusion is trained. 

1. A bit noised image is created based on the provided image.
2. The noise predictor estimates the noise. 
3. If it gets it wrong, it calculates the loss. 
4. It adjusts parameters (backpropagates). 
5. In the end, it tweaks all the parameters so we have the model that can really predict the noise that was added. 

Once good, sampled noise is predicted we can subtract it from the initial a bit noised image, and we should get an original image. 

But in fact, it is a bit harder than that, because it is very hard to go back from a noised image to original image in one step so during our training we add different amounts of noise each time so our noise predictor can extract sample noise from any noise and we do denoising in several steps as well.  

And so here is an example of the denoiser with denoising step of 2: the sample is being predicted, we subtract this noise from the input, and we get some noise, then we again predict the noise based on the input and we take 2nd sample noise and we subtract 2nd sample noise from 2nd input and now we should get an initial image. 

But so far, our stable diffusion can only create different fishes or whatever it was trained on, we need to add something that is called conditioning namely text conditioning. Which is done using as they are called self-attention layers. Convolutional layers extract features from images depending on their spatial position and self-attention layer extract features from phrases based on their embeddings. 

The current version of stable diffusion uses the algorithm created by OpenAI called CLIP which basically consists of image and text encoders. It was trained on images and their captions, in simple terms, training as follows: you provide a text: “image of a dog” and also an image of a dog. Then the model creates embeddings for both image and a text and then it compares similarity score between the two. Initially the similarity score is very low but then using backpropagation after some iteration is quite high. Now we have trained text encoder that can capture the meaning of the text prompt so its embedding is similar to the embedding of the image that the provided prompt would correspond to.  

So now I can finally tell the truth that in fact stable diffusion is being trained on image, text and amount of noise to be added, and so now initial image is being scaled down and more dimensions are being added in the same time we inject our text embeddings in each step as we go into latent space and in the latent space, we train our noise predictor so it predicts the noise of the noised image with text embeddings added it backpropagates adjusts all the parameters and in the end we have a good noise that we can subtract in several steps from inputs to get the original image. 

So now when we input a prompt in the trained stable diffusion model, we know that the text is being embedded as the image that corresponds to the text would be, it is being fed to our denoiser which starts from random noise and since the denoiser was trained on billions of images and since you get quite a lot of info due to high dimensionality in the latent space, our denoiser can predict noise for the image that would correspond to the prompt quite well. This predicted noise is subtracted from the initial noise in several steps, and we get the image that we wanted in the end. 

Lastly, I would like to share an analogy that I came up with during a tech talk which may be confusing for those who are not in the topic but maybe very interesting for those who are in: noise that stable diffusion starts with is like photons in quantum mechanics: they are everywhere and when you start to capture them (observer effect) in our case it is a text prompt, the noise transforms into one unique image.  


Feel free to explore each project and delve deeper into the fascinating world of image generation and stable diffusion techniques!

