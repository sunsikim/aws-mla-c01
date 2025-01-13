# Modeling Theories

Since deep learning approaches took over ML in practice, this section is also mostly about deep learning architecture components and related metrics.

1. [Activation Functions](#activation-functions)

## Activation Functions

Function used to calculate final input of next layer.

* anti-choices : just remind why following functions should not be utilized
    * **linear** : dummy function which makes neural network a linear function of input
    * **step** function : not differentiable, so gradient cannot be calculated
    * **sigmoid**, logistic : gradient is nearly zero in most domain values(vanishing gradient)
    * **tanh** : requires relatively expensive computation compared to upcoming ReLU variants(but, used in RNN)
* preffered choices : try ReLU -> Leaky ReLU -> PReLU, ELU, SiLU for performance optimization
    * **ReLU** : easy computation, but linear in negative domain(dying ReLU problem)
    * **Leaky ReLU** : introduce small negative slope in negative domain
    * **PReLU**(Parametric ReLU) : Leaky ReLU whose slope is also learnable parameter
    * **ELU**(Exponential Linear Unit) : linear on positive domain, exponential function on negative domain
    * **SiLU**(Sigmoid Linear Unit) : f(x) = x * sigmoid(x), a.k.a. swish function