---
layout: page
title: pycask
description: an in-memory key-value store in pure Python
img: assets/img/db-image.jpg
importance: 1
category: personal
related_publications: false
---

This was a fun little project I built after reading the [BitCask paper](https://riak.com/assets/bitcask-intro.pdf). And I get that Python isn't the natural choice of programming language for a project like this one, but given how conceptually simple it all felt to me, I decided to go for it anyway. 6 years on from the time I first learnt Python, its the place I feel most at, with it's beautiful [dynamic types](https://www.reddit.com/r/ProgrammerHumor/comments/96jj09/when_youre_not_used_to_dynamically_typed_languages/), the intuitive [syntax](https://www.reddit.com/r/ProgrammerHumor/comments/eaeocs/indentation_checking_in_python_is_like/) and blazing [performance](https://programmerhumor.io/python-memes/the-final-benchmark-we-can-now-end-the-speed-debate-about-c-vs-python/).

Unfunny memes aside, let's dive right into the internals of Bitcask. It essentially contains an in-memory data structure called a **keydir**, and a disk file store. The keydir is structured as a hash table, with the key mapping to a data structure containing the insertion time, the size of the value in bytes, and the offset in the disk file from where the value is to be read. Knowing the offset and size of the value makes it trivially easy to read it in from the disk file, instead of having to linearly seek through all the bytes in the file. 

{% include figure.liquid loading="eager" path="assets/img/pycask.png" title="Structure of a BitCask Database" class="img-fluid rounded z-depth-1" %}

The disk store contains a data structure that contains the sizes of the key and value, along with their respective values, and some book-keeping information like the CRC value of the entire data, and the insertion timestamp. The only real overhead is the one-time effort of building the in-memory keydir each and every time the database is initialized, from the disk file.


Class ```BitCaskDataStore``` is the interface for any third-party user of the database to initialize a data store, and add/get key-value pairs from the database. The API is fairly idiot-proof, and looks something like this 

```python
import bitcask

datastore = bitcask.BitCaskDataStore()
datastore.put("key1", "value1")
print(datastore.get("key1"))s
datastore.close()
```

The driver code in ```driver.py``` contains some very high-level basic benchmarks of insertion and retrieval time for a number of keys ranging from 10 to 100,000. We see a very slight linear growth in retrieval time for random access, as the size of the database increases exponentially, which is an awesome result, especially given that the benchmark tests random access and not sequential access.

{% include figure.liquid loading="eager" path="assets/img/pycask_results.png" title="Benchmarks on pycask" class="img-fluid rounded z-depth-1" %}