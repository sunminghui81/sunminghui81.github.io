---
layout: post
keywords: TextRank, extractive Chinese text summarization, extractive text summarization
description: 通过 TextRank 算法抽取中文文本中的关键句子来自动生成摘要
title: TextRank 算法抽取中文文本摘要
categories: [text-summarization]
tags: [TextRank, extractive Chinese text summarization, extractive text summarization]
group: archive
icon: list
---
{% include mathsyouth/setup %}


### 收集文本数据

文本数据来源于用户在京东上对锤子科技坚果 Pro 手机的差评，个人对每条评论的摘要总结如下：

1. [comment_1.txt](../data/jianguopro/comment_1.txt)： 前后摄像头模组本身以及模组和后壳开孔玻璃严重偏心，影响成像质量；后壳贴合间隙及断差明显；正面下方有3处残胶；type c有被插过的痕迹；出音孔C角有氧化不均；SIM卡卡槽非常难弹出来；按键回馈声音大；暂时还没找到快速释放缓存的方法；系统自带类似游戏中心并无卵用的应用不能直接删。个人有两个小建议：屏幕前离型膜黑色变透明或磨砂临时膜；ID的四个角如果做成连续曲线而不是倒圆角视觉效果会好很多。
1. [comment_2.txt](../data/jianguopro/comment_2.txt)：优点:胶囊，大爆炸，一步，都能提高我们的办事效率4G+64G 626的处理器 1799价格也挺合适的；缺点:割手 割手 割手 断差 断差 断差，贴个手机膜只能贴一半也是没谁了，最变态的是已经激活的手机不能退货，京东客服和锤子客服都确认过了，京东的七天无理由退货的牌子感觉要被砸了。
1. [comment_3.txt](../data/jianguopro/comment_3.txt)：开机感觉乱乱的；没有很多关于旅行的小工具；胶囊用的太别扭；打字反应和软件开启反应细节上不如魅族；输入法反应慢，感觉傻傻的；没有用户反馈中心，客服服务方面和软件改进速度方面没有魅族贴心；没有nfc软件。

### *TextRank4ZH* 抽取中文摘要

首先， 安装 *TextRank4ZH* 软件：

```
pip install textrank4zh
```

测试代码如下：

```python
import sys
try:
    reload(sys)
    sys.setdefaultencoding('utf-8')
except:
    pass

import codecs
from textrank4zh import TextRank4Keyword, TextRank4Sentence

text = codecs.open("jianguopro/comment_1.txt", 'r', 'utf-8').read()
tr4w = TextRank4Keyword()

tr4w.analyze(text=text, lower=True, window=2)

print "\n关键词："
for item in tr4w.get_keywords(10, word_min_len=2):
    print item.word, item.weight

print "\n关键短语："
for phrase in tr4w.get_keyphrases(keywords_num=25, min_occur_num= 1):
    print phrase

tr4s = TextRank4Sentence()
tr4s.analyze(text=text, lower=True, source = 'all_filters')

print "\n摘要："
for item in tr4s.get_key_sentences(num=5):
    print item.index, item.weight, item.sentence
```

`comment_1.txt` 的结果为：

```
关键词：
应用 0.0203934434487
角落 0.0167282064253
间隙 0.0164120701707
左右侧 0.0149902295247
重工 0.0149902295247
回馈 0.0149902295247
缓存 0.0149902295247
后壳 0.014247741991
摄像头 0.0138200591593
氧化 0.0137024638555

关键短语：
贴片做法
黑色变透明
做摄像头
角做
压铸喷漆

摘要：
14 0.0547074911526 卡槽顶针孔太大，几百块的魅蓝metal用压铸喷漆的工艺居然比使用阳极氧化的锤子做的好
1 0.0492631340767 1.前后摄像头模组本身以及模组和后壳开孔玻璃严重偏心，跟做摄像头的朋友沟通
17 0.0490979168899 7.系统自带类似游戏中心并无卵用的应用不能直接删
22 0.0423757658596 2.ID的四个角如果做成连续曲线而不是倒圆角视觉效果会好很多
23 0.0409271905854 iphone的角落包括应用图标都是如此
```

`comment_2.txt` 的结果为：

```
关键词：
手机 0.0428630492708
锤子 0.0413938853108
感觉 0.0402854451229
退货 0.0368472579424
情怀 0.0295652691736
京东 0.0255539853847
老罗 0.0250864844641
发布会 0.0249681923364
不负 0.0206398348813
有心人 0.0206398348813

关键短语：
锤子手机
感觉没有
不负有心人

摘要：
6 0.10906095026 已激活的手机不支持退货
7 0.10906095026 已激活的手机不支持退货
8 0.10906095026 已激活的手机不支持退货
5 0.108611615664 谁还敢信，买电器上京东，都不支持退货了，感觉被坑了，4年的锤粉，感觉没有爱了
4 0.0990865474525 缺点:割手 割手 割手 断差 断差 断差，贴个手机膜只能贴一半也是没谁了，最变态的是已经激活的手机不能退货，京东客服和锤子客服都确认过了，京东的七天无理由退货的牌子感觉要被砸了
```

