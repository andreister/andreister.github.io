---
layout: post
title:  "Locality sensitive hashing"
date:   2016-03-17 01:00:00
categories: machinelearning
---

Standard way for finding similar documents is Jaccard similarity, which treats a document 
as a bag of words (or, even better, a bag of k-shingles - word sequences of length k). 
If shingles in one document make set `A` and those in another one make set `B` then Jaccard 
similarity of the two documents is

$$JS(d_{1}, d_{2}) = \frac{A \cap B}{A \cup B}$$ 

Obviously this approach won't work if documents count is high, because the algorithm needs 
to compare each document to all others so complexity grows as $$\binom{n}{2}$$. In this case 
we resort to an estimation method - minhashing.

The idea is that instead of comparing shingles across the documents directly, we generate a bunch 
of random hash functions and then for each function calculate hashes for all shingles. But for 
each function we keep only the minimum value out of all shingle hashes (hence the name of the method,
minhashing). After the process is complete, we get _document signature_ - a vector of minhash values.

Below is C# code illustrating the idea:

{% highlight csharp %}
public uint[] GetSignature(string text)
{
    var result = new uint[_hashFunctions.Count];

    var shingles = GetShingles(text);
    for (var shingleId = 0; shingleId < shingles.Count; shingleId++) {
        var minHash = uint.MaxValue;
        var shingleHash = shingles[shingleId].Hash;
        for (var functionId = 0; functionId < _hashFunctions.Count; functionId++) {
            var randomHash = _hashFunctions[functionId](shingleHash);
            minHash = Math.Min(randomHash, minHash);
            result[functionId] = minHash;
        }
    }
    return result;
}
{% endhighlight %}


