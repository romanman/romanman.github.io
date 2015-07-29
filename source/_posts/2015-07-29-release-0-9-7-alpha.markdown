---
layout: post
title: "Release: 0.9.7-alpha"
date: 2015-07-29 17:03:57 -0400
comments: true
categories: release
---

That version is first of the RC series for Frontier network.

 Ethereum(J) is the library that can be embedded in any Java/Scalla project
 and to provide full support for Ethereum protocol and sub services.

Support: 

##### RLPx network layer for channel protection
  
 Between each connected peers on the Ethereum network 
 there is a protection mechanism for encryption and decryption
 for all of the underlying traffic. 
  `org.ethereum.net.rlpx` - package is full implementation for the RLPx protocol
    

<!--more-->

##### PeerDiscovery 

 The peer discovery is ethereum way to manage network topology. Each peer
	tries over time to find best neighbours to exchange information with.
	That is beeing achieved by managing statistic table over the connected peers.
	 `org.ethereum.net.rlpx.discover` - is full implementation for peer disovery protocol
	 
