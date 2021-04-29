# Overview:
Image-to-Image translation is another example of a task for which Generative Adversarial Networks (GANs) are perfectly suited for. These are tasks in which it is nearly impossible to hard-code a loss function. Most studies on GANs are concerned with novel image synthesis, translating from a random vector z into an image. 

![image](https://user-images.githubusercontent.com/82496384/115501682-46665000-a291-11eb-9e24-c799cf9b4374.png)

Image-to-Image translation converts one image to another such as the edges of the bag above to the photo image. Image-to-Image translation is also useful in applications such as colorization and super-resolution. However, many of the implementation ideas specific to the pix2pix algorithm are also relevant for those studying novel image synthesis.

 
A very high-level view of the Image-to-Image translation architecture in this paper is depicted above. Similar to many image synthesis models, this uses a Conditional-GAN framework. The conditioning image, x is applied as the input to the generator and as input to the discriminator.

# Generator:

![image](https://user-images.githubusercontent.com/82496384/115501706-4ebe8b00-a291-11eb-85e0-de21a6e43dcd.png)

The volumes are there to give you a sense of the shape of the tensor dimensions next to them. The input in this example is a 256x256 image with 3 color channels (red, green, and blue, all equal for a black and white image), and the output is the same.


The generator takes some input and tries to reduce it with a series of encoders (convolution + activation function) into a much smaller representation. The idea is that by compressing it this way we hopefully have a higher-level representation of the data after the final encode layer. The decode layers do the opposite (deconvolution + activation function) and reverse the action of the encoder layers.

 

In order to improve the performance of the image-to-image transform in the paper, the authors used a “U-Net” instead of an encoder-decoder. This is the same thing, but with “skip connections” directly connecting encoder layers to decoder layers:

![image](https://user-images.githubusercontent.com/82496384/115501718-541bd580-a291-11eb-8fe0-8ff9a6ba6a86.png)

The skip connections give the network the option of bypassing the encoding/decoding part if it doesn’t have a use for it.


For instance, the first and last layers of the network have no batch norm layer and a few layers in the middle have dropout units.

# Discriminator:
The Discriminator has the job of taking two images, an input image and an unknown image (which will be either a target or output image from the generator), and deciding if the second image was produced by the generator or not.

![image](https://user-images.githubusercontent.com/82496384/115501722-5aaa4d00-a291-11eb-8598-256a4ae66ec2.png)

The structure looks a lot like the encoder section of the generator but works a little differently. The output is a 30x30 image where each pixel value (0 to 1) represents how believable the corresponding section of the unknown image is. In the pix2pix implementation, each pixel from this 30x30 image corresponds to the believability of a 70x70 patch of the input image (the patches overlap a lot since the input images are 256x256). The architecture is called a “PatchGAN”.

# Training Process:

![image](https://user-images.githubusercontent.com/82496384/115501744-6138c480-a291-11eb-9cc0-1735cd25b61d.png)

To train the discriminator, first the generator generates an output image. The discriminator looks at the input/target pair and the input/output pair and produces its guess about how realistic they look. The weights of the discriminator are then adjusted based on the classification error of the input/output pair and the input/target pair.


The generator’s weights are then adjusted based on the output of the discriminator as well as the difference between the output and target image.

# Evaluation:
Evaluating GAN outputs are difficult and there are many different ways of doing this. The authors of pix2pix use two different strategies to evaluate their results.


The first strategy is to use human scoring. Real images and images created with pix2pix are randomly stacked together and human scorers label each image as real or fake after seeing it for 1 second. This is done using the Amazon Mechanical Turk platform.


Another evaluation strategy that I found to be very interesting was the use of a semantic segmentation network on the synthetically generated networks. This is analogous to another very popular quantitative evaluation metric for GAN outputs known as the “Inception Score” where the quality of synthesized images is rated based on a pre-trained Inception model’s ability to classify them.

# Conclusion:
Pix2Pix is a very interesting strategy for Image-to-Image translation using a combination of L1 Distance and Adversarial Loss with additional novelties in the design of the Generator and Discriminator. Thanks for reading, please check out the paper for more implementation details and explanations of experimental results!

Useful Links:
 

### Research Paper: 
https://arxiv.org/abs/1611.07004

### GitHub repo: 
<a href='https://github.com/phillipi/pix2pix'>phillipi/pix2pix</a>
