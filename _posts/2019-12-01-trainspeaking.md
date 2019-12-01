---
title: Trainspeaking
layout: post
categories: blog
tags: [trainspotting, ngrams]
---
["Trainspotting" by Irvine Welsh](https://en.wikipedia.org/wiki/Trainspotting_(novel)) is partially written in Scottish. It makes it so hard to read that I decided to build an [ngrams](https://en.wikipedia.org/wiki/N-gram) model and generate text based on it. Just a quick experiment, you can find it [here](/trainspeaking.html).

The model was built using something like this:
```python
#!/usr/bin/env python3

import re
import json

SIZE = 5

def flatten(dct):
    result = {}
    for k, v in dct.items():
        d = result
        for i in k[:-1]:
            d[i] = d.get(i, {})
            d = d[i]
        d[k[-1]] = v
    return result

with open('book.txt', 'rt') as input_file:
    data = input_file.read()
    ngrams = {}
    window = ('.',)
    for word in re.findall(r'[’\w]+|\.|\?|,', data):
        if len(word) > 10 or word == '’':
            continue
        word = word.lower()
        if len(window) == SIZE - 1:
            ngrams[window] = ngrams.get(window, {})
            ngrams[window][word] = ngrams[window].get(word, 0) + 1
            window = window[1:] + (word,)
        else:
            window = window + (word,)

    print(json.dumps(flatten(ngrams)))
```

The generation itself is also very simple:
```javascript
function iter_tree(tree, fun, current) {
    current = current || [];
    for (let [key, value] of Object.entries(tree)) {
        current.push(key);
        if (Number.isInteger(value)) {
            result = fun(current, value);
            if (result !== undefined) {
                return result;
            }
        } else {
            result = iter_tree(value, fun, current);
            if (result !== undefined) {
                return result;
            }
        }
        current.pop();
    }
}

function random_word_from_tree(tree) {
    sum = 0;
    iter_tree(tree, function(path, count) {
        sum += count;
    });
    selected = Math.floor(Math.random() * sum);
    result = iter_tree(tree, function(path, count) {
        selected -= count;
        if (selected < 0) {
            return path[0];
        }
    });
    return result;
}

function random_word(prefix) {
    tree = ngrams;
    prefix.forEach(function(v) {
        tree = tree[v];
    })
    result = random_word_from_tree(tree);
    return result;
}

function generate(size, length) {
    text = ['.'];
    while (text.length <= size - 1) {
        text.push(random_word(text));
    }
    while (text.length <= length || (text[text.length - 1] != '.' && text[text.length - 1] != '?')) {
        text.push(random_word(text.slice(-(size - 1))));
    }
    while (text[0] == '.' || text[0] == '?') {
        text = text.slice(1);   // drop the initial dots
    }
    text = text.join(' ');
    text = text.replace(/ ,/g, ',');  // remove space from before ,
    text = text.replace(/ \./g, '.');  // remove space from before .
    text = text.replace(/ \?/g, '?');  // remove space from before ?
    return text;
}
```
