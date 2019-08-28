---
layout: post
title: "Tf-Idf in ruby"
---

Tf-Idf is **ranking algorithm** use to rank documents against the keywords given by the user.

It doesn't have an understanding of what is written in the document or what does the words means. It just do some math and determine which document might be relevant to the keywords.  Don't expect some black magic(a.ka. A.I).

To understand Tf-Idf you have to understand two different but related concept first.

- Term Frequency (TF)

- Inverse Document Frequency (IDF)

### #1 Term Frequency

It simiply means how many times a word appears on the given document.

For example if you press Ctrl+F in browser then it compute the times words was present in the document.

![](/assets/img/google-chrome-find.png)
In this document **TermFrequency** for word `ranking` is `3`.

That's it .

Number of times a word occur in a document is called its term frequency.

### #2 Inverse Document Frequency

This will be little hard to understand at first but trust me, it would make sense.

Inverse document frequency is used to calculate how important a word is in the document
For example we took introduction section of [Alan Turing wikipedia page](https://en.wikipedia.org/wiki/Alan_Turing) and try to get top 5 words by term frequency we would something like this.

| Term Frequency | Word   |
| -------------- | ------ |
| 37             | the    |
| 16             | of     |
| 13             | in     |
| 13             | Turing |
| 12             | a      |
