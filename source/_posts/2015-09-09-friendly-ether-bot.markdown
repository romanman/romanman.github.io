---
layout: post
title: "Friendly Ether Bot"
date: 2015-09-09 17:03:57 -0400
comments: true
categories: sample
---
{% img center /images/posts/friendly-bot-1.png Friendly Ether Bot %}


Ethereum is a great system to achieve consensus between an independent peers, but if we want to pump data from the real world into the system , how exactly we do it ?

<!--more-->

Let's write a simple example of server bot to pump real price spot data into a special contract: 
https://github.com/ether-camp/contracts/blob/master/PriceFeed.sol

The full example for the feed reporter can be seen here (todo: link), we will just go over central code points : 

1.	How to get info from the exchanges, we will use Poloniex exchange to get ETH price spots: 


{% codeblock Code example lang:java https://github.com/ether-camp/feed.reporter.ether.camp/blob/master/src/main/java/com/ethercamp/feedreporter/service/PoloniexService.java %}


// Accessing the rest point
RestTemplate restTemplate = new RestTemplate();
String rpcEnd = "https://poloniex.com/public?command=returnTicker";

HttpHeaders headers = new HttpHeaders();
HttpEntity entity = new HttpEntity(headers);

ResponseEntity<Map<String, PoloniexInstrument>> response = null;
try {
    response = restTemplate.exchange(rpcEnd, GET, entity,
            new ParameterizedTypeReference<Map<String, PoloniexInstrument>>(){});

// parsing retrieved data 
Map<String, PoloniexInstrument> output = response.getBody();

for (MarketAsset asset : assets) {
    PoloniexInstrument pi = output.get(asset.getSymbol());
    ret.add(new MarketData(asset, new Date(), Double.parseDouble(pi.getLast())));

{% endcodeblock %}
