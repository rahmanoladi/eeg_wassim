# Introduction
This repo presents the API of a model I trained and deployed to the AWS cloud, for classifying EEG signals into one of four classes:
1. Class **0** which denotes normal, **non-epileptic**, EEG signals.
2. Class **1** which denotes EEG signals for **Complex Partial Seizures**.
3. Class **2** which denotes EEG signals for **electrographic seizures**. 
4. Class **3** which denotes **video-detected seizures**.

Towards training the model, I employed the publicly available [Epileptic EEG Dataset](https://data.mendeley.com/datasets/5pc2j46cbc/1), which was donated to the Mendeley datasets repository by American University in Beirut's researcher, [Professor Nasreddine Wassim](https://www.emedevents.com/speaker-profile/wassim-nasreddine). The trained model successfully achieved a **perfect classification accuracy of 100%** on the training portion of the dataset, while achieving a classification accuracy of **95%** on the test portion.

To quickly test out the model, copy the two lines below (at once, not one after the other) and paste both in a terminal at once:
```
curl -d '{"url":"https://storage.googleapis.com/eeg_test_data/data_slice_50.csv"}' \ 
-X POST https://1xew78khbd.execute-api.us-east-1.amazonaws.com/prod -o "predictions_50.txt"
```
    
     
The above two lines invoke the model's API which lives at https://1xew78khbd.execute-api.us-east-1.amazonaws.com/prod with some test data, containing 50 EEG samples drawn randomly from the test partition of the aforementioned Epileptic EEG Dataset; I uploaded the randomly drawn test set to my Google cloud storage bucket at https://storage.googleapis.com/eeg_test_data/data_slice_50.csv. The output of the API invocation is then stored in a file named "predictions_50.txt" in the current directory of the user's local machine. To compare the model's predictions with the ground truth, I have put the ground truths associated with the 50 EEG random samples in a file at this [URL](https://storage.googleapis.com/eeg_test_data/ground_truth_50.txt). You may download [it](https://storage.googleapis.com/eeg_test_data/ground_truth_50.txt) and compare with the model's prediction available
in the "predictions_50.txt". Upon comparison, you should find that, out of the fifty predictions made by the model, the model gets 48 of them correctly, missing only two of them. Specifically, the model misses only the 35-th and 46-th test samples. 

# How to test the API with your own test data randomly sampled from the Epileptic EEG Dataset
In case you wish to test the model with a test set randomly sampled from the test partition of the Epileptic EEG Dataset, here is how to go about it. As a pre-requisite, you need to have Jupyter Notebook installed on your local machine. In addition, you need to obtain the Python notebook named **eeg_data_prep.ipynb** in the codes folder of this github. Please obtain this Python notebook either by cloning this github, or by directly downloading the notebook file itself. Next, to better understand the workings of the downloaded notebook, please read the comments at the top of the notebook. After that, you may simply run the notebook. The notebook extracts a random subset of data from the test partition of the Epileptic EEG Dataset, and saves the extracted random subset to a file named **data_slice.csv** on the user's local computer. Additionally, the notebook also saves the ground truth labels associated with the extracted random subset to a file named "ground_truth.txt". After running the notebook, the user should upload the output file, **data_slice.csv**, to some storage server on the web, and then make a note of the URL associated with the uploaded file. For illustrational purposes, let's suppose the URL is **https://hypotheticalserver.com/sample_data.csv**, then to invoke the EEG model's API with the uploaded data, you should simply paste the two lines below into the terminal (please copy both lines at once and paste both of them at once into the terminal; do not copy the lines one after the other):

```
curl -d '{"url":"https://hypotheticalserver.com/sample_data.csv"}' \ 
-X POST https://1xew78khbd.execute-api.us-east-1.amazonaws.com/prod -o "sample_predictions.txt"
```
Certainly, it is clear that, before running the lines above, you would need to replace **https://hypotheticalserver.com/sample_data.csv** with the real URL associated with your uploaded data. Upon running the above two lines in the terminal, you the API's output is written to the file **sample_predictions.txt** in the current directory. You can compare the predictions in this file with the labels in the **ground_truth.csv** file, which you got earlier from running the **eeg_data_prep.ipynb** notebook.    


# Brief Description of Training Techniques
Towards training the model, I employed the publicly available [Epileptic EEG Dataset](https://data.mendeley.com/datasets/5pc2j46cbc/1).
Each EEG signal in the dataset has 19 channels, and lasts a duration of one second. Because the signals were sampled at 500Hz, each sample in the dataset has 500 entries per channel. This is why the training data is stored in a 3-d numpy array named **x_train[sample_index, channel_index, time_index]**.For more info about the dataset, please read the accompanying documentation [here](https://data.mendeley.com/public-files/datasets/5pc2j46cbc/files/6f59035d-7d61-40cf-8491-a58cc77d7818/file_downloaded).

My training pipeline involves two main stages: feature extraction and classification. In the feature extraction stage, for each of the 19 channels in the data, I first compute the power spectral densities (PSD) of the input EEG signal, using the Welch function available in the scipy module. Then, for each channel, I divide the resulting frequency spectrum into eleven frquency bands, and use Simpson's formula to compute the area under the implicit PSD graph; these areas give the band powers associated with the pertinent frequency bands. This results in a 19 by 11 matrix of band powers (i.e. 19 channels and 11 bands). Next, I concatenate the rows of this matrix to obtain a 209-dimensional feature vector which I then pass into the classifier in the next stage of the pipeline. In the next stage of the pipeline, after some experimentation, I found that what works best is a random forest classifier. I harness the in-built implementation of the random forest classifier provided by Sklearn, and found that this achieves an **impeccable, perfect classification accuracy of 100%** on the training dataset, while also yielding a 95% classification accuracy on the test dataset. To reproduce my results, please run the **eeg_band_power_random_forest.ipynb** Python notebook in the codes folder of this github. The notebook has been tested in a with the following installed:
- Python 3.6.13
- scikit-learn 0.24.2
- scipy 1.5.4


