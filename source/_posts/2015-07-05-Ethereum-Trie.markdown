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

Let’s take a `“dog”` string as a key , in order to get it’s nibbles, will check the ASCII code which is `[ 0x64 0x6f 0x67 ]`, now the nibbles of the key are 4 bits of each byte: `6, 4, 6, f, 6, 7`.

Another example will be the key: “do” which obviously will be encoded to `6, 4, 6, f`. (“dog” with no “g”).

Now let’s see how we encode the key value `[“do” : “verb”]` and `[“dog” : “puppy”]` into a data structure: (Diagram - 1)

{% img center /images/posts/diagram-1.jpg Trie Headline %}

As we can see each node on the digaram has 17 elements which are representing slots for `[ 0..f ]` digits of encoding, and one more slot  - the last one, for the value (if it exist).

Now we see that to follow key: `“dog”` `(64 6f 67)` we simply follow the slot 6 in the first node, slot 4 in the second, slot 6 on the third, and so on until we bumped into a node that has a 17 slot  filled with value. Here we go `“dog”` leads as to `“puppy”` and the key: `“do”` stops two nibbles before `“dog”` and we found that the corresponding value is: `“verb”`.

That concludes the first step of making a Radix Tree in a new, hex slot oriented style.

So what do we get out of this complex structure? The answer is 2 things:

the keys with similar data will be saved economizing common prefixes.
the retrieve/insert of a value is about to be `O(log(n))` which is not bad at all.
 So far so good, can we improve it ? 


2. **How to improve it?** : The next question is: how we can improve the structure to be more storage effective ? to answer that we define a rule that can be explained in simple words like that: given “too long path” for a single value , we can make a single node holding the full path as long as it has this only one value. As always example is the best to see it:  

Starting with the last diagram, let’s insert another key/value: [“doggiestan” : “aeswome_place”]. As we can see the key – “doggiestan” starts with “dog” (looks familiar?) and goes on with more characters. So in that case we are not going to encode the suffix “giestan” as a bunch of nodes but as a single key/value node: (Diagram - 2)

{% img center /images/posts/diagram-2.jpg Trie Headline %}

**3. How to encode the finger print? :  **

The fingerprint will be the sha3(root_node) function. One will probably ask: “Why it is unique?” or “Why does every change in the trie affect the root node?”  To answer that let’s closely observe the next diagram:

{% img center /images/posts/diagram-3.jpg Trie Headline %}


In previous diagrams we used arrows to point out a node reference by another node, but in the data world, no arrows exist. So how is the reference actually implemented? Simply by saving the hash of the next node, in our case using `sha3()` function. That way, the most important goal of reflecting a change in data of each node, is achieved. Each change to a node will be reflected in the hash of that node, hence reflecting in the parent, and also his parent, and so on up to the root_node. That’s why `sha3(root_node)` is the absolute fingerprint of the full structure.

**Summary:**  Obviously the Trie structure as it is fully implemented holds more nuances and improvements. But once one understands the 3 principles that I have visualised here, it should be really easy to complete the picture by studying the actual code.

For futher investigation of the wonders of Trie , you can use our repository test cases, one I inserted especially for this post:

That is the runnig example for our test suite and can be used as start point for further study.

{% codeblock Code example lang:java https://github.com/ethereum/ethereumj/blob/develop/ethereumj-core/src/test/java/org/ethereum/trie/TrieTest.java#L917 %}
    @Test // update the trie with blog key/val
          // each time dump the entire trie
    public void testSample_1() {

        TrieImpl trie = new TrieImpl(mockDb);

        trie.update("dog", "puppy");
        String dmp = trie.getTrieDump();
        System.out.println(dmp);
        System.out.println();
        Assert.assertEquals("ed6e08740e4a267eca9d4740f71f573e9aabbcc739b16a2fa6c1baed5ec21278", Hex.toHexString(trie.getRootHash()));

        trie.update("do", "verb");
        dmp = trie.getTrieDump();
        System.out.println(dmp);
        System.out.println();
        Assert.assertEquals("779db3986dd4f38416bfde49750ef7b13c6ecb3e2221620bcad9267e94604d36", Hex.toHexString(trie.getRootHash()));

        trie.update("doggiestan", "aeswome_place");
        dmp = trie.getTrieDump();
        System.out.println(dmp);
        System.out.println();
        Assert.assertEquals("8bd5544747b4c44d1274aa99a6293065fe319b3230e800203317e4c75a770099", Hex.toHexString(trie.getRootHash()));
    }
{% endcodeblock %}

Any questions or comments are welcome.

Thanks to: **Nick Savers** for reviewing the draft and making my language to a real English.

* by **Roman Mandeleil**: the founder of the mighty Ethereum java implementation also known as EthereumJ.

