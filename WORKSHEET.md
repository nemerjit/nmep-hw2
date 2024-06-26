# HW 1 Worksheet

---

This is the worksheet for Homework 1. Your deliverables for this homework are:

- [ ] This worksheet with all answers filled in. If you include plots/images, be sure to include all the required files. Alternatively, you can export it as a PDF and it will be self-sufficient.
- [ ] Kaggle submission and writeup (details below)
- [ ] Github repo with all of your code! You need to either fork it or just copy the code over to your repo. A simple way of doing this is provided below. Include the link to your repo below. If you would like to make the repo private, please dm us and we'll send you the GitHub usernames to add as collaborators.

`YOUR GITHUB REPO HERE (or notice that you DMed us to share a private repo)`

## To move to your own repo:

First follow `README.md` to clone the code. Additionally, create an empty repo on GitHub that you want to use for your code. Then run the following commands:

```bash
$ git remote rename origin staff # staff is now the provided repo
$ git remote add origin <your repos remote url>
$ git push -u origin main
```



# Part -1: PyTorch review

Feel free to ask your NMEP friends if you don't know!

## -1.0 What is the difference between `torch.nn.Module` and `torch.nn.functional`?

`torch.nn.Module and torch.nn.functional are both methods to implement a neural network. torch.nn.Module (module) allows for more customizability in terms of managing state and parameters whereas torch.nn.functional (functional) doesn't. Additionally, module is a more object oriented approach requiring an __init__ method where layers are defined and forward method which specifies how layers and input data interact. Furthermore, functional has a lot of prewritten functions (activation functions, etc).`

## -1.1 What is the difference between a Dataset and a DataLoader?

`Datasets are ways to create datasets in Pytorch to access individual points. Dataloaders wrap datasets to allow for batching, loading, and shuffling the data. Dataloaders help train models on data.`

## -1.2 What does `@torch.no_grad()` above a function header do?

`@torch.no_grad() prevents a function from tracking gradient computations. This reduces space and time complexity as now you aren't storing or using gradients.`



# Part 0: Understanding the codebase

Read through `README.md` and follow the steps to understand how the repo is structured.

## 0.0 What are the `build.py` files? Why do we have them?**
The build.py files (models/build.py and data/build.py) instantiate models and data loaders based on the specific configurations provided. These exist to abstract the process of creating instances of models/data loaders, so that specific implementation details aren’t needed. 

## 0.1 Where would you define a new model?

You'd define a new model in models, by creating a new .py file.  

## 0.2 How would you add support for a new dataset? What files would you need to change?

To add support for a new dataset, you’d need to modify the data/build.py and data/datasets files. In data/build.py, you’d need to edit the build_loader function by creating an additional if statement (ex. if config.DATA.DATASET == ‘new_dataset’). Within that if statement, set the dataset_train, dataset_test, and dataset_val variables appropriately. You’d also need to create a new class for the dataset in data/dataset.py. 

## 0.3 Where is the actual training code?

The training code is located in main.py.

## 0.4 Create a diagram explaining the structure of `main.py` and the entire code repo.
![Image Alt text](/images/<img width="789" alt="IMG_0133" src="https://github.com/nemerjit/nmep-hw2/assets/110420942/ab4d5fff-45ff-4904-a66b-5986c6416794">)




# Part 1: Datasets

The following questions relate to `data/build.py` and `data/datasets.py`.

## 1.0 Builder/General

### 1.0.0 What does `build_loader` do?

`build_loader creates datasets and then dataloaders for either the CIFAR 10 or imagenet datasets.`

### 1.0.1 What functions do you need to implement for a PyTorch Datset? (hint there are 3)

`__init__, __len__, __get_item__ are the functions that you need to implement for a Pytorch Dataset.`

## 1.1 CIFAR10Dataset

### 1.1.0 Go through the constructor. What field actually contains the data? Do we need to download it ahead of time?

`self.dataset contains the data. We don't need to download it ahead of time (download=true; if not downloaded, download it now).`

### 1.1.1 What is `self.train`? What is `self.transform`?

`self.train is tells us whether the dataset is meant for training or not training. self.transform seems to be transforming/pre-processing the data to our liking for our purposes (color jitter, etc).`

### 1.1.2 What does `__getitem__` do? What is `index`?

`index is a specific datapoint in the dataset. __getitem__ obtains a datapoint from the dataset, obtains the transformation of the image at that index, and returns that image and its label (the entire datapoint with the transformed image).`

### 1.1.3 What does `__len__` do?

`__len__ returns the number of datapoints within the dataset.`

