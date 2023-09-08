# Installation
- Install conda from: https://www.anaconda.com/ <br>
- Install cuda from: https://developer.nvidia.com/cuda-11-7-0-download-archive <br>
- For Nvidia GPU run the following line on command prompt to check cuda version <br>
```
nvcc -V
```
- Extract all files into a folder <br>
- In the root of the folder run the following line using conda command line<br>
```
conda env create -f environment.yml
```
- Sometimes pytorch does install well through .yaml files if this happens visit PyTorch website to install that, all other libraries can be installed via "pip install {name of library}"
- Activate the environment using the following command: <br>
```
conda activate bigdata_hw1
```
- Run the model.ipynb <br>
- Run all cells and the model should run <br>
- Modify any hyperparams in the hyperparams cell<br>
- Note: Dataset only needs to be downloaded once no need to re-run that cell after the first time<br>
- All results will be pasted into a .csv file after model completion 
# Computer Specs
- GPU: Nvidia RTX 3070 (8GB RAM)
- CPU: AMD Ryzen 5800X 8-Core
- 32 GB DDR4 RAM
# Files
- model.ipynb : File that does everything, creates dataset runs models, and finally pastes results into .csv file (also generates pictures + does Outlier Test Scenario)
- results.csv : File contains data from running the initial model, I misread instructions and ran too many models. I kept this file to help inform observations. These models have a larger spread and can better show trends than the 4 required for this assignment
- results_final.csv : File contains printout of all results generated by the 8 (4+4 for CPU/GPU) models
- README.md : Current file you are reading, contains full report and information about the project
- results.xlsx : Compiles and contains tables used later in this report, data was taken from the raw compiled output in results_file.csv
- environment.yml : Lists all packages + versions used to run this project
- results_k.csv : File contains printout of all results generated by the Outlier Test Scenario (K+1)
# Deliverables:
## 1. Dataset/Code
- See below for Dataset information
- Link to Repo: https://github.com/ShaanGil1/CS6220_HW1/
### Dataset
- The dataset used was CalTech101, it is a simple image dataset with 101 categories and is easy to work with
- Dataset is downloaded through the code in the model.ipynb file, simply run the file and it will download the dataset onto the root of the folder
- The other datasets are created by modifying the original dataset
- Images are resized and train/test sets are recreated depending on the given K value
- Given parameters the subset is created and then trained/tested.  All datasets created are subsets of Caltech101
### Datasets created
- K = 10, Image size = 64x64,   Total # of images: 3,739
- K = 50, Image size = 64x64,   Total # of images: 6,790
- K = 10, Image size = 128x128, Total # of images: 3,739
- K = 50, Image size = 128x128, Total # of images: 6,790
## 2. Screenshots of Environment:
- See Appendix, I didn't understand what was meant by this? But I took a screenshot of my VS Code environment running the code/my environment file (.yml file)
## 3. Input Analysis		
### Table
A) <br>
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/f7053823-1629-40c8-98c9-e7a32460a3d4)
<br>
B) See Appendix for all images <br>
C) Model Used: squeezenet1_1, model is lightweight and accepts different input sizes which is perfect for this given task.
- Hyperparams used:
  - Model Pretrained = True (Used this for faster convergence and therefore lower training time) 
  - Batch Size = 16
  - Loss Function = Cross Entropy Loss
  - Learning Rate = .001
  - Weight Decay = .0001
  - Optimizer = Adam
  - Epcohs = 20
  - LR Scheduler Gamma: .5
  - LR Scheduler Step Size: epochs/3
- The SAME hyperparams were used when training all of the models
- The reasoning for this is to keep the conditions consistent in order not to mess with the experiments. Modification of certain hyperparameters would change the accuracies/runtime and could lead to "parameter hacking" making it harder to draw conclusions
- Some examples of how using different parameters could affect the model would be:
  - Increasing batch size, can decrease runtime and also change accuracy
  - Increasing epochs also directly increases runtime and can also change accuracy.
  - Learning rate can affect accuracy, if it is too high it can lead to a model that doesn't converge and if too small will lead to a model that takes too long to converge.
  - Loss Function usually doesn't have too large of an impact as long as you are using a proper loss function that captures what your model is trying to accomplish
  - LR Scheduler simply reduces learning rate in later epochs to provide a better convergence but can sometimes cause similar problems that LR falls to
  - Weight Decay helps prevent overfitting but if the term is too high it might push the model in the wrong direction
- The parameters used seemed to provide reasonable runtime/accuracy and were maintained to be consistent to make direct comparisons and minimize bias
- This methodology can have its own issues however I think this minimizes bias. It was ensured that the model had enough epochs to be trained until "completion" (plateauing accuracy/loss curve)
D) Training Convergence Condition
- As stated before I looked at a loss/accuracy curve of the training data and found the minimum number of epochs required for all 4 graphs to plateau
- This is to ensure all models are trained to completion while keeping the number of epochs to make runtime comparison unbias (more epochs = longer runtime which is bad when we are trying to find the effects of image size and complexity of dataset/value of K)
- The termination condition used (number of epochs) was 20 epochs
## 4. Output Analysis		
A) Table																				
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/491d15f9-9405-4f0e-aa5d-15bb52d8b378)
<br>
B) Recorded on Table <br>
C) I was a little confused on this part as well so I did it as best I could
- I got especially confused when it said: "(a)	Use this set of unknown samples as test dataset. Report the test accuracy and test time for your pre-trained k-class classifier" - because if the model is not trained on the unknown samples and then it is tested on them it will simply get 0% accuracy. If you train a binary classifier it will either return 0 or 1, if you ask it to identify a 3rd item it will still return 0 or 1 making it wrong, and if the full test set is unknown samples all of them will be wrong leading to a 0% accuracy since it will never return another but 0, or 1
- Given this I did this problem using the following proceedure:
  - Train a classifier that has K categories, record runtime/accuracy
  - Train a new classifier with double the data.  Half of this data will be "unknown" data, provide a new label for this data to distinguish this as a new label
  - Train a new model on this new data, record runtime/accuracy
  - Compare the K and the K + 1 models
