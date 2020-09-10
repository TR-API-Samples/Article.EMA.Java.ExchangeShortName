# How to get Exchange Information from a Market Price Domain using EMA Java

## Overview
For some instruments, suffix of RIC indicates the exchange where it is traded, e.g. HSBA.L (.L = LSE - London Stock Exchange), HBC.N (.N = NYSE - New York Stock Exchange). However, there are some instruments without the exchange code suffix. How the application know the exchange of those instruments?

## Solution
Refinitiv Real-Time provides a short exchange name information via the FID 1709 (Field name RDN_EXCHD2) for the Market Price domain. This FID 1709 is the enumeration field that used in the most of the exchanges. The [Refinitiv Real-Time SDK 1.1.0 (Enterprise Message API Java 3.1.0)](https://developers.refinitiv.com/elektron/elektron-sdk-java) (formerly known as Elektron SDK/Elektron Message API) and above can retrieve an enum value from dictionary directly, so it can help a consumer application get a short exchange name from this enumeration field.


#### RDMFieldDictionary
```
RDN_EXCHD2 "EXCHANGE ID 2"       1709  NULL        ENUMERATED    5 ( 3 )  ENUM             2
```

#### enumtype.def
```
RDN_EXCHD2  1709
...
! VALUE      DISPLAY   MEANING
! -----      -------   -------
      0        "   "   undefined
      1        "ASE"   NYSE AMEX
      2        "NYS"   New York Stock Exchange
      3        "BOS"   Boston Stock Exchange
      4        "CIN"   National Stock Exchange (formerly Cincinnati Stock Exchange)
      5        "PSE"   NYSE Arca
      6        "XPH"   NASDAQ OMX PSX when trading in SIAC (formerly Philadelphia Stock Exchange)
	...
      1454     "PCW"   PetroChem Wire LLC
      1455     "SMP"   Euronext - Smartpool
      1456     "BT1"   BATS ONE - LEVEL 1 (PRODUCT)
```

### Demo prerequisite
This example requires the following dependencies softwares and libraries.
1. Oracle/Open JDK 8 or Oracle JDK 11.
2. [Apache ANT](http://ant.apache.org/) project management.
3. [Apache IVY](https://ant.apache.org/ivy/) dependency manager tool.
4. Internet connection. 

*Note:* 
The Real-Time SDK Java version 1.5.1 (EMA Java 3.5.1) supports Oracle JDK versions 8, 11 and Open JDK version 8. If you are using other versions of Real-Time SDK Java, please check the SDK's [README.md](https://github.com/Refinitiv/Elektron-SDK/blob/master/Java/README.md) file regarding the supported Java version.

The Real-Time SDK Java is now available in [Maven Central Repository](https://search.maven.org/). This demo application use Apache IVY download the EMA Java and its dependencies jars file for the application.

The example code has been tested with Real-Time SDK Java version 1.5.1 (EMA Java 3.5.1 - ivy.xml dependency: ```<dependency org="com.thomsonreuters.ema" name="ema" rev="3.5.1.0" />```).

## Running the application
You can get it via the following git command
```
$>git clone git@github.com:Refinitiv-API-Samples/Article.EMA.Java.ExchangeShortName.git
```
Note: The application works with EMA Java 3.1.0 (Real-Time SDK 1.1.0) and above which supports the enum parsing only.

You can build the application and run it via the following steps

1. Install and configure [Apache ANT](http://ant.apache.org/) and [Apache IVY](https://ant.apache.org/ivy/) in your machine

3. Configure the Channel_1 of EmaConfig.xml file to specify the host name and RSSL Port of the server (the ADS or Refinitiv Real-Time) to which the EMA connects. This is for setting values of the <ChannelGroup><ChannelList><Channel><Host> node. This value can be a remote host name or IP address.

```
<Channel>
      <Name value="Channel_1"/>								
      <ChannelType value="ChannelType::RSSL_SOCKET"/>													
	<CompressionType value="CompressionType::None"/>
	<GuaranteedOutputBuffers value="5000"/>
      <ConnectionPingTimeout value="30000"/>
	<TcpNodelay value="1"/>

	<Host value="[Your ADS HOST]"/>
	<Port value="[Your ADS RSSL Port]"/>
</Channel>
```

4. You can change the requested service and item name in the following line of code to match your environment
```
consumer.registerClient(EmaFactory.createReqMsg().serviceName("<service>").name("<item name>").interestAfterRefresh(false).payload(view), appClient);
```

5. All application class files will be available at "out" folder, the EmaConfig.xml also copied to the out folder automatically.
```
$>ant build
```

6. Stay in the same location, run the application with the following command
```
java -cp out;lib/* com.refinitiv.platformservices.article.ExchangeName
```

7. The example output when you run the application for each item name:
```
//IBM.N
Item Name: IBM.N
Service Name: ELEKTRON_DD
Item State: Non-streaming / Ok / None / 'All is well'
Fid: 3 Name = DSPLY_NAME DataType: Rmtes Value: INTL BUS MACHINE
Fid: 22 Name = BID DataType: Real Value: 0.0
Fid: 25 Name = ASK DataType: Real Value: 0.0
Fid: 1709 Name = RDN_EXCHD2 DataType: Enum Value: NYS //FID value: 2          "NYS"   New York Stock Exchange

//HSBA.L
Item Name: HSBA.L
Service Name: ELEKTRON_DD
Item State: Open / Ok / None / 'All is well'
Fid: 3 Name = DSPLY_NAME DataType: Rmtes Value: HSBC HOLDINGS
Fid: 22 Name = BID DataType: Real Value: 642.8
Fid: 25 Name = ASK DataType: Real Value: 642.9
Fid: 1709 Name = RDN_EXCHD2 DataType: Enum Value: LSE //FID value: 64        "LSE"   London Stock Exchange

//PTT.BK
Item Name: PTT.BK
Service Name: ELEKTRON_DD
Item State: Non-streaming / Ok / None / 'All is well'
Fid: 3 Name = DSPLY_NAME DataType: Rmtes Value: PTT
Fid: 22 Name = BID DataType: Real Value: 35.25
Fid: 25 Name = ASK DataType: Real Value: 35.5
Fid: 1709 Name = RDN_EXCHD2 DataType: Enum Value: SET //FID value: 158        "SET"   The Stock Exchange of Thailand
```
## References
For further details, please check out the following resources:
* [Refinitiv Real-time Java API page](https://developers.refinitiv.com/elektron/elektron-sdk-java/) on the [Refinitiv Developer Community](https://developers.refinitiv.com/) web site.
* [Enterprise Message API Java Quick Start](https://developers.refinitiv.com/elektron/elektron-sdk-java/quick-start)
* [Developer Webinar: Introduction to Enterprise App Creation With Open-Source Enterprise Message API](https://www.youtube.com/watch?v=2pyhYmgHxlU)

For any question related to this article or Enterprise Message API page, please use the Developer Community [Q&A Forum](https://community.developers.refinitiv.com/spaces/72/index.html).
