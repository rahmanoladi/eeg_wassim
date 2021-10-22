# Introduction
This repo contains a Jupyter notebook for classifying the publicly available [Epileptic Patients' Dataset](https://data.mendeley.com/datasets/5pc2j46cbc/1), which was donated to the Mendeley datasets repository by American University in Beirut's researcher, [Professor Nasreddine Wassim](https://www.emedevents.com/speaker-profile/wassim-nasreddine). The techniques used in the Jupyter notebook successfully achieved a **perfect classification accuracy of 100%** on the training portion of the dataset, while achieving a classification accuracy of **95%** on the test portion.The dataset has four classes:
1. Class **0** which denotes normal, **non-epileptic**, EEG signals; there are 3,895 samples in this class.
2. Class **1** which denotes EEG signals for **Complex Partial Seizures**; there are 3,034 samples in this class.
3. Class **2** which denotes EEG signals for **electrographic seizures**; there are 705 samples in this class. 
4. Class **3** which denotes **video-detected seizures** with no visual change over EEG; there are 111 samples in this class.

Further, each EEG signal in the dataset has 19 channels, and lasts a duration of one second. Because the signals were sampled at 500Hz, each sample in the dataset has 500 entries per channel. This is why the training data is stored in a 3-d numpy array named **x_train[sample_index, channel_index, time_index]**.For more info about the dataset, please read the accompanying documentation [here](https://data.mendeley.com/public-files/datasets/5pc2j46cbc/files/6f59035d-7d61-40cf-8491-a58cc77d7818/file_downloaded).

# Techniques
My pipeline involves two main stages: feature extraction and classification. In the feature extraction stage, for each of the 19 channels, I first compute the power spectral densities (PSD) of the input EEG signal, using the Welch function available in the scipy module. Then, for each channel, I divide the resulting frequency spectrum into eleven frquency bands, and use Simpson's formula to compute the area under the implicit PSD graph; these areas give the band powers associated with the pertinent frequency bands. This results in a 19 by 11 matrix (i.e. 19 channels and 11 bands) of band powers. Next, I concatenate the rows of this matrix to obtain a 209-dimensional feature vector which I pass into the classifier in the next stage of the pipeline. In the next stage of the pipeline, after some experimentation, I found that what works best is a random forest classifier. I harness the in-built implementation provided by Sklearn, and found that this achieves an impeccable, perfect classification accuracy of 100% on the training dataset, while also yielding a 95% classification accuracy on the test dataset.   

# Environment
The Jupyter notebook has been tested in a Conda environment with the following modules:
- Python 3.6.13
- Scikit-learn 0.24.2
