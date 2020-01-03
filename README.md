## N-gram language models

N-gram language model is basically assuming that each word only depends on the previous n-1 words, so that we can calculate the conditional probability of a word given previous n-1 words and also the probability of a sentence. 

+ load the dataset   
+ counts: `counts[prefix][word]` `total[prefix]`   
+ conditional probability queries: `P(word|prefix)`    
+ sequence probability: sum(log)    
+ generation: categorical sampling  

## Issues: Data Sparsity & Generalization   

The model will assign zero probability to even some reasonable n-grams. So that any sentence containing the n-gram will be given a zero probability regardless of how likely all other n-grams are. 

n-gram language models address data sparsity through **smoothing** techniques which reallocate probability mass, for instance to n-grams with zero counts.

+ a simple and naive method is to **add a fixed pseudo-count** to every n-gram.    

  horrible estimator: how does every unseen n-gram have the same probability?

+ **interpolating** between the estimate of the n-gram probability and the estimate of n-1 probability.   
  n-gram smooth probability is computed recursively by the lower-order n-gram probability. This is a more efficient strategy, considering the falling off to the lower n-grams contains at least some information of the original n-gram, unlike the previous approach of adding a scalar $\alpha$ to every possible n-gram.

+ **KenLM:** estimates n-gram language model using modified Kneser-Ney smoothing, has a fast and memory-efficient implementation. for interpolation, the problem is that if the probability degeneration ngram is high, then the probability of ngram is high, however, this is not exactly true for all situations. For example, the corpus has a high frequency of the phrase "San Franciso", this means that the probability of "Franciso" is high, but it doesn't mean that the probability of "Happy Franciso" is high. But interpolation will predict the probability of "Happy Franciso" is high. 

  In this case, we better use Kneser-Ney smoothing. It will use a parameter to control the probability of degenerated ngram. 

  $P(Franciso|Happy)=\alpha(Franciso|Happy)$, if count("Happy Franciso")>0

  $P(Franciso|Happy)=\lambda(Franciso|Happy)P(Franciso)$, if count("Happy Franciso")=0