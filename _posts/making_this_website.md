---
title: Making this Website
date: 2025-01-01 6:38:00 -500
categories: [Programming, Website Development]
tags: [website,development,programming,jekyll,chirpy]
---

# My First Post

guys idk what to write lalalalal

* a

* b

* c

* d

### python is awesome

no one can argue with me on this

```python
print("hello world")
```

nice!

```python
input_words = ["bat", "tab", "tap", "pat", "cat", "act", "rat", "tar", "art"]


def group_anagrams(words):
    anagram_dict = {}

    for word in words:
        sorted_word = ''.join(sorted(word))
        if sorted_word in anagram_dict:
            anagram_dict[sorted_word].append(word)
        else:
            anagram_dict[sorted_word] = [word]

    result = list(anagram_dict.values())
        
    return result

print(group_anagrams(input_words))
```
nice!