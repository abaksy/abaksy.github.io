---
layout: page
title: palying with bloom filters
description: the fun world of probabilistic data structures 
img: assets/img/db-image.jpg
importance: 1
category: personal
related_publications: false
---

I'll freely admit that the very idea of a "probabilistic" data structure weirded me out at first. Don't we rely on computers to be deterministic? Shouldn't a deterministic, bug-free program produce the same output each time when provided the same input? 

But real life use cases in distributed systems tend to bend the rules of things one learns in an academic setting. Using hashing-based sets for the applications that Bloom filters are used for (ranging from various types of recommender systems, to caching systems like Akamai's CDN and Google Chrome's safe site list, all of which can be reduced to "check if an element is present in a set") would be prohibitively expensive from a memory utilization point of view, and the magic behind fast lookup in Bloom filters which is influenced by the fact that memory usage is a strict restriction, is truly fantastic!

A Bloom filter is essentially an array of M bits. There are K hash functions which transform an element into a set of bits, which are then set in the array. The membership test is essentially checking whether the bits returned by the K hash functions are set or not. Given a set size N, and a desired error rate e, it's possible to estimate both M and K. [Wikipedia](https://en.wikipedia.org/wiki/Bloom_filter) is probably the best source of all the math behind the Bloom filter. 


Implementing the project in Go was such fun! It's so nice to use a language that has fast compile times, a fantastic, world-class standard library (some langauges just can't [relate](https://x.com/mitsuhiko/status/1886007964785336334)) and modern tooling. 

This project is still far from complete though. There are so many variations on Bloom filters that I'm keen on exploring and adding to this project, so, watch this space!