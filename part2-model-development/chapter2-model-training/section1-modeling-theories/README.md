# Modeling Theories

Since deep learning approaches took over ML in practice, this section is also mostly about deep learning architecture components and related metrics.

1. [Activation Functions](#activation-functions) : anti choices and preferred choices
1. [Convolutional Neural Network](#convolutional-neural-network) : what convolution means and famous architectures
1. [Recurrent Neural network](#recurrent-neural-network) : vanishing gradient problem and recurrent cell
1. [Evaluation Metrics](#evaluation-metrics) : widely used metrics for classification and regression

## Activation Functions

Function used to calculate final input of next layer.

* anti-choices : just remind why following functions should not be utilized
    * **linear** : dummy function which makes neural network a linear function of input
    * **step function** : not differentiable, so gradient cannot be calculated
    * **sigmoid, logistic** : gradient is nearly zero in most domain values(vanishing gradient)
    * **tanh** : requires relatively expensive computation compared to upcoming ReLU variants(but, used in RNN)
* preffered choices : try ReLU -> Leaky ReLU -> PReLU, ELU, SiLU for performance optimization
    * **ReLU** : easy computation, but linear in negative domain(dying ReLU problem)
    * **Leaky ReLU** : introduce small negative slope in negative domain
    * **PReLU**(Parametric ReLU) : Leaky ReLU whose slope is also learnable parameter
    * **ELU**(Exponential Linear Unit) : linear on positive domain, exponential function on negative domain
    * **SiLU**(Sigmoid Linear Unit) : f(x) = x * sigmoid(x), a.k.a. swish function

## Convolutional Neural Network

* *Feature location invariant* network used to find things in data that is might not be in exactly where expected to be. 
* It scans the data and find the pattern of interest; word 'Convolution' is a fancy term for a process of breaking a data into little chunks, processing them individually and assembling into a bigger picture.
* This network recognizes edges on the image to notice specific object by repeating Convolution, Pooling, Dropout, Flatten, Dense operations in a model-specific order.

### some famous architectures

* **LeNet-5** : got famous through MNIST handwriting recognition challenge
* **AlexNet** : got famous through ImageNet image classification challenge
* **GoogLeNet** : introduces *inception modules*(which is basically groups of convolution layers)
* **ResNet** : deeper architecture achieved by *skip-connection*

## Recurrent Neural Network

* Operation(or, cell) to project current portion of data into hidden state vector using previous hidden state vector is repeated.
* Recent behavior tends to have more influence on current time step.
* vanishing gradient : occurs when sequence gets longer(even happens with deep CNNs), so RNNs from LSTM adopted *memory cell* to propagate message to next step.

### some famous RNN cells

* **LSTM Cell** : maintains separate ideas of short-term and log-term states
* **GRU Cell** : simplified LSTM cell that performs as well

## Evaluation Metrics

Essential for monitoring the training process and setting early stopping criteria

* classification
    * accuracy = (TP + TF) / all
    * precision = TP / (TP + FP)
    * recall = TP / (TP + FN)
    * F1 score = 2 * TP / (2 * TP + FP + FN)
    * AUC = area under the ROC curve(x = FP rate, y = TP rate in various threshold level)
    * PR curve(curve of recall(y) by precision(x); area under the curve is used for metric)
* regression
    * RMSE = root mean squared error(average of square root of (y-yhat)^2)
    * MAE = mean absolute error(average of |y-yhat|)
