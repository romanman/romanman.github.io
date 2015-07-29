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
 
##### Multi Peer blockchain syncronization
	
 The Ethereum protocol supports fast block chain download - using
 bittorent way of downloading multiple blocks simultaneously from different channels
 
 `org.ethereum.net.eth` - us full support of Eth subprotocol and multipeer blocks download

##### Full Ethereum VM support 

 The heart of the Ethereum consensus protocol is the virtual machine.
 The virtual machines is running the contracts algorithms exactly the 
 same way on all the peers, tested and compatible with all implementations.
 
 `org.ethereum.vm` - full Ethereum Virtual Machine implemented]
	
##### Ethereum Repository updates and manipulations
 
 The data structure that supports consensys validation 
 algorithm, and eventually holding the full list of 
 ethereum affairs is called Ethereum Repository. 
 It embrace Ethereum Trie protocol, more can be 
 study [here](/blog/2015/07/05/Ethereum-Trie/)
   
 `org.ethereum.repository` - full Ethereum Virtual Machine implemented
      
##### Testing notes

 The EthereumJ librarey Was tested on 800k blocks of POC-9 olympic with final result of 
 full consensus reached

 The library supports 99% of compatabiltiy kit test case. We support almost all test cases defined by 
 Ethereum development group, the several adge cases that we formally not support we decided to exclude
 at that stage for performance optimization.
 
##### How To Start

 
  To include the EthereumJ library: use your favorite build system:
 
 [Maven] (~!~) (to check for real)
 
{% codeblock Maven Snippet lang:xml http://www.google.com %}  
   
   <repositories>
	  <repository>
		 <id>oss.jfrog.org</id>
		 <name>Repository from JFrog</name>
		 <url>http://oss.jfrog.org/simple/oss-snapshot-local/</url>
	   </repository>
    </repositories>
 
 
   <dependency>
	<groupId>org.ethereum</groupId>
	<artifactId>ethereumj-core</artifactId>
	<version> (~!~) 0.9.6-SNAPSHOT</version>
	<type>zip</type>
   </dependency>
{% endcodeblock %}     
	 
