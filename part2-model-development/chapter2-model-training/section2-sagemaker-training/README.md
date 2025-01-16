# Model Training on SageMaker

This section covers list of service user can use with different complexity level.

## 1. SageMaker Canvas

No-code interface for building ML models targeting non-technical users including business analysts to return prediction value.

* **highest simplicity** : user can train a model only by drag-and-drop
* **lowest flexibility** : no room for customization or parameter tuning(once result returned, that's it)
* **supported tasks** : regression, single/multi class classification on tabular data, forecasting and text, image classification

## 2. SageMaker Autopilot

This is AutoML tool which figures out the best model that most generalizes data by itself. It is applicable when 

1. problem to solve is either classification or regression
1. data is in tabular format(therefore, it is stored in S3 as CSV or Parquet format)
1. model choice can be restricted to Linear Learner, XGBoost, Deep Learning, Ensemble.

Then, given single target value, it finds best model and hyperparameter set and returns:

1. model notebook : used for further model refination by data scientists or ML engineer
1. model leaderboard : to provide information on how the model is selected compared to other alternatives

This solution is integrated with *SageMaker Clarify* which provides feature importance report using SHAP value.

## 3. SageMaker Jumpstart

Next simplest way to train a model is to use notebook environment. Using *SageMaker Studio*, user can select instance type, install everything inside the notebook(safe to say that this is Google colab equivalent of AWS). Combined with *SageMaker Jumpstart*, which provide suite of pretrained models for various task including text (encoding/generation/summarization) and image (encoding/segmentation), user can either use the pretrained model as is or tune the model seamlessly.

## 4. SageMaker Training Job

Lowest level for model training is to

1. configure running environment using Docker image
1. let SageMaker run the code within configured type of instance by creating Training job. 

Since this provides most flexible way to train a model, there are various options to consider.

### SageMaker Model Registry

Used to generate catalog of models and manage their versions(i.e. associate metadata with models). This is especially useful for workflow to centralize the model management workplace and share, deploy(with CI/CD) models easily.

* ex) trained model -> model registry -> event bridge -> lambda function(email notification to administrator) -> model approved -> API gateway -> lambda function(update parameter store) -> inference pipeline enabled
