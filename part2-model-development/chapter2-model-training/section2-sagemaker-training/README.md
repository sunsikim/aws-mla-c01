# Model Training on SageMaker

This section covers list of service user can use with different complexity level.

1. [SageMaker Canvas](#sagemaker-canvas)
1. [SageMaker Autopilot](#sagemaker-autopilot)
1. [SageMaker Jumpstart](#sagemaker-jumpstart)
1. [SageMaker Training Job](#sagemaker-training-job)
    1. [SageMaker Model Registry](#sagemaker-model-registry)
    1. [Automatic Model Tuning](#automatic-model-tuningamt)
    1. [Monitoring](#monitoring)
    1. [Distributed Training](#distributed-training)

## SageMaker Canvas

No-code interface for building ML models targeting non-technical users including business analysts to return prediction value.

* **highest simplicity** : user can train a model only by drag-and-drop
* **lowest flexibility** : no room for customization or parameter tuning(once result returned, that's it)
* **supported tasks** : regression, single/multi class classification on tabular data, forecasting and text, image classification

## SageMaker Autopilot

This is AutoML tool which figures out the best model that most generalizes data by itself. It is applicable when 

1. problem to solve is either classification or regression
1. data is in tabular format(therefore, it is stored in S3 as CSV or Parquet format)
1. model choice can be restricted to Linear Learner, XGBoost, Deep Learning, Ensemble.

Then, given single target value, it finds best model and hyperparameter set and returns:

1. model notebook : used for further model refination by data scientists or ML engineer
1. model leaderboard : to provide information on how the model is selected compared to other alternatives

This solution is integrated with *SageMaker Clarify* which provides feature importance report using SHAP value.

## SageMaker Jumpstart

Next simplest way to train a model is to use notebook environment. Using *SageMaker Studio*, user can select instance type, install everything inside the notebook(safe to say that this is Google colab equivalent of AWS). Combined with *SageMaker Jumpstart*, which provide suite of pretrained models for various task including text (encoding/generation/summarization) and image (encoding/segmentation), user can either use the pretrained model as is or tune the model seamlessly.

## SageMaker Training Job

Lowest level for model training is to

1. configure running environment using Docker image
1. let SageMaker run the code within configured type of instance by creating Training job. 

Since this provides most flexible way to train a model, there are various options to consider.

### SageMaker Model Registry

Used to generate catalog of models and manage their versions(i.e. associate metadata with models). This is especially useful for workflow to centralize the model management workplace and share, deploy(with CI/CD) models easily. Can be integrated with MLFlow.

* ex) trained model -> model registry -> event bridge -> lambda function(email notification to administrator) -> model approved -> API gateway -> lambda function(update parameter store) -> inference pipeline enabled with the new model

### Automatic Model Tuning(AMT)

Given list of hyperparameters and grid of its possible values, this searches the best combination using one of following approaches:

* **grid search** : limited to categorical parameters(brute forces every possible combination; worst solution)
* **random search** : among possible choices, select random parameter set
* **bayesian optimization** : regression problem on metric to optimize(learns as it goes, so parallel learning is disabled)
* **hyperband** : appropriate for algorithms that learns iteratively along number of epochs(i.e most DL models). faster than BO or RS

Few tips to keep in mind:

* Don't optimize too many hypermarameters at once(magnitude of trial to expolit grows exponentially)
* Don't run too many tuning jobs concurrently, because learning happens sequentially.
* Limit the possible value ranges to as small(or, realistic) as possible to avoid unnecessary trial

### Monitoring

* **health checking** : enabled by default when using `ml.p` or `ml.g` instance types to ensure NCCL works properly in every instance within cluster
* **Sagemaker Debugger** : `smdebug` library provides integrated APIs to define conditions to make alarm via CloudWatch
* **Tensorboard Integration** : requires some modification within training script as [written](https://docs.aws.amazon.com/sagemaker/latest/dg/tensorboard-on-sagemaker.html)

### Distributed Training

Note: before moving out to creating cluster of multiple instances, always max out number of gpus in single instance first to prevent overhead from inter-instance communication

* job parallelism : running multiple jobs in parallel
* data parallelism : to distribute training data into multiple instances within cluster
    * SageMaker Distributed Data Parallelism library implements inter-instance communication
    * set backend of torch.distributed.init_process_group as 'smddp'
    * however, working with other distributed training libraries are just fine(ex. pytorch ddp, torchrun, deepspeed etc)
* model parallelism : when model doesn't fit into single instance so has to be loaded on aggregated resources
    * Sagemaker MPP library for Pytorch provides functionalities including
        * optimization state sharding : weights, gradients anything that stateful optimizer(ex. Adam) holds are sharded
        * activation checkpointing : trick to reduce memory usage by clearning recorded activation value of some layers, and recomputing the during a backward process
        * activation offloading : swaps checkpointed activations in a microbatch to/from the cpu
    * for more information, see [documents](https://sagemaker.readthedocs.io/en/stable/api/training/sdp_versions/latest/smd_data_parallel_pytorch.html)

### Other extra keywords 

* warm pool : maintain training resources to reduce provisioning time
    * can be turned on when defining sagemaker training job(configure *KeepAlivePeriodInSeconds*)
    * requires service limit increase request in advance -> then use persistent cache to store data across training jobs in combination
* Elastic Fabric Adapter(EFA) : network device attached to the sagemaker GPU instances by making better use of the bandwidth    