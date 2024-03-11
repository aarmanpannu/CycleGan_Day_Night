# Day to Night Skyline Image Translation Using CycleGAN 
Project Members: Rohan Vasishth and Aarman Pannu

Project Objective: Our project’s objective was to use the CycleGAN for an image-to-image translation to translate between day and night images. In order to do this, we leveraged a day night [dataset](https://www.kaggle.com/datasets/heonh0/daynight-cityview/data?select=day) of city skylines.

# Sample Results
![night_image_pair_15](https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/b2960aba-d2d5-42cd-a0fc-5ebf7b22be72)
![day_image_pair_18](https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/620e008a-fe01-48e5-9fe1-717b0de667d3)
![day_image_pair_27](https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/9a3fbbda-d9af-4e41-a1a9-4eac99c54967)
![night_image_pair_12](https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/9325b3c8-f230-419e-a70b-d823907a17c0)


# Project Methods
Similar to the approach of the main [CycleGAN paper](https://arxiv.org/pdf/1703.10593.pdf), we leverage a generator and discriminator approach to translate between the day and night images. We have five main components: data loading, the discriminator model, the generator model, training the model, and getting the results.

Data Loading
	Our Data Loading class loads the DayNight Dataset that contains paths to the root directory of the day images folder and one to the night images folder. Our dataset had 227 Night Images and 522 Day Images. We split our dataset, into training which contains 488 day images for training, 208 night images for training, 34 day images for testing, and 19 night images testing. Because the training set of day images is over twice as big as the night images for training, we index the night images modulo the size of the night images. This means that we overrepresent the night-images in training, although this did not lead to a significant decrease in evaluation. Additionally, the paper had this issue as well for its datasets, and it did not appear to be a problem.
Additionally, we apply standard transformations on the images as seen in the paper. We resize the images to 256x256 pixels, randomly applying a horizontal flip (with probability 0.5), and normalizing the image before turning it into a tensor.
The paper describes using a batch_size of 1, which although we tried using a larger batch size ran into memory issues as it was not able to store more images for training purposes.

Building the Discriminator
To start, the high level purpose of our discriminator was to be able to identify whether an image was a real or fake day/night image. By doing this, our generator could generate better image translation results. The following diagram illustrates the role of the discriminator and is inspired by figure 3 from the official CycleGAN paper:

<p align="center">
<img width="214" alt="Screenshot 2024-03-11 at 12 24 21 PM" src="https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/02bf4d7f-2e04-4c85-895d-aec7f03d9b72">
</p>
<p align="center">
Figure 1. Discriminator Role in Model
</p>

DD distinguishes between a real or fake day image. DN distinguishes between a real or fake night image. Our discriminator model has a relatively simple architecture. As discussed in the paper, we have four layers: C-64 to C-128 to C-256 to C-512. We use a leaky ReLU as recommended with a slope of 0.2. Finally, we also don’t use InstanceNorm within our first layer. The final output is 1-dimensional to report a distinguishing score.

<p align="center">
<img width="358" alt="Screenshot 2024-03-11 at 12 24 40 PM" src="https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/49453406-d504-417f-ab26-4e77902c0cd7">
</p>
<p align="center">
Figure 2. Discriminator Architecture
</p>

Building the Generator
The generator model’s primary purpose was to translate day images into night images and vice-versa with an un-paired set of training data. The following diagram illustrates how we thought about this process and is inspired by figure 3 from the original paper:

<p align="center">
<img width="247" alt="Screenshot 2024-03-11 at 12 24 59 PM" src="https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/9ea681b6-ded5-4611-87d2-c23e0ca188ba">
</p>
<p align="center">
Figure 3. Leveraging Generators to Translate Between Day and Night Images
</p>

The architecture of our generator has 3 components: encoder blocks, residual blocks, and finally, decoder blocks. We used 9 residual layers as suggested by the paper for images that our 256x256 or bigger.

<p align="center">
<img width="380" alt="Screenshot 2024-03-11 at 12 25 18 PM" src="https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/0186406a-7255-4200-80fb-6f2924b753a9">
</p>
<p align="center">
Figure 4. Generator Architecture
</p>

Training the Model
To train our model, we used two discrimantors and two generators (one for day and one for night). We calculate two types of losses: adversarial loss and cycle consistency loss. These losses are combined to report a final loss and train the model. We ran 250 epochs. The following diagram visualizes how we thought about computing total loss:

<p align="center">
<img width="559" alt="Screenshot 2024-03-11 at 12 25 36 PM" src="https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/f3d7d7b1-6bf5-486b-ad5a-8405578bea4c">
</p>
<p align="center">
Figure 5. Computing Generator Loss
</p>

# Project Results

Here is a small fragment of our results. In the first image, we go from an original night image to a fake day image and then try to go back to the original night image. In the second grid of images, we start from a day image, go to a fake night image and then try to regenerate the original day image. All 50+ result images can be seen in the accompanying Notebook file or results folder.

<p align="center">
Real Day | Generated Night from Real Day | Generated Day from Generated Night
</p>
<p align="center">
<img width="418" alt="Screenshot 2024-03-11 at 12 30 46 PM" src="https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/af26c7c4-8725-42d6-b207-115dc2236f72">
</p>
<p align="center">
Real Night | Generated Day from Real Night | Generated Night from Generated Day
</p>
<p align="center">
<img width="422" alt="Screenshot 2024-03-11 at 12 29 48 PM" src="https://github.com/aarmanpannu/CycleGan_Day_Night/assets/112771299/3c642504-ec70-4f9b-817a-1e9fade369d7">
</p>

# Further Improvement and Next Steps
Dataset and DataLoading Image Transformations
As mentioned before, our Dataset is relatively small compared to other implementations’ and the Paper’s datasets. We think that having a larger dataset could greatly improve our model’s performance. The original paper uses random square crops of the original images for their Monet’s Paintings to Photos CycleGAN implementation, which is one approach we think could lead to substantial improvements in our implementation. Our resizing had distorted the images significantly since the images are significantly larger in size than the desired 256x256 squares, and most often of a 2:1 aspect ratio. Essentially, we could have tested some alternative image transformation techniques when loading in the data that resulted in less image distortion. 

Generator Model
	There appears to be a slight grid artifact on all the generated images which can be seen when zoomed in incredibly closely, which is especially prominent in the fake (generated) night images. We think we can avoid this grid by applying larger kernel sizes at the final convolution step.
