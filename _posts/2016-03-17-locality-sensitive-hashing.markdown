---
layout: post
title:  "Locality sensitive hashing"
date:   2016-03-17 01:00:00
categories: machinelearning
---

Standard way for finding similar documents is Jaccard similarity, where we treat a document 
as bag of words. If words from one document go to set `A` and words from another one go to
set `B` then Jaccard similarity of the documents is

$$J(D_{1}, D_{2}) = \frac{\pi^2}{6}$$

Obviously this approach is not feasible if you have hundreds of documents

