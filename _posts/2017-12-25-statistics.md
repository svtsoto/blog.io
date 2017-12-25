---
layout: post
title:  广东楼盘区域价格、取名套路及竞争点简单分析（附源码）
date: 2017-12-25
categories: blog
tags: [统计]
description: svt's blog.
---


 广东楼盘区域价格、取名套路及竞争点简单分析          
	
  无论是早晨7点拥挤的深圳地铁，还是闲适的广州茶馆，理解一座城市首先需要去了解这座城市的生活。生活包括了方方面面，但最不可缺少的，还要属房子。在中国，房子的重要性更为突出，它承载着每个人的根基，影响着社会生活的方方面面。
	
  为了对广东房产（具体到居住用途的商品房）有一个整体性的了解，我编写了数据爬取代码来帮助我批量获取广东主要区域的楼盘信息。
  
  统计楼盘数							*数据采集自“安居客” 
  
  4,827                采集时间 2017.12.04    
<center>
    <p><img src="https://upload-images.jianshu.io/upload_images/9385446-90276c5ee3faba7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700" align="center"></p>
</center>
  
上图表示了本次分析数据涉及的主要城市以及各个城市的统计楼盘数。由于数据源（安居客）可能存在疏漏，统计楼盘数并不代表真实楼盘数，但仍具有一定的代表性：如各个城市的统计楼盘数量，可表明该城市的地产活跃度。

*考虑房子的第一要素自然是房价，那么广东房价的整体分布如何？
<center>
    <p><img src="https://upload-images.jianshu.io/upload_images/9385446-2f8da64b12d9e0c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700" align="center"></p>
</center>

整体平均房价逼近2w，无疑处于很高的水平；省内市间平均房价差异大，特别是广州、深圳、珠海三市，平均房价均突破2w大关，在全国处于绝对的高位。

而韶关市的房价则比较尴尬，放在全国也属于较低的位次。

现在，我们大概对广东的楼盘价格有了初步的认识，我们不妨放下这些枯燥的数字，来看一些有趣的东西。

楼盘的命名是一门学问，好的楼盘名可以突出主题，升华价值；而糟糕的楼盘名甚至会起到负面的效果。

让我们来看看广东的地产开发商们是如何起名字的吧！
<center>
    <p><img src="https://upload-images.jianshu.io/upload_images/9385446-8cb9f9a6cfd90f04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/507" align="center"></p>
</center>


Emmm..看来经久不衰的广场、花园、国际、公馆在广东依然是主流。也就是说，今天我们去上班的路上，经过的楼盘很有可能就叫**花园。

除了这些短语，单字也很有趣，它们代表了财富、幸福、成功等等一系列能引发人美好遐想的事物。

所以下次如果让我来给一个楼盘命名，如果实在没主意的话，从上图中随便抓几个关键字/词组成一个3-5字的短句就能把这个难题解决了。

比如现在要给个楼盘命名，要求突出其高端大气属性，我们完全可以从上图选择2个单字+1个二字词语进行搭配。

“尚豪国际”、“壹号公馆”，这俩是我随便搭配的，看起来还不错，对吧？

了解楼盘的命名套路后，我们也应该看看广东各楼盘在宣传里所强调的竞争点。

竞争点对于地产开发十分重要，一个好的定位与策略，可以使项目实现更高的收益。

我们同样使用下图这种直观的表达方式来观察广东地产商们极力宣传的楼盘优势有哪些。一定程度上，这些词出现的频率越高，该类楼盘的市场热度越高。
<center>
    <p><img src="https://upload-images.jianshu.io/upload_images/9385446-38bd54e064a18e8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/507" align="center"></p>
</center>


对于都市人来讲，交通是选择房子的第一因素。

所以能看到，”轨交房“出现的频率是最高的。

同时，经过预装修的非毛坯房、改善房、临近商场等关键词都是楼盘定位及宣传的热点。

市场中，卖家产品很大程度上反映了买家的需求。于是我们同样可以通过上图来洞悉近期楼盘市场中的热门需求。



---------------------------------------分割线------------------------------------------------

本文数据提取自安居客，使用框架：scrapy
这里附上部分源码，以供参考

spider.py

```
# -*- coding:utf-8 -*-
import sys  
reload(sys)  
sys.setdefaultencoding('utf8')
import urllib2
from lxml import etree
import scrapy
from scrapy.contrib.spiders import CrawlSpider, Rule
from scrapy.contrib.linkextractors.sgml import SgmlLinkExtractor
from scrapy.selector import HtmlXPathSelector
from scrapy.item import Item
from ajk.items import ajkItem
import re
from scrapy.http import Request
class ajkSpider(CrawlSpider):
    #定义爬虫的名称
    name = "ajkSpider"
    #定义允许抓取的域名,如果不是在此列表的域名则放弃抓取
    allowed_domains = ["58.com"]
    #定义抓取的入口url
    start_urls = [
        "http://sz.58.com/pinpaigongyu/pn/104/"
    ]
    # 定义爬取URL的规则，并指定回调函数为parse_item
    rules = [
        Rule(SgmlLinkExtractor(allow=(r'http://sz.58.com'),
                      ))
                       
    ]
    #提取数据到Items里面，主要用到XPath和CSS选择器提取网页数据
    def parse(self, response): 
        for info in response.xpath('//a[@tongji_label="listclick"]'):
            item = ajkItem()
            name = info.xpath('div[@class="des"]/h2/text()').extract()
            #address= info.xpath('div[@class="infos"]/a[@class="address"]/span/text()').extract()
            #huxing = info.xpath('div[@class="infos"]/a[@class="huxing"]/span/text()').extract()
            onsale = info.xpath('div[@class="des"]/p[@class="room"]/text()').extract()
            notsale = info.xpath('div[@class="des"]/p[@class="spec"]/span[@class="spec1"]/text()').extract()
            proper = info.xpath('div[@class="des"]/p[@class="spec"]/span[@class="spec2"]/text()').extract()
            special = info.xpath('div[@class="des"]/p[@class="spec"]/span[@class="spec3"]/text()').extract()
            price = info.xpath('div[@class="small-logo"]/text()').extract()
            tel = info.xpath('div[@class="money"]/span/b/text()').extract()
            #scrapy crawl ajkSpider -o info.csv -t csv命令执行csv输出。    
            if name:                
                item['name']=name[0].encode('utf-8')
            
           # if address:
                #item['address']=address[0].encode('utf-8')
           # if huxing:
            #    item['huxing']=huxing[0].encode('utf-8')
            if onsale:
                item['onsale']=onsale[0].encode('utf-8')
            if notsale:
                item['notsale']=notsale[0].encode('utf-8')

            if proper:
                item['proper']=proper[0].encode('utf-8')
            
            if price:
                item['price']=price[0].encode('utf-8')
            if special:
                item['special']=special[0].encode('utf-8')
            if tel:
                item['tel']=tel[0].encode('utf-8')
            yield item
                
            

    # 翻页
        next_page = response.xpath('//div[@class="page"]/a[@class="next"]/@href')                
        if next_page:
            url =response.urljoin(next_page[0].extract())
      	    yield scrapy.Request(url,callback=self.parse)
            
```

所有在spider.py中定义的爬取数据都要在item.py里有所体现

scrapy有些坑要注意，比如callback，比如dont——filter，这些都是很好的功能，但在入门时容易走偏

另外注意在settings.py中添加一条：
```
ROBOTSTXT_OBEY = False
```
