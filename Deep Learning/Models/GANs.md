[[Design Image Segmentation]]

How GANs work - high level overview
- A class of [[Generative Models]]
- Use two deep learning models that work against each other


![[Pasted image 20220415111230.png]]

- The Generator creates images from random noise. Initially they will suck ass
- These generated images and the real images that we are trying to generate are sent to the discriminator 
- The discriminator will decided whether its real or fake
	- If the discriminator is wrong (either due to a random guess or whatever) we will update its weights
- If the discriminator is right the weights of the generator will be updated

1) So what the Generator is doing is learning a mapping between the input samples (which are z random noise) and mapping it to something that looks like (in our example a panda) the target distribution
2) The Discriminator is learning to separate real (1) and fake images(0). 
3) The generator is trying to fool the discriminator by learning the true probability distribution 
4) The goal is to get the generator good enough such that the discriminator eventually converges to 0.5 (i.e doesnt know which image is real/fake)


## [[Supervised]] or [[Unsupervised]] Learning
The Generator is learning the the target probability distribution and this is generative modelling so an unsupervisied task but ...

We pose this unsupervised task as a supervised problem due to having the discriminator which is a supervised classifiert hat distinguishes between real and generated images so our [[Loss Function]] can be devised as a Supervised loss function


## GAN Loss Function
The binary [[Cross Entropy]] loss function for the GAN is:

![[Pasted image 20220415112242.png]]

This is a combined loss function for the discriminator and generator

![[Pasted image 20220415112335.png]]

What do each of the terms mean

x is an image form the true distribution
z is a random sample 
$p_{data}(x)$ is the probability distribution of the real images
$p_z(z)$ is the probability distribution of the generated images
G(z) is the output from the generator 
D(G(z)) is discriminators output on the generated image ie. the probability of the generated image being fake
D(x) is the discriminators output that the real image is real
$E_x$ is the expected value over all real data instances
$E_z$ is the expected value over all fake data instances (random inputs to the generator)

The GANs goal is to replicate $p_{data}$ with $p_z$ 
Binary [[Cross Entropy]] is a good measure of distance between these two distributions and we want to minimize it

D(x) is the probability that the datapoint is real
1-D(G(z)) is the probability that the class is fake

The loss function for a single sample measures how far away that sample is from the actual value 
The full loss function is just an average over all samples

### How do we get from Binary Cross Entropy to our Loss function?
[[Cross Entropy|Binary Cross Entropy]] 

The discriminator:

$Loss(y^{pred}, y) = [ylog(y^{pred}) + (1-y)log(1-y^{pred})]$

the loss function when you take an image from the true distribution
sub D(x) for y pred and 1 for y and you will get $Loss = log(D(x))$

When you take an image from the generator the loss function looks like this
sub D(G(z)) for y pred and 0 for y
$Loss = log(1-D(G(z)))$

Add these terms to get the final loss.

**What about the max and min stuff?**

To see why we need the $max_{D}$ plot both terms 
![[Pasted image 20220415123547.png]]


The generators 

The generators loss function is the same as when the discriminator receives generated image 

$Loss(D(G(z)), 0) = log(1-D(G(z)))$

The generator wants to minimize the term 

![[Pasted image 20220415123936.png]]

In order to fool the discriminator i.e D(G(z)) = 1 you need to minimize this term



## Training the GAN

Train the Generator and Discriminator Alternatively 

Discriminator
	Freeze the generators weights. 
	Take data from your real images and create data from your generator 
	Discriminator updates its weights to maximize predicting real images as 1 and fake as 0

Generator
	Freeze discriminator weights
	Take another batch of noise
	The generator updates its weights to minimize the loss term leading to fake images being classified as 1

