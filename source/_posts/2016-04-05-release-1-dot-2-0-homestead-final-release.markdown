---
layout: post
title: "Release: 1.2.0  Homestead Final Release"
date: 2016-04-05 17:57:40 -0400
comments: true
categories: release
---

{% img right /images/posts/release-ilustration-1.gif Release 1.2.0 %}

> by Anton Nashatyrev

Ethereum(J) is the library that can be embedded in any Java/Scala project
 and to provide full support for Ethereum protocol and sub services
 
<!--more--> 

##### Homestead compatibility

Starting from the earlier 1.2.0 Release Candidate EthereumJ is 100% Homestead compatible

##### Solidity compiler is now embedded into EthereumJ

Using Solidity contracts is now pretty easy from Java as we embedded `solc` compiler (thanks to our collegues from [Ethereum-cpp](https://github.com/ethereum/solidity) team) into EthereumJ for all supported platforms (Windows, Linux, Mac OS X). 

For usage sample please refer to a simple JUnit test: [CompilerTest](https://github.com/ethereum/ethereumj/blob/develop/ethereumj-core/src/test/java/org/ethereum/solidity/CompilerTest.java)

##### Handy helper classes to create/invoke/test contracts on a local standalone blockchain 

As a next step after embedding the Solidity compiler we added some handy and easy to use helper classes. Now creating and testing a Solidity contract in Java without connecting to any Ethereum network became incredibly easy.

{% codeblock Easy Contract Test Snippet lang:java https://gist.github.com/romanman/3488bc5a106f915e734ed10ea29dd75a %}  
    StandaloneBlockchain bc = new StandaloneBlockchain();
    
    SolidityContract contract = 
         bc.submitNewContract(
           "contract A { uint a; ... }"
           );
         
    contract.callFunction("funcName", "arg1", 
                       2, new byte[] {1,2,3}, "arg4");
    bc.createBlock()
    
    System.out.println("Result: " + 
         contract.callConstFunction("getResultFunc"));
{% endcodeblock %}

You may cover any complex Solidity contract with Java unit tests and debug the EVM execution in any complex case.

For more details take a look at the small [sample](https://github.com/ethereum/ethereumj/blob/develop/ethereumj-core/src/main/java/org/ethereum/samples/StandaloneBlockchainSample.java) and [helper classes](https://github.com/ethereum/ethereumj/tree/develop/ethereumj-core/src/main/java/org/ethereum/util/blockchain) docs

##### Performance and memory footprint optimization 

We further optimized performance and now first 1M blocks can be imported in less than 1 hour (depending on the hardware). Memory footprint had been also reduced and the node could now be started with `-Xmx256M` (with some config changes).

##### More stable node run 

We have fixed amount of bugs, refactored blockchain sync process and made it more reliable, upgraded Windows `LevelDB` version to `1.18` (which fixed periodical DB crash)

##### Running EthereumJ

###### Adding as maven artifact to your project: 

{% codeblock Maven Snippet lang:xml https://gist.github.com/romanman/5f20d68d294ec1d049b5 %}  
<repositories>
 	<repository>
   	<id>oss.jfrog.org</id>
   	<name>Repository from Bintray</name>
   	<url>http://dl.bintray.com/ethereum/maven</url>
 	</repository>
</repositories>
 
 
<dependency>
  <groupId>org.ethereum</groupId>
  <artifactId>ethereumj-core</artifactId>
  <version>1.2.0-RELEASE</version>
  <type>pom</type>
</dependency>
{% endcodeblock %}   

or gradle: 

{% codeblock Gradle Snippet lang:groovy https://gist.github.com/romanman/55964ce71acdd9b51d8c %}
   repositories {
       maven {
  	url "http://dl.bintray.com/ethereum/maven"
       }
   }

   compile ("org.ethereum:ethereumj-core:1.2.0-RELEASE")
{% endcodeblock %}   

As a starting point for your own project take a look at https://github.com/ether-camp/ethereumj.starter

###### Running from command line:

{% codeblock command line lang:bash https://gist.github.com/romanman/0bd6df410689af1a86b4 %}
 git clone https://github.com/ethereum/ethereumj
 cd ethereumj
 gradlew run [-PmainClass=<sample class>]
{% endcodeblock %}   


###### Importing project to IntelliJ IDEA: 

{% codeblock command line lang:bash https://gist.github.com/romanman/ab27a4a0d879ded73aa3 %}
 git clone https://github.com/ethereum/ethereumj
 cd ethereumj
 gradlew build
{% endcodeblock %}   

  IDEA:
* File -> New -> Project from existing sources…
* Select `ethereumj/build.gradle`
* Dialog “Import Project from gradle”: press “OK”
* After building run either `org.ethereum.Start`, one of `org.ethereum.samples.*` or create your own main. 

##### Configuring EthereumJ

For reference on all existing options, their description and defaults you may refer to the default config `ethereumj.conf` (you may find it in either the library jar or in the source tree `ethereum-core/src/main/resources`) 
To override needed options you may use one of the following ways: 

* put your options to the `<working dir>/config/ethereumj.conf` file
* put `user.conf` to the root of your classpath (as a resource) 
* put your options to any file and supply it via `-Dethereumj.conf.file=<your config>`
* programmatically by using `SystemProperties.CONFIG.override*()`
* programmatically using by overriding Spring `SystemProperties` bean 

Note that don’t need to put all the options to your custom config, just those you want to override. 

