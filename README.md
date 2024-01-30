# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary
In this project we use a UCI Bank Marketing dataset to predict if the client will subscribe to a term deposit with the bank or not.
The best performing model was a model generated by AutoML and its Voting Essemble with XGboost classifier which gave an accuracy of 91.67% (0.91669).

## Scikit-learn Pipeline
The following steps were performed in the pipeline:

Create a compute cluster
Provide parameter sampler
Provide early termination policy
Provide a HyperDriveConfig
Submit the HyperDriveConfig to run a train script
Retrieve the best run to select the best hyperparameters
Save best model

The data was accessed from "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv" and a Tabular Dataset was created using TabularDatasetFactory
Data is then cleaned and pre-processed within a clean_data function, the data is split into training and testing in a ratio of 80:20. 

The HyperDriveConfig class was configured with the following parameter sampler:
![parametersample](https://github.com/samfrost23/nd00333_AZMLND_Optimizing_a_Pipeline_in_Azure-Starter_Files/assets/99268262/70de5343-8f0c-4a37-a003-fcfc97a57883)

Regularization Strength (C)
Maximum Number of Iteractions (max_iter) 

Primary_metric_name as Accuracy and primary_metric_goal was set to maximize. 

The Accuracy was calculated on the test set for each run, best model was then retrieved and saved

I chose RandomParameterSampling because it supports early termination of low performance runs which helps to reduce computation time and still allows us to to find reasonably good models, this saves us time and cost for computing resources

I used BanditPolicy for this reason which is an "aggressive" early stopping policy. With BanditPolicy it defines an early termination policy based on a slack factor, slack amount and evaluation interval which is specified as 0.1, 1, 5 respectively:
![policy](https://github.com/samfrost23/nd00333_AZMLND_Optimizing_a_Pipeline_in_Azure-Starter_Files/assets/99268262/795ef812-31a6-46cb-9d15-cb79fbd1c09a)


## AutoML
AutoML gives the ability to run multiple experiments and choose the best classification model.

I defined the following config for the AutoML run:
![automl config](https://github.com/samfrost23/nd00333_AZMLND_Optimizing_a_Pipeline_in_Azure-Starter_Files/assets/99268262/22974abc-1097-49f0-920a-9e22b2b2bf71)


Experiment timeout is set to 30 mins, task type is classification with the primary metric as accuracy.

Once AutoML ran through its experiments the best one proved to be VotingEnsemble model with XGBoost classifier with an accuracy of 91.67%
![automl](https://github.com/samfrost23/nd00333_AZMLND_Optimizing_a_Pipeline_in_Azure-Starter_Files/assets/99268262/994c72a7-34e0-4a54-9511-48d0a249d91d)

## Pipeline comparison
In this project, AutoML had a better accuracy than the HyperDrive model.

HyperDrive Accuracy (90.88%):
![job1](https://github.com/samfrost23/nd00333_AZMLND_Optimizing_a_Pipeline_in_Azure-Starter_Files/assets/99268262/317659b4-6ea6-4ebd-8642-902cc6b04a39)

AutoML Accuracy (91.67%):
![automl2](https://github.com/samfrost23/nd00333_AZMLND_Optimizing_a_Pipeline_in_Azure-Starter_Files/assets/99268262/f5e00c7f-e2ed-4ae5-962e-84946a37762b)

The HyperDrive accuracy was lower than the AutoML's and could be more optimized with adjustments to the Random Parameter Sampling. The SciKit-learn pipeling uses a logistic regression model whereas AutoML benefits from the ability to use multiple models, however at a cost of time in this project with the HyperDrive taking less than 9 mins compared to AutoML's 30 mins.

## Future work
It would be interesting to see how the HyperDrive would perform with adjusted parameter sampling, switching to continuous hyperparameters, adjusting early stopping policy parameters as these could optimise the results. But the same can be said with AutoML, it would be interesting to see what accuracy it can find if we increase the experiment timeout, the increase in timeout would mean that it would have more time and possibly more accuracy that may come with it.
