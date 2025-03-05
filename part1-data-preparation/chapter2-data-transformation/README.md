# Feature Engineering Techniques

Feature engineering can be defined as process of applying domain knowledge on data by transforming data or adding/deleting/selecting attributes. This process is essential, since it is not wise to pour everything into the piepline and expect somehow good results to come out. As a result, this is an important discriminator of proficient and plain practitioner.

## The curse of dimensionality

Any row of data can be represented as a real vector. As dimension of this vector increases, the vector becomes more flexible and can represent more diverse pattern. However, this implies that the case where ML algorithm has to seek for the pattern would also increase. In such sparse data, most ML algorithm tends to fit suboptimal pattern, therefore it is always wise to boil down the features to the set which matters the most.

## Imputinig missing data

* mean replacement
    * pros : fast and easy(but obvious not the best)
    * cons : distorts the distribution of the column(reduces variance) and disregards the correlation between column
* dropping
    * pros : reasonable when the ratio of missing data is low
    * cons : if 'missing' property contains some meaning, it can cause the loss of information
* Preferred alternatives
    * use ML techniques(ex. KNN, MICE, train model where that column is target)
    * just collect more data

## Dealing with unbalanced data

* threshold modification : reduce false positive and (possibly) increase false negative
* sampling
    * undersampling : dropping majoirity case
    * oversampling : augmenting minority case
* SMOTE(Synthetic Minority Over-sampling TEchnique)
    * yes, it is oversampling technique with machine learning(specifically, KNN)
    * hybrid approach of the two sampling technique above, so more preferred
    * it both generates new samples and undersamples the majority case

## Other Topics

* outlier detection : Defined as data whose pattern deviates from the normal pattern significantly(ex. user that reacts extraordinarily diverse item in collaborative filtering). Use standard deviation or box plot to detect outliers.
* binning : converting numerical data into categorical data representing the interval of that value(effective when there can be innate measurement error; care is required since it drops information)
* transforming : apply functions(ex. square root or logarithm) on numerical data to augment the signal from the feature
* encoding : what deep learing does best
* scaling, normalization, shuffling : you know...