---
layout: post
title: "Climbing Ethereum Trie"
date: 2015-07-05 17:03:57 -0400
comments: true
categories: architecture
---
{% img center /images/posts/tumblr_inline_nayhun2I9B1stehdi.jpg Trie Headline %}


The Ethereum data structure which is used to calculate the state of all the current balances, all the contract code and even the storage which is occupied by a particular contract, is still difficult for some developers on the project. I actually found myself having a hard time trying to understand it. This is why I will try to make a clear and visual explanation of the simple concepts and ideas that stand behind this important structure. 

**Why do we need it?**

Before designing and using a new data structure, it’s always good to start by asking: “Why?” or more specifically, “What are the main requirements that we need so bad, that we are designing a new data structure?”

In our case we have three:

1. We need a storage efficient hash map.
2. We need a very time efficient insert/delete hash map.
3. Most important: We need a fingerprint to identify the state of the whole structure. The fingerprint should be calculated very fast on the fly, obviously depended on all the changes the hash map going to have.

So with the requirements defined, let’s see how the structure is built and why it is appropriate for our goals.

**1. How is the structure built?**

The structure will be a tree, which will be built on a principle of what is known as a Radix Tree, the Radix Tree principle can be explained simply as:

Given a key/value that should be inserted, the key will be split to it’s sub parts e.g. characters and each value will be a node in a tree, so if we want to retrieve the reference value we should jump from node to node each representing part of the key until we find the end and there we will have the value.

In our case we will split the key to a nibbles of it’s ASCII code, don’t worry the example will make it clear:

Let’s take a “dog” string as a key , in order to get it’s nibbles, will check the ASCII code which is [ 0x64 0x6f 0x67 ], now the nibbles of the key are 4 bits of each byte: 6, 4, 6, f, 6, 7.

Another example will be the key: “do” which obviously will be encoded to 6, 4, 6, f. (“dog” with no “g”).

Now let’s see how we encode the key value [“do” : “verb”] and [“dog” : “puppy”] into a data structure: (Diagram - 1)

