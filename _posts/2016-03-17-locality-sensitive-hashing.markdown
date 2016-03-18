---
layout: post
title:  "Locality sensitive hashing"
date:   2016-03-17 01:00:00
categories: machinelearning
---

One of our projects is a startup which provides paywalled access to scientific and academic 
papers uploaded by the users. 

Given a large number of daily uploads, we need to automatically reject the documents which 
are excessively similar to those we already have - but at the same time if the similarity 
isn't too high we want to be able to recommend the document to users who previously purchased 
or previewed similar papers.

This post is a brief outline of the similarity search algorithm we implemented. For full 
implementation please check out [github](https://github.com/andreister/NAlgo/blob/master/NAlgo/Text/LocalitySensitiveSearch.cs)

#### Jaccard similarity

Standard way for finding similar documents is Jaccard similarity, which treats a document 
as a bag of words (or, better yet, as a bag of k-shingles - word sequences of length k). 
If shingles in one document make set `A` and those in another one make set `B` then Jaccard 
similarity of the two documents is

$$JS(d_{1}, d_{2}) = \frac{A \cap B}{A \cup B}$$ 

This approach won't scale if the number of documents count is high, because the algorithm needs 
to compare each document to all others so complexity grows as $$O(n^{2})$$. In this case 
we resort to an estimation method - minhashing.

#### Minhashing

The idea is that instead of comparing shingles across the documents directly, we generate a bunch 
of random hash functions and then for each function calculate hashes for all shingles. 

But then for each function we keep only the minimum value out of all shingle hashes (hence the name 
of the method, minhashing). After the process is complete, we get _document signature_ - a vector 
of minhash values.

C# code below illustrates the idea:

{% highlight csharp %}
public uint[] GetSignature(string text, List<Func<Shingle, uint>> hashFunctions)
{
    var result = new uint[hashFunctions.Count];

    var shingles = GetShingles(text);
    foreach (var shingle in shingles) {
        var minHash = uint.MaxValue;
        for (var functionId = 0; functionId < hashFunctions.Count; functionId++) {
            var hash = hashFunctions[functionId](shingle);
            minHash = Math.Min(hash, minHash);
            result[functionId] = minHash;
        }
    }
    return result;
}
{% endhighlight %}

#### Locality sensitive hashing

Once we have the signatures, we can compare them in $$\binom{n}{2}$$ fashion:

{% highlight csharp %}
public IEnumerable<DocumentSignature> GetSimilarDocuments(IEnumerable<DocumentSignature> signatures, decimal threshold)
{
    foreach (var signature in signatures) {
        var equal = 0.0m;
        var total = _minhashVector.Length;
        for (var i = 0; i < _minhashVector.Length; i++) {
            if (_minhashVector[i] == signature._minhashVector[i]) {
                equal++;
            }
        }
        signature.Similarity = equal / total;
    }
    return signatures.OrderBy(x => x.Similarity).Where(x => x.Similarity >= threshold);
}
{% endhighlight %}



