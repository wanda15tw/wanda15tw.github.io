---
layout: post
title:  "Sentiment prediction on text reviews using BERT"
date:   2020-02-21
categories: DataScience NLP BERT
---
* View this markdown in [hack.md](https://hackmd.io/@wG5x2COsRkOpxhsHnBO1_A/HJA4rcyXU).
## Introduction

**BERT** is Google’s neural network-based technique for natural language processing (NLP) pre-training. BERT stands for Bidirectional Encoder Representations from Transformers.

Traditionally, NLP requires many preprocessing techniques and the model performance sacrifies due to the trade off between data size and computation efficiency. Luckily Google open sourced BERT, which has trained on plenty of internet text data, including wikipedia and news using neural networks, and has made itself a generic but powerful pre-trained NLP model.

## BERT setup
Install and import BERT
{%gist wanda15tw/60b1460a85a83993755a4335e53e356a%}

## Data Preparation

### InputExample
BERT expects **four** parameters when constructing **InputExample** in classifier: **guid**, **text_a**, **text_b** and **label**.

```
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



## Reference

* https://github.com/google-research/bert
* https://colab.research.google.com/github/google-research/bert/blob/master/predicting_movie_reviews_with_bert_on_tf_hub.ipynb