- The findings can be found in the below table:
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/8acbc326-8b66-432e-b444-626974bdd141)
- Based on the table we can see runtime increases for K to K + 1, since there are more training samples (as expected)
- Accuracy decreases from K to K + 1, which shows the additional samples are not enough to combat the increase in complexity
- The highest change in test accuracy came from K = 10 and K = 11 which makes it hard to draw any conclusion since this datapoint in the middle of other values
D) Shown on table:
- CPU consistently takes longer than GPU in terms of runtime
- In terms of how long it seems to vary depending on the data set
- The trend shows that the larger the dataset the more time "faster" the GPU is in comparison to the CPU (This is shown on the table as GPU is 13+x faster on the largest dataset and only ~5x on the smallest one).
- This makes sense as GPU has parallelization capability which makes it more suitable for CNN/ML operations, while a CPU cannot do this making it significantly slower
- In terms of accuracy, there is little to no difference.  The differences can simply be attributed to different seeding and minor rounding differences making the small differences negligible
E) Observations:
- CPU always takes longer than GPU to run but accuracy is about the same
- Larger datasets (in terms of MB) take longer to run this includes increasing the number of samples (Increasing K increases the number of samples since we are pulling more images from K categories) or increasing image size
- Train Accuracy is typically higher than Test accuracy which suggests slight overfitting
- Increasing K decreases accuracy, as expected since there are options and more options would make it harder for the model to get the right one (Baseline for K = 10 is 10% assuming expected value when randomly guessing while for K = 50 it is only 2%)
- Increasing image resolution (64x64 vs. 128x128) slightly increases the accuracy of the models.  The difference is greater between accuracies when comparing K=50 and the change is very small when comparing K=10
- These trends can be shown in the table above but for more comparisons, you can view the "results.csv" file which contains 72 models and the parameters used to run them, they continue to validate the observations stated above
## Ways to Improve/Reflection
- Analyze on are larger scale with a larger spread of parameters (more K values, more image resolutions)
- I ended up reading the problem incorrectly and ended up training 72 models using differing parameters (results.csv), putting all that data into tables and then graphing them to show trendlines would help to further support the observation
- When I increased K, I simply added all samples from the dataset with those categories so I modified the number of categories and the value of K, I think for improvement I should do these separately for a more direct comparison (An increase in K should not change the size of the dataset, and an increase in dataset size should not increase K)
- I also used my computer while the models were running which may have slowed down some operations so for consistency, I should have just run this process alone with no interruption
- Add a dynamic stop condition rather than setting it at a specific number of epochs, something like: If the change in train accuracy/loss is less than some value terminate operation
- Use a more complex dataset (accuracies were very high, might want to get lower accuracies to show better comparisons)
- When generating K classes I used the classes with the most images to have more training data this would not be needed in better datasets that have better distribution/more data (instead I could just choose random ones)
# Appendix
## Execution/Environment 
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/354ac79d-b422-4aa8-9752-f3620da31617)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/a7fc6d9d-63a4-4d75-93e3-a9852a09f7b7)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/edb8ba2f-9bdb-45e6-81f4-bd78d7cc3284)
## Images
- Generated using matplotlib, sampled randomly
### Size: 64x64, K: 10
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/c8f26c6a-695c-40fe-a6e9-c789eb8d4aa5)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/848562cd-7697-4efa-a7a3-d5a1d57f0b00)
### Size: 64x64, K: 50
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/04f6ed19-2434-43c4-b444-c2f6ba4a6648)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/15b6e9a3-3949-454d-8940-a1ad9fe968d0)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/198c5ce6-fd12-4859-8060-71a7e84c5fc1)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/85802461-3161-4a0b-9ac7-ddc06b72b6ac)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/2e386857-ff2e-4525-ab45-f4192b627e24)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/9d9d22f9-9fd3-469e-91dd-cb3791deec36)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/fcbbbb22-5b42-469b-9636-8a3bd00bdd50)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/7811d949-6df0-43da-a11f-f4668706c4cb)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/bca56dbc-6f14-4d20-a6a8-095c048b479a)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/351d9920-bb57-4b68-a43d-2f11610b46ff)
### Size: 128x128, K: 10
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/feff6250-084f-4af7-b346-45c4311018f5)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/db5ea5c4-22e4-498f-9524-e406f315e20a)
### Size: 128x128, K: 50
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/d161aa05-d350-42de-a298-10b2471d8227)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/75c87368-c154-450c-b1f6-77ecbec8abd9)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/b1ce3add-4d09-4e60-a4fe-b01434df8305)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/ff0fd2b9-302b-4a2b-9f1e-c87f62cdf541)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/562926d5-9dd7-4b20-90e2-ba294031330a)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/46dd7d0d-94eb-443b-97a6-9f53f01628b1)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/b068cd09-29d9-4038-baf9-0a389fad9a7f)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/ef104a4a-642b-4873-8ccb-515d3249e3a3)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/fb77e732-10d7-4525-bf10-7fa1cf2705b0)
![image](https://github.com/ShaanGil1/CS6220_HW1/assets/71356331/326dc91b-9c11-4bbb-9f58-e06737221551)


















