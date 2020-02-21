---
layout: post
title:  "Sentiment prediction on text reviews using BERT"
date:   2020-02-21
categories: DataScience NLP BERT
---
* View this markdown in [hack.md](https://hackmd.io/@wG5x2COsRkOpxhsHnBO1_A/HJGGm3TQU).



## Introduction

**BERT** is Google’s neural network-based technique for natural language processing (NLP) pre-training. BERT stands for Bidirectional Encoder Representations from Transformers.

Traditionally, NLP requires many preprocessing techniques and the model performance compromises due to the trade off between data size and computation efficiency. Luckily Google open sourced BERT, which has trained on plenty of internet text data, including wikipedia and news using neural networks, and has made itself a generic but powerful pre-trained NLP model. Utilizing BERT to NLP can effectively save time, computation and boost the model performance even without too many data.

## BERT setup
Install and import BERT
{%gist wanda15tw/60b1460a85a83993755a4335e53e356a%}

## Data Preparation

### InputExample
BERT expects **four** parameters when constructing **InputExample** in classifier: **guid**, **text_a**, **text_b** and **label**.

```python=
class InputExample(object):
  """A single training/test example for simple sequence classification."""

  def __init__(self, guid, text_a, text_b=None, label=None):
    """Constructs a InputExample.
    Args:
      guid: Unique id for the example.
      text_a: string. The untokenized text of the first sequence. For single
        sequence tasks, only this sequence must be specified.
      text_b: (Optional) string. The untokenized text of the second sequence.
        Only must be specified for sequence pair tasks.
      label: (Optional) string. The label of the example. This should be
        specified for train and dev examples, but not for test examples.
    """
    self.guid = guid
    self.text_a = text_a
    self.text_b = text_b
    self.label = label
```

In our simple classifier, we may just define text_a and label as the text field and the sentiment label as below.
{%gist wanda15tw/ce486937736395b6cbc2d8ffa1e80d73%}

### Tokenizer & convert to features
As instructed in Google's [tutorial](https://colab.research.google.com/github/google-research/bert/blob/master/predicting_movie_reviews_with_bert_on_tf_hub.ipynb), we load **tokenizer** from the pretrained [BERT model](https://tfhub.dev/google/bert_uncased_L-12_H-768_A-12/1) from the hub for the first time. Then, tokenize and convert the InputExample to features that BERT understands.
{%gist wanda15tw/d3859be02944d79ad7ad154ebd5fcd1e%}

## Create Model
For me, this is the hardest part to understand, but Google does a great job organizing the code so we can mostly copy and modify code from below. Note that BERT model is loaded again as a layer.
{%gist wanda15tw/a9ecca7e557d21263ed617222c868cf2%}

## Train
Once the **estimator** is in place, we will then build an **input_fn_builder** from the feature, and have the model/estimator to train on it.
{%gist wanda15tw/f83e4747fde36c10705430f02ed6be86%}

## Evalulate
Not the best practice, but straighforward to evaluate the model fittness, I am feeding the same reviews_feature to build another input_fn (but is_training=False this time), and use the trained estimator to evaluate metrics. (shouldn't do this because of overfitting, but still can check if surpass underfitting)
{%gist wanda15tw/09a7c7f9d918da21b955d86dc4ac88ba%}

The performance is astonishingly well.
{'auc': 0.7391304,
 **'eval_accuracy': 0.9877925,
 'f1_score': 0.9937888**,
 'false_negatives': 0.0,
 **'false_positives': 12.0**,
 'global_step': 92,
 'loss': 0.021924153,
 'precision': 0.9876543,
 'recall': 1.0,
 'true_negatives': 11.0,
 'true_positives': 960.0}

The performance at a glance is very good with 98.78% accuracy, but there are more **false positives** than **true negatives** mainly because of the imbalanced data. To improve this, we may resample (oversample minority class or undersample majority class), but this is outside of this post's scope.

## Prediction
If we want to make a new prediction based on a new text (review comment, for example), we can do that, but we need to preprocess the new data the way we did on train/test datasets - first construct to **InputExample**, then tokenized **features** and finally **input_fn** before putting to the model.
{%gist wanda15tw/744dee9ac062d12ac114176b52c8e740%}

[('excellent cache plugin in comparison to others work out of the box my server is openlitespeed with some additional optimization i get an average 3sec page load pagespeed score 92% at gtmetrixcom nice image optimization builtin cloudflare developer mode in one click and more',
  array([-6.444571e+00, -1.590417e-03], dtype=float32),
  'Positive'),
 ("first i want to say normally i don't take the time to do reviews but for this case i thought i must say that this plugin is top class i had w3 cache great rated great quality before installed as a comparison litespeed cache plugin improved my loading speed by x2.5 times better that means a huge difference the test comparison is true plus it has great and other free optionsfeatures that other plugins in his category don't have 5 stars for litespeed cache well deserved",
  array([-4.31978   , -0.01339214], dtype=float32),
  'Positive'),
 ('very satisfied with the performance of plugin and ease of use',
  array([-6.6634393e+00, -1.2775840e-03], dtype=float32),
  'Positive'),
 ('so easy any questions use the plugin support forum answers everything',
  array([-6.5263772e+00, -1.4653194e-03], dtype=float32),
  'Positive'),
 ('better than any plugin out there litespeed is superb',
  array([-6.7109399e+00, -1.2182918e-03], dtype=float32),
  'Positive'),
 ('amazing plugin converting images to webp is simply unique',
  array([-6.2855477e+00, -1.8647201e-03], dtype=float32),
  'Positive')]

## Summary
Okay, now we have gently walked through text classification using BERT. The full notebook and repo are on [github](https://github.com/wanda15tw/LS_review).

## Reference

* https://github.com/google-research/bert
* https://colab.research.google.com/github/google-research/bert/blob/master/predicting_movie_reviews_with_bert_on_tf_hub.ipynb