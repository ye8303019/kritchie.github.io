---
layout: post
title:  "Science papers retrieval engine: Part 2"
date:   2017-06-26 20:15:00 +0800
categories: jekyll update
---

In my previous blog post I presented a small ArXiv science paper retrieval project. The goal was to automate the process of fetching those daily papers from the ArXiv public API. I briefly explained the concept of Extract-Transform-Load (ETL) and how to query the API using Python. To continue where I left off, I’ll describe my implementation of the other part of the system. The transformation part of the daily incoming data. 

## Data transformation

The process of data transformation is simply the fact of applying operations on the raw data before storing it. It can be anything from cleaning, formatting or aggregating your data with other data sources. 

In our case, since the goal is to build a basic NLP pipeline, our main pre-processing will be to separate sentences into words. For those who are not familiar, NLP stands for <b>Natural Language Processing</b> which is a specialty of the Linguistic field. To give a better idea, an NLP pipeline would be a series of small functions or algorithms that would be executed on some raw text, with the goal of extracting meaningful information. 

<img src="{{ site.github.url }}/assets/nlp_pipeline.png"/>
<br/>
<i>(Source: [Introduction to NLP - Part 1: Overview](http://mattfortier.me/2017/01/31/nlp-intro-pt-1-overview/). If you want to know more about building an NLP pipeline, I suggest that you go take a look at the excellent [blog article](http://mattfortier.me/2017/01/31/nlp-intro-pt-1-overview/) about NLP pipelines. <b>As a disclaimer</b>: I personally know the author, but the blog post is still awesome and very well written.)</i>
<br/><br/>

So let's get back to our first pre-processing task. Why would we want to separate sentences into words? Well, imagine you want to create a sports’ news website and you want to break the articles into categories. What you could do, is to count the number of times the word “<i>hockey</i>” appears in a given article. After processing each articles, you would sort all of them by the highest occurrences of that word to build a list. You would then present this list sorted by occurrences of the word “<i>hockey</i>” to one of your users so he/she could read about his/her favorite sport. (<b>Interesting fact</b>: News articles about Hockey rarely mention the word "hockey" itself.)

<br/>
<img src="{{ site.github.url }}/assets/hockey_news.png" border="1"/>
<br/><br/>
<i>(Source: [TheHockeyNews](http://www.thehockeynews.com/news/article/get-ready-for-a-lot-of-mistakes-to-be-made-on-july-1))</i> 
<br/><br/>

Of course, the reality is much more complex than that, but in the end it’s all about trying to extract some meaning or information from text. 

## Pre-Processing

In my case, the only important pre-processing step was to tokenize the data. Like mentioned earlier, this is very useful for a lot of NLP tasks because words are the primary building blocks when it's question of extracting information from a text. In order to do that, you must find an algorithm that will split any sentence into words. Here’s an example : 

{% highlight text  %}
# Receive an input sentence.
“The cat is black.”

# Lowercase every character of the sentence.
“the cat is black.”

# Remove punctuation marks.
“the cat is black”

# Tokenize to extract the words.
[“the”, “cat”, “is”, “black”]
{% endhighlight  %}

<i>(Obviously you should use code to do that, but that was to illustrate the idea.)</i>
<br><br/>

So you can observe that we correctly transformed a sentence into an array of words. 
This may seem simple at first but tokenization is a complex problem. The thing is that if your tokenizer is not good enough, the rest of your pipeline will suffer from it and your results will be bad as well. "<i>Garbage in, garbage out</i>".

To illustrate how hard this task can be, try to tokenize a similar sentence, but in Simplified Chinese :

<br/>
"<i>我真的很喜欢猫尤其是黑猫</i>"

(There are no white spaces, so where would you split?…)
<br/><br/>

Chinese tokenization is extremely complicated. A lot of research is going into improving the current state-of-the-art for Chinese tokenizers. You can find a lot of information on the Internet about that subject.

In my case, to make things simple, I used the tokenizer from Python's Natural Language Toolkit ([NLTK](http://www.nltk.org/)). The only thing you have to do is to send a string as input and the NLTK tokenizer will take care of the rest for you. You will then be able to use the output to build your dictionary. 

{% highlight python  %} 
tokens = nltk.word_tokenize("The cat is black")

>>> ['The', 'cat', 'is', 'black'] 
{% endhighlight  %}

<br/>

## Building the dictionary

So first of all, why would we want a dictionary ? The answer is because we need to have a count of the words that appear in our corpora (our collection of documents) to calculate relevancy metrics. To give you a better example, let’s say you have this piece of text you want to analyze : 

<br/>
“<i>I have described a method of, and apparatus for, transmitting two or more telegraphic signals simultaneously along a single wire by the employment of transmitting-instruments [...]</i>”

(Source : [Alexander Graham Bell’s Telephone Patent](http://patft.uspto.gov/netacgi/nph-Parser?Sect2=PTO1&Sect2=HITOFF&p=1&u=/netahtml/PTO/search-bool.html&r=1&f=G&l=50&d=PALL&RefSrch=yes&Query=PN/174465))

<br/>
In that context, how can you tell which words are important and which are not ? For a human this is an easy task, but not necessarily for a computer. One thing that you might try as a human is that you would probably search through the text for all the words that you find strange or uncommon. This would give you a clue to identify what the idea of this text might be. 

This is exactly the same thing that we will teach our algorithm to do. We will build a data set of all the words that we’ve seen so far and we will keep the occurrences within this data set. We will then be able to find the occurrence count within all the documents for any specific word. Using that count, we will be able to score those words in terms of “<i>uncommonness</i>”. 

In general (and when you have sufficient data), the more a word in uncommon, the more we will tend to view it as more meaningful. That’s the idea behind the [TF-IDF](www.tfidf.com) score. Let's say I want to calculate the TF-IDF score of the words in a sentence that I pick randomly in a book : 

<br/>
“<i>The cat is black<i>” 

(“<i>the</i>” and “<i>is</i>” would probably have higher occurrences than “<i>cat</i>” or “<i>black</i>”, meaning that the important words of the sentence are more likely to be “<i>cat</i>” and/or “<i>black</i>” )
<br/><br/>

## How to do that using Python ?  

Python is a fantastic tool that can do all sorts of things. Because of that, a lot of people have built incredible tools and libraries. One of them is called [Gensim](www.gensim.com). Gensim includes lots and lots of features that are used to do topic modeling. That’s the library that I chose to build the dictionary because it's very simple and yet very powerful. You have the ability to load/save/update a model, which is what I needed for my pipeline. It's also widely used and well maintained/documented. 

{% highlight python  %}

from gensim.corpora.dictionary import Dictionary

# Creating a dictionary
dictionary = Dictionary()

# Loading the dictionary
dictionary = Dictionary.load('/path/of/the/dictionary/on/disk')

# Updating the dictionary
documents = ... # Array of array of words (i.e. the sentences tokenized)
                # [['the', 'cat'], ['is', 'black']]

dictionary.add_documents(documents) 

# Saving the dictionary
dictionary.save('/path/of/the/dictionary/on/disk')

{% endhighlight  %} 

(Easy as 1,2,3...)
<br/><br/>

## Putting it all together

Putting it all in one piece, what we have is a small pipeline able to perform two tasks. The first task being data collection (extraction) and the other one data processing (transformation). 

<img src="{{ site.github.url }}/assets/paper_retrieval_pipeline_overview.png"/>

Here’s the full code including both operations.

{% highlight python  %}

# -*- coding: utf-8 -*-
from __future__ import (absolute_import, division, print_function, unicode_literals)

import logging
import feedparser
import luigi
import datetime
import json
import nltk

from gensim.corpora.dictionary import Dictionary
from luigi.format import GzipFormat

logger_name = 'paper.pipeline'
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(logger_name)


class PaperFetcher(luigi.Task):
    """ Query the ArXiv API and save response to file

    As mentioned by the documentation from ArXiv, using feedparser to parse the response into a Python object
    """
    to_date = luigi.DateParameter()
    from_date = luigi.DateParameter()
    batch_size = luigi.IntParameter(default=100)

    def requires(self):
        return

    def output(self):
        return luigi.LocalTarget('../../data/raw/{date:%Y%m%d}.gz'
                                 .format(date=self.to_date),
                                 format=GzipFormat())

    def query(self, offset):
        query = 'http://export.arxiv.org/api/query?search_query=lastUpdatedDate:' \
                '[{from_date:%Y%m%d}1159+TO+{to_date:%Y%m%d}1159]&max_results={batch_size}&' \
                'sortBy=submittedDate&sortOrder=descending&start={offset}'\
            .format(batch_size=self.batch_size,
                    from_date=self.from_date,
                    to_date=self.to_date,
                    offset=offset)

        return feedparser.parse(query).get('entries', None)

    def run(self):
        """ Cycle query to query by batch until there is no more paper received from the ArXiv API.

        """
        count = 0
        entries = self.query(offset=count)

        with self.output().open('w') as output:
            while entries and len(entries) > 0:
                for entry in entries:
                    output.write(str.encode(json.dumps(entry) + '\n'))
                count += len(entries)
                entries = self.query(offset=count)

        logger.info('Loaded {docs} documents into the dataset for [{to_date}]'
                    .format(docs=count, to_date=str(self.to_date)))
        return


class DictionaryFetcher(luigi.Task):

    def output(self):
        return luigi.LocalTarget('../../data/models/dictionary.model')

    def run(self):
        dictionary = Dictionary()
        dictionary.save(self.output().path)


class DictionaryBuilder(luigi.Task):
    """ Builds a dictionary with paper summaries receive from ArXiv API

    """
    to_date = luigi.DateParameter(default=(datetime.date.today() - datetime.timedelta(days=1)))
    from_date = luigi.DateParameter(default=(datetime.date.today() - datetime.timedelta(days=365)))

    def requires(self):
        return [PaperFetcher(to_date=self.to_date, from_date=self.from_date), DictionaryFetcher()]

    def output(self):
        return luigi.LocalTarget('../../data/processed/{date:%Y%m%d}'
                                 .format(date=self.to_date),
                                 format=GzipFormat())

    def run(self):
        """ Loop through documents from a file to build dictionary

        """
        dictionary = Dictionary.load(self.input()[1].path)
        documents = []

        with self.output().open('w') as f_out:
            with self.input()[0].open('r') as f_in:
                for line in f_in:
                    tokens = nltk.word_tokenize(json.loads(line.decode('utf-8'))['summary'])
                    documents.append(tokens)
                    f_out.write(str.encode(json.dumps(tokens) + '\n'))

                dictionary.add_documents(documents)

        dictionary.save(self.input()[1].path)
        return

if __name__ == '__main__':
    luigi.run(main_task_cls=DictionaryBuilder, local_scheduler=True,
              cmdline_args=[])


{% endhighlight %}

I hope you enjoyed this article. Suggestions are always very welcome !

Cheers 
