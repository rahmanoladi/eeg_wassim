# Introduction
This repo contains a Jupyter notebook for classifying the publicly available [Epileptic Patients' Dataset](https://data.mendeley.com/datasets/5pc2j46cbc/1), which was donated to the Mendeley datasets repository by American University in Beirut's researcher, [Professor Nasreddine Wassim](https://www.emedevents.com/speaker-profile/wassim-nasreddine). The techniques used in the Jupyter notebook successfully achieved a **perfect classification accuracy of 100%** on the training segment of the dataset, while achieving a classification accuracy of **95%** on the test segment.The dataset has four classes:
1. Class **0** which denotes normal, **non-epileptic**, EEG signals; there are 3,895 samples in this class.
2. Class **1** which denotes EEG signals for **Complex Partial Seizures**; there are 3,034 samples in this class
3. Class **2** which denotes EEG signals for **electrographic seizures**; there are 705 samples in this class 
4. Class **3** which denotes **video-detected seizures** with no visual change over EEG; there are 111 samples in this class.

Further, each EEG signal in the dataset has 19 channels, and lasts a duration of one second. The signals were sampled at 500Hz, and so each sample in the dataset has 500 entries per channel. This is why the training data is stored in a 3-d numpy array named x_train[sample_index, channel_index, time_index].For more info about the dataset, please read the accompanying documentation [here](https://data.mendeley.com/datasets/5pc2j46cbc/1).

# Techniques
My pipeline involves two main stages: feature extraction and classification.

