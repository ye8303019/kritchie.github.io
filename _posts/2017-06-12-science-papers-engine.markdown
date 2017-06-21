---
layout: post
title:  "Science papers retrieval engine: Part 1"
date:   2017-06-13 13:38:00 +0800
categories: jekyll update
---


As a software engineer, I’m always trying to find the most efficient way to do something. I often think that if something can be automated, then it should be automated. Following that, I started thinking about something that I could build for myself that would automate some of the tasks I perform daily. This would also be a great way to learn something new and get my hands dirty.

A few weeks ago, I discovered that the public scientific papers database [ArXiv](https://arxiv.org) had a very simple API from which it was possible to retrieve information. Since I often find myself looking for new papers on ArXiv about computer science, I thought it would be a good idea to build something on top of it that would make my search easier and faster.

I decided to start building a paper retrieval engine using basic natural language processing features. The goal was to make it accessible via an API that would be providing features like similarity search, for instance. I would then be able to directly look for papers about a specific subject directly on my API.

### Data Collection

The first thing I did was to draw a simple diagram that would present an overview of the data collection subsystem. Before anything, I had to figure out how to get data into the system. 

The architecture simply follows the Extract-Transform-Load (ETL) pattern. The way it works is kind of like any manufacturing process. You gather primary resource, you transform it and finally you store it somewhere for later delivery. That’s exactly the goal here. To gather data from the ArXiv API, apply some transformation on it and store the results somewhere for fast retrieval. 

Here’s the diagram that presents the high-level view of that kind of system.

<img src="{{ site.github.url }}/assets/arxiv_data_collection_overview.png"/>

# Implementation

I decided to use Luigi pipeline for this project. I like the way it chains tasks and using a central scheduler makes it easy to automate tasks on a remote server.The extraction process is done through a very simple method that performs an HTTP call to the ArXiv API. 

{% highlight python %}

query = 'http://export.arxiv.org/api/query?search_query=lastUpdatedDate:' \
        '[{from_date:%Y%m%d}1159+TO+{to_date:%Y%m%d}1159]&max_results={batch_size}&' \
        'sortBy=submittedDate&sortOrder=descending&start={offset}'

{% endhighlight %}

You've probably notice that there are parameters in the query. This is useful to query specific dates in case of data loss or simply for debugging purposes. 

Another detail is that I'm retrieving all of yesterday's papers from the API instead of today's ones. This is simply because I wanted to be able to set a daily cron that would get all the papers for one specific day. If you don't wait after midnight to query the papers, you will miss some of them. 

{% highlight python %}

to_date = luigi.DateParameter(default=(datetime.date.today() - datetime.timedelta(days=1)))
from_date = luigi.DateParameter(default=(datetime.date.today() - datetime.timedelta(days=2)))

{% endhighlight %}

In combination with this, I also decided to query in batch. I thought that this was a more elegant way to query the API, because I can set a maximum limit of papers to retrieve. This is useful in the case where there would be a very large number of papers to retrieve for a given day. I don't necessarily want to collect all of them in one single call to the API.

Here's the complete code of the Luigi task that is going to ran daily.

{% highlight python %}

class PaperFetcher(luigi.Task):
    """ Query the ArXiv API and save response to file

    As mentioned by the documentation from ArXiv, using feedparser to parse the response into a Python object
    """
    to_date = luigi.DateParameter(default=(datetime.date.today() - datetime.timedelta(days=1)))
    from_date = luigi.DateParameter(default=(datetime.date.today() - datetime.timedelta(days=2)))
    batch_size = luigi.IntParameter(default=100)
    max_documents = 5000

    def __init__(self):
        super(PaperFetcher, self).__init__()

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
        while entries and len(entries) > 0 and count < self.max_documents:
            with self.output().open('w') as output:
                for entry in entries:
                    output.write(str.encode(json.dumps(entry) + '\n'))
            count += len(entries)
            entries = self.query(offset=count)
        return

{% endhighlight %}

### Conclusion

So this is it for part 1. This is a very simple data collection process. It will enable me to get some papers inserted daily into my data set without having to worry about it.

The next part will cover the transformation phase to create the TfIdf model based on the summaries of the papers extracted daily. 