### 1.1.4 What does `self._get_transforms` do? Why is there an if statement?

`self.__get_transforms transforms/pre-processes the data. There is an if statement because the pre-processing done on the images should be different for data that is to be trained on and data that the model is validated on.`

### 1.1.5 What does `transforms.Normalize` do? What do the parameters mean? (hint: take a look here: https://pytorch.org/vision/main/generated/torchvision.transforms.Normalize.html)

`transforms.Normalize normalizes the image among several channels. In this case, we have normalized the means across the RGB channels. This can be used to transform the color of the images.`

## 1.2 MediumImagenetHDF5Dataset

### 1.2.0 Go through the constructor. What field actually contains the data? Where is the data actually stored on honeydew? What other files are stored in that folder on honeydew? How large are they?

`The self.file field contains the data. I think this data is stored in the root directory of honeydew in the data folder. Other files stored in the folder are other datasets used by project teams, etc. Files in the folder are massive. Most are 100K+ and the largest are just over 1M.`

> *Some background*: HDF5 is a file format that stores data in a hierarchical structure. It is similar to a python dictionary. The files are binary and are generally really efficient to use. Additionally, `h5py.File()` does not actually read the entire file contents into memory. Instead, it only reads the data when you access it (as in `__getitem__`). You can learn more about [hdf5 here](https://portal.hdfgroup.org/display/HDF5/HDF5) and [h5py here](https://www.h5py.org/).

### 1.2.1 How is `_get_transforms` different from the one in CIFAR10Dataset?

`__get_transforms is different in this function as this dataset doesn't include colorjitter and random horizontal fit, normalize includes normalization in the standard deviation of values, and if the dataset is used for training, we add on additional pre-processing steps rather than having a different set of transformations.`

### 1.2.2 How is `__getitem__` different from the one in CIFAR10Dataset? How many data splits do we have now? Is it different from CIFAR10? Do we have labels/annotations for the test set?

`__getitem__ modifies the label based on whether the data is the test split or not. If it is in the test split, the label is overwritten and replaced with -1 while if it is not in the test split, the original value is retained in the label value.`

### 1.2.3 Visualizing the dataset

Visualize ~10 or so examples from the dataset. There's many ways to do it - you can make a separate little script that loads the datasets and displays some images, or you can update the existing code to display the images where it's already easy to load them. In either case, you can use use `matplotlib` or `PIL` or `opencv` to display/save the images. Alternatively you can also use `torchvision.utils.make_grid` to display multiple images at once and use `torchvision.utils.save_image` to save the images to disk.

Be sure to also get the class names. You might notice that we don't have them loaded anywhere in the repo - feel free to fix it or just hack it together for now, the class names are in a file in the same folder as the hdf5 dataset.

`YOUR ANSWER HERE`


# Part 2: Models

The following questions relate to `models/build.py` and `models/models.py`.

## What models are implemented for you?

The LeNet and ResNet18 models have been implemented for us. 

## What do PyTorch models inherit from? What functions do we need to implement for a PyTorch Model? (hint there are 2)

PyTorch models inherit from the torch.nn.Module class. The functions needed to implement PyTorch models are: __init__() and forward(). 

## How many layers does our implementation of LeNet have? How many parameters does it have? (hint: to count the number of parameters, you might want to run the code)
The implementation of LeNet has 11 layers (6 feature extracting layers, and 5 classifier layers). 



# Part 3: Training

The following questions relate to `main.py`, and the configs in `configs/`.

## 3.0 What configs have we provided for you? What models and datasets do they train on?
The configs file contains the configurations for the models lenet and resnet18. The lenet model trains on the cifar10 dataset, and the resnet model trains on the cifar10 dataset,and the medium_imagenet dataset. 


## 3.1 Open `main.py` and go through `main()`. In bullet points, explain what the function does.

- Sets the device to either CUDA GPU or CPU, CPU if cuda isn’t available.  
- Initializes a model with the specific configurations given 
- Transfers model to specific device for computation 
- Sums the number of parameters and flop counts
- Initializes an optimizer, criterion, and learning rate scheduler 
- Loads the saved model parameters, optimizer state, learning rate scheduler state
- If the model is in evaluation mode, it will return and not engage in training tasks 
- In each training loop, the model is trained for one epoch on the training data, and the training
-  accuracy and loss is computed/reported. The training progress is logged. 
- Checkpoints are periodically saved during the process of training. 
- Model’s performance is then evaluated on the validation dataset, and validation accuracy is computed and validation loss is reported. 


## 3.2 Go through `validate()` and `evaluate()`. What do they do? How are they different? 
> Could we have done better by reusing code? Yes. Yes we could have but we didn't... sorry...

The validate() function evaluates the performance of the model (loss, accuracy) based on its performance with a validation dataset during training. The evaluate() function measures the performance of the model on new data, after the training period is complete. 


# Part 4: AlexNet

## Implement AlexNet. Feel free to use the provided LeNet as a template. For convenience, here are the parameters for AlexNet:

```
Input NxNx3 # For CIFAR 10, you can set img_size to 70
Conv 11x11, 64 filters, stride 4, padding 2
MaxPool 3x3, stride 2
Conv 5x5, 192 filters, padding 2
MaxPool 3x3, stride 2
Conv 3x3, 384 filters, padding 1
Conv 3x3, 256 filters, padding 1
Conv 3x3, 256 filters, padding 1
MaxPool 3x3, stride 2
nn.AdaptiveAvgPool2d((6, 6)) # https://pytorch.org/docs/stable/generated/torch.nn.AdaptiveAvgPool2d.html
flatten into a vector of length x # what is x?
Dropout 0.5
Linear with 4096 output units
Dropout 0.5
Linear with 4096 output units
Linear with num_classes output units
```

> ReLU activation after every Conv and Linear layer. DO **NOT** Forget to add activatioons after every layer. Do not apply activation after the last layer.

## 4.1 How many parameters does AlexNet have? How does it compare to LeNet? With the same batch size, how much memory do LeNet and AlexNet take up while training? 
> (hint: use `gpuststat`)

AlexNet has 57.04481 million parameters, and the LeNet has around 99.28k paramters. With a batch size of 256, the alexnet will take up around 3099 MB of memory, while the LeNet will take up around 338 MB of memory. The LeNet is much more memory-efficient. 

## 4.2 Train AlexNet on CIFAR10. What accuracy do you get?

Report training and validation accuracy on AlexNet and LeNet. Report hyperparameters for both models (learning rate, batch size, optimizer, etc.). We get ~77% validation with AlexNet.

> You can just copy the config file, don't need to write it all out again.
> Also no need to tune the models much, you'll do it in the next part.

The max accuracy was about 77.48% validation with AlexNet. 
Hyperparamters: 
AUG:

  COLOR_JITTER: 0.4
  
DATA:

  BATCH_SIZE: 256
  
  DATASET: "cifar10"
  
  IMG_SIZE: 70
  
  NUM_WORKERS: 32
  
  PIN_MEMORY: True
  
MODEL:
  NAME: alexnet
  
  NUM_CLASSES: 10
  
  DROP_RATE: 0.5
  
TRAIN:

  EPOCHS: 50
  
  WARMUP_EPOCHS: 10
  
  LR: 3e-4
  
  MIN_LR: 3e-5
  
  WARMUP_LR: 3e-5
  
  LR_SCHEDULER:
  
    NAME: "cosine"
    
  OPTIMIZER:
  
    NAME: "adamw"
    
    EPS: 1e-8
    
    BETAS: (0.9, 0.999)
    
    MOMENTUM: 0.9
    
OUTPUT: "output/alexnet_base.yaml"

SAVE_FREQ: 1

PRINT_FREQ: 500

SAVE_FREQ: 5

PRINT_FREQ: 99999
--------------------------------------
LeNet was straight doo doo. Max Accuracy of 40%.

AUG:
  COLOR_JITTER: 0.4
  RAND_AUGMENT: rand-m9-mstd0.5-inc1
BASE:
- ''
DATA:
  BATCH_SIZE: 256
  DATASET: cifar10
  DATA_PATH: ' '
  IMG_SIZE: 32
  INTERPOLATION: bicubic
  NUM_WORKERS: 32
  PIN_MEMORY: true
EVAL_MODE: false
MODEL:
  DROP_RATE: 0.0
  NAME: lenet
  NUM_CLASSES: 200
  RESNET: {}
  RESUME: ''
OUTPUT: output/lenet
PRINT_FREQ: 99999
SAVE_FREQ: 5
SEED: 0
TEST:
  CROP: true
  SEQUENTIAL: false
  SHUFFLE: false
TRAIN:
  ACCUMULATION_STEPS: 1
  EPOCHS: 50
  LR: 0.0003
  LR_SCHEDULER:
    NAME: cosine
  MIN_LR: 3.0e-05
  OPTIMIZER:
    BETAS:
    - 0.9
    - 0.999
    EPS: 1.0e-08
    MOMENTUM: 0.9
    NAME: adamw
  START_EPOCH: 0
  WARMUP_EPOCHS: 10
  WARMUP_LR: 3.0e-05





# Part 5: Weights and Biases

> Parts 5 and 6 are independent. Feel free to attempt them in any order you want.

> Background on W&B. W&B is a tool for tracking experiments. You can set up experiments and track metrics, hyperparameters, and even images. It's really neat and we highly recommend it. You can learn more about it [here](https://wandb.ai/site).
> 
> For this HW you have to use W&B. The next couple parts should be fairly easy if you setup logging for configs (hyperparameters) and for loss/accuracy. For a quick tutorial on how to use it, check out [this quickstart](https://docs.wandb.ai/quickstart). We will also cover it at HW party at some point this week if you need help.

## 5.0 Setup plotting for training and validation accuracy and loss curves. Plot a point every epoch.

`PUSH YOUR CODE TO YOUR OWN GITHUB :)`

## 5.1 Plot the training and validation accuracy and loss curves for AlexNet and LeNet. Attach the plot and any observations you have below.

`Below is Lenet's validation and training accuracy. Despite going for more epochs, its worse than Alexnet. Additionally, it's training accuracy is unuasually low and its validation accuracy looks quite similar to its training accuracy.`

<img width="1424" alt="Screen Shot 2024-04-09 at 4 38 25 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/5e279a26-4c55-424e-a15a-fb758c63d265">

Below is AlexNet's training and validation accuracy.

<img width="1409" alt="Screen Shot 2024-04-09 at 4 40 24 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/24506cbb-4142-4a0c-b465-0299659e89b1">

AlexNet has a higher training and validation accuracy with far lower epochs. It looks like the validation and training accuracy is increasing at the end, meaning that if we had more epochs, the training and validation accuracy would likely increase. 


## 5.2 For just AlexNet, vary the learning rate by factors of 3ish or 10 (ie if it's 3e-4 also try 1e-4, 1e-3, 3e-3, etc) and plot all the loss plots on the same graph. What do you observe? What is the best learning rate? Try at least 4 different learning rates.

<img width="1414" alt="Screen Shot 2024-04-09 at 4 44 41 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/ad78e960-e77d-4bdf-9ed4-8600084d061c">

<img width="1422" alt="Screen Shot 2024-04-09 at 4 45 13 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/723cb140-331d-4cfa-8ded-555e58f6761f">

<img width="1434" alt="Screen Shot 2024-04-09 at 4 45 36 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/8a3ce5ab-8054-4523-950c-06ee219c98f0">

These three graphs alongside the one for alexNet above are the three graphs I used to figure out the best learning rate. The first learning rate of 3e-4 performed the best with 1e-4 being a close second in terms of validation loss. I would say, based on the results 3e-4 is the best learning rate.

## 5.3 Do the same with batch size, keeping learning rate and everything else fixed. Ideally the batch size should be a power of 2, but try some odd batch sizes as well. What do you observe? Record training times and loss/accuracy plots for each batch size (should be easy with W&B). Try at least 4 different batch sizes.

`YOUR ANSWER HERE`

<img width="1429" alt="Screen Shot 2024-04-09 at 4 48 35 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/4ca93d37-619a-4874-a5b6-83d09033253e"> - 128

<img width="1413" alt="Screen Shot 2024-04-09 at 4 48 59 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/d5d8699d-8062-4cae-afc1-85cccab80866"> - 512

<img width="1410" alt="Screen Shot 2024-04-09 at 4 49 28 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/1abecbc6-536f-4ba1-916e-dd0976631636"> - 256

<img width="1409" alt="Screen Shot 2024-04-09 at 4 50 04 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/85635ae8-7f1f-4736-92fb-ef77a8c2853e"> - 1024

1024 batch size was clearly the slowest. The amount of time each epoch took had a one-to-one correlation with the size of the batch. The larger the size of the batch, the longer the epoch would take. Oddly enough, validation accuracy was pretty similar for 256, 512, and 1024. The rest were somewhat different in this regard.


## 5.4 As a followup to the previous question, we're going to explore the effect of batch size on _throughput_, which is the number of images/sec that our model can process. You can find this by taking the batch size and dividing by the time per epoch. Plot the throughput for batch sizes of powers of 2, i.e. 1, 2, 4, ..., until you reach CUDA OOM. What is the largest batch size you can support? What trends do you observe, and why might this be the case?
You only need to observe the training for ~ 5 epochs to average out the noise in training times; don't train to completion for this question! We're only asking about the time taken. If you're curious for a more in-depth explanation, feel free to read [this intro](https://horace.io/brrr_intro.html). 

`36384 was the batch size where Cuda OOM occurred. The largest batch size that worked was 18192. As batch size increases, throughput increases, which could be because we are using more memory, slowing down the process. One trend that is noticable is as the batch size increases, time per epoch increases as well. Throughput also increases by orders of magnitude. While the time the epoch takes increases steadily as the batch size increase, there are points at which the throughput becomes ten times the previous throughput. Even at the end, just doubling the batch size 1.5x'd the throughput.`

## 5.5 Try different data augmentations. Take a look [here](https://pytorch.org/vision/stable/transforms.html) for torchvision augmentations. Try at least 2 new augmentation schemes. Record loss/accuracy curves and best accuracies on validation/train set.

<img width="1442" alt="Screen Shot 2024-04-09 at 4 57 46 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/64f2b4ae-0d44-4b03-9cca-e4764b6874dd">

`2 new augmentation schemes: vertical flip and random resized crop. The best accuracy is 55%.`

## 5.6 (optional) Play around with more hyperparameters. I recommend playing around with the optimizer (Adam, SGD, RMSProp, etc), learning rate scheduler (constant, StepLR, ReduceLROnPlateau, etc), weight decay, dropout, activation functions (ReLU, Leaky ReLU, GELU, Swish, etc), etc.

`YOUR ANSWER HERE`

I'm good chief.



# Part 6: ResNet

## 6.0 Implement and train ResNet18

In `models/*`, we provided some skelly/guiding comments to implement ResNet. Implement it and train it on CIFAR10. Report training and validation curves, hyperparameters, best validation accuracy, and training time as compared to AlexNet. 

`YOUR ANSWER HERE`
Resnet Config: 
AUG:
  COLOR_JITTER: 0.4
DATA:
  BATCH_SIZE: 1024
  DATASET: "cifar10"
  IMG_SIZE: 32
MODEL:
  NAME: resnet18
  NUM_CLASSES: 10
  DROP_RATE: 0.0
TRAIN:
  EPOCHS: 10
  WARMUP_EPOCHS: 0
  LR: 3e-4
  MIN_LR: 3e-5
  WARMUP_LR: 3e-5
  LR_SCHEDULER:
    NAME: "cosine"
  OPTIMIZER:
    NAME: "adamw"
    EPS: 1e-8
    BETAS: (0.9, 0.999)
    MOMENTUM: 0.9
OUTPUT: "output/resnet18_cifar"
SAVE_FREQ: 50 
PRINT_FREQ: 500

<img width="1438" alt="Screen Shot 2024-04-09 at 4 59 09 AM" src="https://github.com/nemerjit/nmep-hw2/assets/110488494/21dc933a-82e8-4650-933b-0806da9c9fc0">

The training time appears to be similar to alexnet if not slightly less for this batch size. Resnet actually has a worse overall max accuracy score when accounting for epochs but overall has a better ending max accuracy score.




## 6.1 Visualize examples

Visualize a couple of the predictions on the validation set (20 or so). Be sure to include the ground truth label and the predicted label. You can use `wandb.log()` to log images or also just save them to disc any way you think is easy.

`YOUR ANSWER HERE`


# Part 7: Kaggle submission

To make this more fun, we have scraped an entire new dataset for you! 🎉

We called it MediumImageNet. It contains 1.5M training images, and 190k images for validation and test each. There are 200 classes distributed approximately evenly. The images are available in 224x224 and 96x96 in hdf5 files. The test set labels are not provided :). 

The dataset is downloaded onto honeydew at `/data/medium-imagenet`. Feel free to play around with the files and learn more about the dataset.

For the kaggle competition, you need to train on the 1.5M training images and submit predictions on the 190k test images. You may validate on the validation set but you may not use is as a training set to get better accuracy (aka don't backprop on it). The test set labels are not provided. You can submit up to 10 times a day (hint: that's a lot). The competition ends on __TBD__.

Your Kaggle scores should approximately match your validation scores. If they do not, something is wrong.

(Soon) when you run the training script, it will output a file called `submission.csv`. This is the file you need to submit to Kaggle. You're required to submit at least once. 

## Kaggle writeup

We don't expect anything fancy here. Just a brief summary of what you did, what worked, what didn't, and what you learned. If you want to include any plots, feel free to do so. That's brownie points. Feel free to write it below or attach it in a separate file.

**REQUIREMENT**: Everyone in your group must be able to explain what you did! Even if one person carries (I know, it happens) everyone must still be able to explain what's going on!

Now go play with the models and have some competitive fun! 🎉
