---
layout: post
title: "interesting use case for recursion"
date: 2012-07-02 21:53
comments: true
categories: 
---

Recursion is the process of defining an infinite set of objects by a single process. In programming an example of this is invoking a function from within itself.

I found an interesting use case for this when I was building my lightweight CSS pre-processor, [bareBones.js](https://github.com/marthakelly/bareBones.js), with JavaScript.

In bareBones I "tokenize" and format my custom CSS syntax into an array of objects representing CSS blocks. Each object could potentially have a children array of identically structured objects (re: data-structure gist).

To account for an infinite number of nested CSS objects I map the blockToCSS function over each object in the data array *and each object in its children array.* (recursion!) 

If the object has no children the function will simply return a string of formatted CSS as a single entry in an array.

If the object has children it will map the same function over each object in the children array. (After that I reduce this array of arrays into an array of strings...a bit unorthodox, yes...:D).

Next, I map over each entry in this new array so I can concatenate the parent selector in front of each child to conserve CSS precedence.

{% gist 3037040 data-structure.js %}

The rest of this function (and the program) involves formatting the CSS which is not particularly interesting for a recursion discussion. Contact me if you want to know more!

Hopefully this gives you more insight into use cases where recursion could be useful, especially with nested data structures!