`comment_3.txt` 的结果为：

```
关键词：
感觉 0.0340273348183
没有 0.0268900340112
反应 0.0233718839288
软件 0.0202187903934
方面 0.0196369730934
胶囊 0.0171286945504
魅族 0.0168704845176
屏幕 0.0165066311834
使用 0.0161353911118
用户 0.0159142935308

关键短语：
想买
想说
没有用户
自动切换成
胶囊感觉
方面没有魅族
胶囊过来

摘要：
8 0.159836188343 还有本来想买低版本但低版本没有nfc，硬咬牙买个高版本，想着以后不用带银行和门禁、公交卡，拿到手机后连个nfc软件都没有，感觉被骗的感觉又再脑海闪现，倒被同事说了和SB了吧、被忽悠了吧、2299加屏幕保险2399，心里有多难受你们不知道，我想说能退不
2 0.137642951854 还有打字反应和软件开启反应细节上不如魅族，有可能是使用习惯，但我想说的那个胶囊测试后要删除的时候拖到垃圾箱直接删除不就得了吗
3 0.110256358176 然后再弹出来，问你要不要删除，感觉烦死了，不像老罗说的能少一步操作就少一步操作
6 0.110213186426 又没有用户反馈中心，客服服务方面和软件改进速度方面没有魅族贴心，最起码你提出来的东西人家就能周周更新
1 0.0993976227866 什么胶囊感觉就是给老罗自己单独设计的，我平日业务也比较忙，本来冲着着胶囊过来的，用的时候太别扭
```

通过以上三个实例，我们可以看出，*TextRank4ZH* 提取中文摘要的效果并不是很好：首先，它对句子的定义是用标点符号，如句号，感叹号等为标识，这对于评论来说，断句不够准确；其次，无论是关键词、关键短语或者摘要都无法反应评论的重点信息，特别是关键词和关键短语没有任何有用的关键信息；最后，生成的摘要冗余信息太多。

### *SnowNLP* 抽取中文摘要

首先， 安装 *SnowNLP* 软件：

```
pip install snownlp
```

*SnowNLP* 利用 *TextRank* 算法来提取文本中的关键词和摘要，测试代码如下：

```python
import sys
try:
    reload(sys)
    sys.setdefaultencoding('utf-8')
except:
    pass

import codecs
from snownlp import SnowNLP

text = codecs.open("jianguopro/comment_1.txt", 'r', 'utf-8').read()

s = SnowNLP(text)

print "\n关键词："
for item in s.keywords(10):
    print item

print "\n摘要："
for item in s.summary(5):
    print item
```

`comment_1.txt` 的结果为：

```
关键词：
不
做
大
孔
角
还
间隙
系统
后壳
侧

摘要：
2.ID的四个角如果做成连续曲线而不是倒圆角视觉效果会好很多
出音孔C角有氧化不均
1.前后摄像头模组本身以及模组和后壳开孔玻璃严重偏心
正面中框左右侧宽度不均
卡槽顶针孔太大
```

`comment_2.txt` 的结果为：

```
关键词：
手机
都
不
买
天
入手
情怀
感觉
锤子
理由

摘要：
已激活的手机不支持退货
已激活的手机不支持退货
已激活的手机不支持退货
都不支持退货了
感觉没有爱了
```

`comment_3.txt` 的结果为：

```
关键词：
不
感觉
说
软件
输入
后
想
魅族
胶囊
好

摘要：
购买后感觉一点也不好
但我想说的那个胶囊测试后要删除的时候拖到垃圾箱直接删除不就得了吗
这个输入法倒好反应慢不说
还有一个我想说的输入密码的时候要输入大小写
我想说能退不
```

和 *TextRank4ZH* 的测试效果对比，我们可以看出，*SnowNLP* 提取中文摘要的效果也不是很好：首先，它对句子的定义是用逗号为标识，断句相对句号而言要准确一些，但还是有待改进；其次，无论是关键词还是摘要都无法反应评论的重点信息，特别是关键词没有任何有用的关键信息；最后，生成的摘要冗余信息太多。总之，对于提取中文关键词和摘要而言，*TextRank* 算法还有很大的改进空间，后续我将进一步解读 *TextRank* 算法。

### 参考文献
1. [TextRank4ZH](https://github.com/letiantian/TextRank4ZH) implements the *TextRank* algorithm to extract key words/phrases and text summarization
in Chinese. It is written in Python.
1. [SnowNLP](https://github.com/isnowfy/snownlp) is python library for processing Chinese text.
