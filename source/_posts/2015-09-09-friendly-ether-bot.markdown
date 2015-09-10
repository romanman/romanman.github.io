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
[PriceFeed.sol]
(https://github.com/ether-camp/contracts/blob/master/PriceFeed.sol)

The full example for the feed reporter can be seen here (todo: link), we will just go over central code points : 

1.	How to get info from the exchanges, we will use Poloniex exchange to get ETH price spots: 


{% codeblock Code example lang:java https://github.com/ether-camp/feed.reporter.ether.camp/blob/master/src/main/java/com/ethercamp/feedreporter/service/PoloniexService.java %}


    // Accessing the rest point
    RestTemplate restTemplate = new RestTemplate();
    String rpcEnd = "https://poloniex.com/public?command=returnTicker";
    
    HttpHeaders headers = new HttpHeaders();
    HttpEntity entity = new HttpEntity(headers);
    
    ResponseEntity<Map<String, PoloniexInstrument>> response = null;


    response = restTemplate.exchange(rpcEnd, GET, entity,
            new ParameterizedTypeReference<Map<String, PoloniexInstrument>>(){});

    // parsing retrieved data 
    Map<String, PoloniexInstrument> output = response.getBody();

    for (MarketAsset asset : assets) {
        PoloniexInstrument pi = output.get(asset.getSymbol());
        ret.add(new MarketData(asset, new Date(), Double.parseDouble(pi.getLast())));
    }


{% endcodeblock %}

The full example can be seen here: [PoloniexService.java](https://github.com/ether-camp/feed.reporter.ether.camp/blob/master/src/main/java/com/ethercamp/feedreporter/service/PoloniexService.java)


Now after we got the updated info from the real world exchange let's push it into the Ethereum contract: 

{% codeblock Code example lang:java https://github.com/ether-camp/feed.reporter.ether.camp/blob/master/src/main/java/com/ethercamp/feedreporter/scheduler/Scheduler.java %}


// Accessing Ethereum managed been 
Ethereum ethereum = ethereumBean.getEthereum();

CallTransaction.Function function = CallTransaction.Function.fromSignature("update",
        "bytes32[]", "uint[]", "uint[]");

byte[] accountAddr = userKey.getAddress();


BigInteger nonce = ethereum.getRepository().getNonce(accountAddr);
log.info("======= Nonce: " + nonce);
long t = System.currentTimeMillis();
String[] symbols = new String[lastData.size()];
int[] prices = new int[lastData.size()];
long[] timestamps = new long[lastData.size()];

for (int i = 0; i < lastData.size(); i++) {
    symbols[i] = lastData.get(i).getAsset().getSymbol();
    prices[i] = (int) (lastData.get(i).getLastPrice() * 1_000_000);
    timestamps[i] = Utils.toUnixTime(lastData.get(i).getTime().getTime());
}


Transaction tx = CallTransaction.createCallTransaction(
        nonce.longValue(),
        70_000_000_000L, // => gas price
        1_000_000,       // => gas limit
        feedAccount,     // => the contract address we actually updating
        1,               // => value,  can be zero
        function,        // => abi definition of the call
        symbols, prices, timestamps // => params to update: for each: symbol~(price, timestamp)
);

tx.sign(userKey.getPrivKeyBytes());

log.info("=> Sending: " + tx);
ethereum.submitTransaction(tx);

{% endcodeblock %}


The full example can be seen here: [Scheduler.java](https://github.com/ether-camp/feed.reporter.ether.camp/blob/master/src/main/java/com/ethercamp/feedreporter/scheduler/Scheduler.java)

That is actually all you need to make your own bot to transfer data from the real world to an ethereum network.

After the contract is updated with data any other contract can access it and be built on top of market real prices. As we discussed in this forum post: [forum link](http://forum.ethereum.org/discussion/3417/ask-%CE%9E-community-what-do-you-think-of-our-new-smart-contract-pricefeed)

For any question or comment regarding that example you can ask us here [chat room](https://gitter.im/ethereum/ethereumj), or by leaving a comment directly.


