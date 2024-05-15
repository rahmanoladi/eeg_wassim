# Introduction
This repo presents the API of a model I trained and deployed to the AWS cloud, for classifying EEG signals into one of four classes:
1. Class **0** which denotes normal, **non-epileptic**, EEG signals.
2. Class **1** which denotes EEG signals for **Complex Partial Seizures**.
3. Class **2** which denotes EEG signals for **electrographic seizures**. 
4. Class **3** which denotes **video-detected seizures**.

Towards training the model, I employed the publicly available [Epileptic EEG Dataset](https://data.mendeley.com/datasets/5pc2j46cbc/1), which was donated to the Mendeley datasets repository by American University in Beirut's researcher, [Professor Nasreddine Wassim](https://www.emedevents.com/speaker-profile/wassim-nasreddine). The trained model successfully achieved a **perfect classification accuracy of 100%** on the training portion of the dataset, while achieving a classification accuracy of **95%** on the test portion.

# Brief Description of Training Techniques
Towards training the model, I employed the publicly available [Epileptic EEG Dataset](https://data.mendeley.com/datasets/5pc2j46cbc/1).
Each EEG signal in the dataset has 19 channels, and lasts a duration of one second. Because the signals were sampled at 500Hz, each sample in the dataset has 500 entries per channel. This is why the training data is stored in a 3-d numpy array named **x_train[sample_index, channel_index, time_index]**.For more info about the dataset, please read the accompanying documentation [here](https://data.mendeley.com/public-files/datasets/5pc2j46cbc/files/6f59035d-7d61-40cf-8491-a58cc77d7818/file_downloaded).

My training pipeline involves two main stages: feature extraction and classification. In the feature extraction stage, for each of the 19 channels in the data, I first compute the power spectral densities (PSD) of the input EEG signal, using the Welch function available in the scipy module. Then, for each channel, I divide the pertinent PSD graph into eleven frquency bands, and use Simpson's formula to compute the area under the  graph; these areas give the band powers associated with the frequency bands. This results in a 19 by 11 matrix of band powers (i.e. 19 channels and 11 bands). Next, I concatenate the rows of this matrix to obtain a 209-dimensional feature vector which I then pass into the classifier in the next stage of the pipeline. In the next stage of the pipeline, after some experimentation, I found that what works best is a random forest classifier. I harness the in-built implementation of the random forest classifier provided by Scikit-learn, and found that this achieves an **impeccable, perfect classification accuracy of 100%** on the training dataset, while also yielding a 95% classification accuracy on the test dataset. To reproduce my results, please run the **eeg_band_power_random_forest.ipynb** Python notebook in the codes folder of this github. The notebook has been tested in a with the following installed:
- Python 3.6.13
- scikit-learn 0.24.2
- scipy 1.5.4


