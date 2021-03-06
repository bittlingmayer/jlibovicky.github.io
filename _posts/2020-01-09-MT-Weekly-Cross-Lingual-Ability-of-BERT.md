---
layout: post
title: "Machine Translation Weekly 24: Cross-Lingual Ability of Multilingual BERT"
tags: [mt-weekly, en]
lang: en
---

After the Christmas holidays, I will once again have a look at multilingual
BERT. I already [discussed multilingual BERT on this blog
once](/2019/10/18/MT-Weekly-Multilingual-BERT.html) when I reviewed a paper
that explored some cross-lingual and multilingual properties of multilingual
BERT. This week's paper does more in-depth experiments and shows that some of
the outcomes of the previous paper were not correct. The title of the paper is
[Cross-lingual Ability of Multilingual BERT: An Empirical
Study](https://arxiv.org/pdf/1912.07840.pdf) was done at the University of
Pennsylvania and will be published at this year's ICLR conference.

Multilingual BERT is a neural network that provides dense numerical sentence
representation for 104 languages. During training, the model learns to guess
what words were left out in the input and if two sentences follow each other in
a coherent text or not. This is done with millions of sentences in 104
languages without telling the model what the language is.

![Training BERT](/assets/bert.svg)

One of the main reasons why we develop such multilingual representations is
that we would like to be able to transfer solutions from one language (for
which we have enough data) into other languages (for which the data do not
exist). For example, imagine you have manually annotated offensive tweets in,
for instance, English and French and you would like to classify the tweets in
other languages as well. With a high-quality multilingual representation,
you could train a model using the English and French data and (given the
multilingual representation is good enough) it would automatically work on
other languages as well (this is sometimes called a zero-shot transfer).
However, we are not there yet.

Multilingual BERT is far from being perfect, but it is actually pretty good at
this (until recently, it was the best thing we have). Quite embarrassingly, we
do not really know how and why it works (and by we I mean the research
community). The [exploratory
paper](https://www.aclweb.org/anthology/P19-1493/) I reviewed in [MT Weekly
15](/2019/10/18/MT-Weekly-Multilingual-BERT.html) probes multilingual BERT
on various tasks such as named entity recognition or cross-lingual sentence
retrieval makes some interesting observations. The results of their
experiments suggest two aspects play an important role:

1. Vocabulary overlap: many sub-words mean the same in many languages (numbers,
   proper names, professional terminology).

2. Structural similarity of languages.

The paper I am going to review today takes a different approach. Instead of
treating multilingual BERT as a black box and doing experiments with it, they
broke it into small pieces. Instead of a single large model for 104 languages,
they trained only bilingual models and explored details of the training process
to find out where the multilinguality comes from. Their language pairs were
English with Spanish, Hindi, and Russian.

The tasks on which they tested the bilingual models are named entity
recognition (highlighting names of people, locations, numbers, etc.) and
natural language inference (telling if a sentence entails from another, or they
contradict each other, or have nothing in common). The task-specific model is
always trained using English and tested on the other language from the pair.

To test the hypothesis about the lexical overlap helping the multilinguality,
they used a fake vocabulary for English, so that it does not share anything
with the other language from the pair, not even numbers or punctuation (they
basically used a “secret alphabet”). The experiments showed that the vocabulary
overlap does not matter at all. It is also obvious from the results that
language similarity indeed matters—everything you learn on  “secret” English
can be applied to real English, but since the similarity is quite hard to
quantify (or even conceptualize), the authors do not say more about it.

If the models do not benefit from sharing subwords, there is no reason not to
try another text segmentations. But surprise: sub-word tokenization still leads
to better results than using both words or characters. Given that typography is
a pure convention, it should not be surprising that neither words nor
characters are the optimal input units for statistical processing.
Nevertheless, it is stunning that subwords are so much better: segmentation
into subwords is based on simple statistical heuristics (characters that appear
most frequently together other get grouped and that is basically it) which is
not in any way optimized towards the neural network that uses them.

There are also other interesting observations. The deeper the network the
better cross-lingual performance you get. Another interesting observation is
that the cross-lingual language inference works better when both sentences
being compared are in one language than when one of the sentences is in English
and the other is in the second language.

The paper sheds some light on what helps the models to be more language
agnostic, but there is still a gap between the in-language and cross-language
performance. The open question is how to make the multilingual models more
language-agnostic which will be in my opinion one of the most important
research questions in natural language processing in 2020.
