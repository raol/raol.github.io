---
layout: post
title: "Poor man language identification."
description: ""
category: 
tags: ["programming", "nlp", "nltk" ]
---
{% include JB/setup %}

Language identification, as it's easy to guess, is the task of identifying the language
of a document. For instance search engines may store the language of the indexed document
and provide option such as _Search for English results only_ as Google does. But in order 
to store the language, engine should determine it first.

<!-- more -->

There are bunch of methods for language detection, but the easiest one is based on stopwords.
I.e. the most common words in a language that are usually filtered out before document processing.

So all we need to do is to tokenize our document (split by words), build set of words and find
intersection with the stopwords collection for particular language. The biggest intersection wins.

Here [NLTK](http://www.nltk.org/) package will be useful. Let's examine it first.

```python
nltk.corpus.stopwords.fileids()

#[u'danish', u'dutch', u'english', u'finnish', u'french', u'german', u'hungarian'
#, u'italian', u'norwegian', u'portuguese', u'russian', u'spanish', u'swedish', 
#u'turkish']

nltk.corpus.stopwords.words('english')

#[u'i', u'me', u'my', u'myself', u'we', u'our', u'ours', u'ourselves', u'you' ...]

```

Below we iterate over the languages and compare words from the document
with stopwords defined for the language.

```python

import nltk

phrase = '''
Here at Google Research we have been using word n-gram models for a variety of R&D projects, 
such as statistical machine translation, speech recognition, spelling correction, entity 
detection, information extraction, and others. While such models have usually been 
estimated from training corpora containing at most a few billion words, we have been 
harnessing the vast power of Google's datacenters and distributed processing infrastructure 
to process larger and larger training corpora. We found that there's no data like more data, 
and scaled up the size of our data by one order of magnitude, and then another, and then one 
more - resulting in a training corpus of one trillion words from public Web pages.
'''

words = set([v for v in nltk.wordpunct_tokenize(phrase)])
langs = {}
for f in nltk.corpus.stopwords.fileids():
    lsw = set(nltk.corpus.stopwords.words(f))
    langs[f] = len(lsw.intersection(words))

print 'Language of the document is:', max(langs, key=langs.get)

```

Though this method is clear and relatively fast, it won't work well on short documents
like tweets, simply because there are not as many stopwords, and often they are skipped
or replaced with abbreviations like `you -> u`. For such cases N-gram method will be the 
best choice, and I'll try to write about it next time.