# Restaurant Recommendation System Based on Yelp with NLP
## Charlene Chen, Aaron Yuen, Alexander Herring

This project aims to primarily use language-based features such as review text to understand the user?s
preference and then generate personalized restaurant recommendations to specific users. In this project, we train a recurrent neural network (RNN) with LSTM units to generate a recommendation system of ratings for Yelp restaurants. We also explore an experimental topic modeling approach using collaborative filtering with LDA topic modeling for generating recommendations of rankings without using neural networks or word embeddings.

The primary RNN with LSTM units model dramatically outperformed our Gaussian Naive Bayes
baseline in accuracy, F1 score and correlation, while the LDA model only slightly outperformed the Gaussian Naive Bayes baseline using the same metrics. The aspect of RNNs being able to determine relationships between words helped with the performance, but through error analysis we found that the model did not perform well in some review text where there were polarizing sentiments. Additionally, the topic pool in the LDA model contained some meaningless topics that led to degraded performance in generating useful user-topic distributions for nearest neighbor search.

![](https://github.com/CongyingChen/Data-Science-Portfolio/blob/master/Natural_Language_Processing_with_Deep_Learning/code/image/methods.png)

![](https://github.com/CongyingChen/Data-Science-Portfolio/blob/master/Natural_Language_Processing_with_Deep_Learning/code/image/result.png)

![](https://github.com/CongyingChen/Data-Science-Portfolio/blob/master/Natural_Language_Processing_with_Deep_Learning/code/image/RNN-LSTM.png)

![](https://github.com/CongyingChen/Data-Science-Portfolio/blob/master/Natural_Language_Processing_with_Deep_Learning/code/image/LDA.png)

![](https://github.com/CongyingChen/Data-Science-Portfolio/blob/master/Natural_Language_Processing_with_Deep_Learning/code/image/LDA2.png)