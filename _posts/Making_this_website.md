---
title: Making This Website
date: 2025-01-01 7:00:00 -500
categories: [programming, website development]
tags: [programming,development,website]
---

# Making this website

idk what to say lalala

* a
* b
* c

this is cool

```
so i can write in this box
```

and do this...
python is the best

```python
print("hello world")
```

and this...
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

dang thats cool!