---
layout: post
keywords: Nature Language Processing (NLP)
description: 学习《Nature Language Processing with Python》，记录重点信息
title: 《Nature Language Processing with Python》学习笔记
categories: [text-summarization]
tags: [nlp]
group: archive
icon: list
---
{% include mathsyouth/setup %}


### Categorizing and Tagging Words

Words can be grouped into classes, such as nouns, verbs, adjectives, and
adverbs. These classes are known as *lexical categories* or *parts-of-speech*.
The process of classifying words into their parts-of-speech and labeling them accordingly is known as *part-of-speech tagging*, *POS tagging*, or simply *tagging*. The collection of tags used for a particular task is known as a *tagset*.

A *part-of-speech tagger*, or *POS tagger*, processes a sequence of words, and attaches a part of speech tag to each word.

```python
import nltk

text = nltk.word_tokenize("And now for something completely different")
print nltk.pos_tag(text)
print nltk.help.upenn_tagset('RB')
```

The tag of a word depends on the word and its context within a sentence. Unigram taggers are based on a simple statistical algorithm: for each token, assign the tag that is most likely for that particular token. For example, it will assign the tag *JJ* to any occurrence of the word **frequent**, since **frequent** is used as an adjective (e.g., a frequent word) more often than it is used as a verb (e.g., I frequent this cafe). In the following code sample, we train a unigram tagger, use it to tag a sentence, and then evaluate:

```python
from nltk.corpus import brown

brown_tagged_sents = brown.tagged_sents(categories='news')
brown_sents = brown.sents(categories='news')
# We train a UnigramTagger by specifying tagged sentence data as a parameter
# when we initialize the tagger.
unigram_tagger = nltk.UnigramTagger(brown_tagged_sents)
print unigram_tagger.tag(brown_sents[2007])
print unigram_tagger.evaluate(brown_tagged_sents)
```

Now that we are training a tagger on some data, we must be careful not to test
it on the same data, as we did in the previous example. Instead, we should split the data:

```python
size = int(len(brown_tagged_sents) * 0.9)
train_sents = brown_tagged_sents[:size]
test_sents = brown_tagged_sents[size:]
unigram_tagger = nltk.UnigramTagger(train_sents)
print unigram_tagger.evaluate(test_sents)
```

An n-gram tagger is a generalization of a unigram tagger whose context is the
current word together with the part-of-speech tags of the n-1 preceding tokens.
Here we see a special case of an n-gram tagger, namely a bigram tagger. First
we train it, then use it to tag untagged sentences:

```python
bigram_tagger = nltk.BigramTagger(train_sents)
print bigram_tagger.tag(brown_sents[2007])
unseen_sent = brown_sents[4203]
print bigram_tagger.tag(unseen_sent)
```

Notice that the bigram tagger manages to tag every word in a sentence it saw
during training, but does badly on an unseen sentence. As soon as it encounters
a new word (i.e., 13.5), it is unable to assign a tag. It cannot tag the
following word (i.e., million), even if it was seen during training, simply
because it never saw it during training with a *None* tag on the previous word.
Consequently, the tagger fails to tag the rest of the sentence. Its overall accuracy score is very low:

```
bigram_tagger.evaluate(test_sents)
```

Most NLTK taggers permit a backoff tagger to be specified. The backoff tagger
may itself have a backoff tagger:

```python
t0 = nltk.DefaultTagger('NN')
t1 = nltk.UnigramTagger(train_sents, backoff=t0)
t2 = nltk.BigramTagger(train_sents, backoff=t1)
print t2.evaluate(test_sents)
```

### Extracting Information from Text

The basic technique we will use for entity recognition is chunking, which 
segments and labels multitoken sequences. We will begin by considering the 
task of *noun phrase chunking*, or *NP-chunking*, where we search for chunks 
corresponding to individual noun phrases. *NP-chunks* are defined so as not 
to contain other *NP-chunks*. One of the most useful sources of information 
for NP-chunking is part-of-speech tags. In order to create an NP-chunker, we 
will first define a chunk grammar, consisting of rules that indicate how 
sentences should be chunked. In this case, we will define a simple grammar
with a single regular expression rule. This rule says that an NP chunk should 
be formed whenever the chunker finds an optional determiner (DT) followed by 
any number of adjectives (JJ) and then a noun (NN). Using this grammar, we 
create a chunk parser, and test it on our example sentence：

```python
sentence = [("the", "DT"), ("little", "JJ"), ("yellow", "JJ"),
            ("dog", "NN"), ("barked", "VBD"), ("at", "IN"), ("the", "DT"), ("cat", "NN")]
grammar = "NP: {<DT>?<JJ>*<NN>}"
cp = nltk.RegexpParser(grammar)
result = cp.parse(sentence)
print result
result.draw()
```

The rules that make up a chunk grammar use *tag patterns* to describe 
sequences of tagged words. A tag pattern is a sequence of part-of-speech tags 
delimited using angle brackets, e.g., `<DT>?<JJ>*<NN>`. Sometimes it is 
easier to define what we want to exclude from a chunk. We can define a *chink* 
to be a sequence of tokens that is not included in a chunk. Entity recognition 
is often performed using chunkers, which segment multitoken sequences, and 
label them with the appropriate entity type. Common entity types include 
ORGANIZATION, PERSON, LOCATION, DATE, TIME, MONEY, and GPE (geo-political 
entity). 


### 参考文献
1. Steven Bird, Ewan Klein, and Edward Loper. [Nature Language Processing with Python](http://www.nltk.org/book/) – Analyzing Text with the Natural Language Toolkit.
1. http://www.nltk.org/
