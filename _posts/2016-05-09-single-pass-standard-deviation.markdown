---
layout: post
title:  "Single pass standard deviation"
date:   2016-05-09 21:50:00
categories: statistics, C#
---

The usual way to calculate standard deviation is two step: one pass through data points to calculate the mean, and then another pass to get the deviations from the mean.

However, in scenarios with obscenely large data sets which you stream from the database, there's a better approach backed up by math.

Let's rewrite the formula for sample standard deviation:

$$SD^{2} = \frac{1}{n-1} \sum_{i=1}^{n}{(x_{i} - \bar{x})^{2}}$$

$$= \frac{1}{n-1} \sum_{i=1}^{n}{(x^{2}_{i} - 2x_{i}\bar{x} + \bar{x}^{2})}$$

$$= \frac{1}{n-1} (\sum_{i=1}^{n}{x^{2}_{i}} - 2\sum_{i=1}^{n}{x_{i}\bar{x}} + \sum_{i=1}^{n}{\bar{x}^{2}})$$

However,

$$= \sum_{i=1}^{n}{x_{i}} = n\bar{x}$$

Therefore

$$SD^{2}= \frac{1}{n-1} (\sum_{i=1}^{n}{x^{2}_{i}} - 2n\bar{x}^{2} + n\bar{x}^{2})$$

$$SD^{2}= \frac{1}{n-1} (\sum_{i=1}^{n}{x^{2}_{i}} - n\bar{x}^{2}$$

$$SD = \sqrt(\frac{1}{n-1} (\sum_{i=1}^{n}{x^{2}_{i}} - n\bar{x}^{2})$$

#### Implementation

(put gist here)

#### Conclusion

...




