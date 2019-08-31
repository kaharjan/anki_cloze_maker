# anki_cloze_maker
根据jieba的tf-idf算法，及自定义的关键词，对.txt文件批量生成anki填空符。

## 运行环境
1. Windows系统
2. Python3

## 感谢
### 结巴中文分词
linhx13等人所作的[结巴中文分词](https://github.com/fxsjy/jieba)，anki_cloze_maker使用它的tf-idf算法提取关键词，再结合自定义的关键词，对其生成填空符。
### 中文停止词库来源
yinzm所作的[常用的中文停用词表](https://github.com/yinzm/ChineseStopWords#中文常用停用词表)， anki_cloze_maker使用它过滤不必要的词，不会生成填空符。

## 启发
本人阅读supermemo的[The 20 rules of formulating knowledge in learning](http://super-memory.com/articles/20rules.htm)，有所启发，特别是第五条Cloze deletion is easy and effective讲述填空的部分，深有感触。第五条揭示了对一段文本可以采用关键词挖空的形式多次记忆，形式如下：

>问：秋天的...，到处是火红火红的枫叶，朝霞初照，像落在山腰的红云彩；晚霞辉映，像一团团玛瑙。

>答：山野

>问：秋天的山野，到处是火红火红的...，朝霞初照，像落在山腰的红云彩；晚霞辉映，像一团团玛瑙。

>答：枫叶

>问：秋天的山野，到处是火红火红的枫叶，朝霞初照，像落在山腰的...；晚霞辉映，像一团团玛瑙。

>答：红云彩

>问：秋天的...，到处是火红火红的...，朝霞初照，像落在山腰的...；晚霞辉映，像一团团玛瑙。

>答：山野 枫叶 红云彩

可以看到，通过对一句话分批记忆每个填空，最后再一次性记忆所有的填空，可以大大提高学习效率。

当然，你也可以将知识点进一步拆分，采用自问自答的方式强化记忆，但由于自己设置问题需要花费一定的功夫，故不建议这么做。如果没有多少时间，最好采用填空的形式辅助记忆。

## 解决问题
本人查阅了anki批量制卡的工具，很不幸比较少，虽然有一款名为[Word Query](https://ankiweb.net/shared/info/775418273)的比较出色的工具可以辅助提高学习者批量制作单词卡的效率，但因为每个人的需求不同，对anki模板的选择也有所不同，故尚未有功能比较完备的批量制卡工具。

在此前提下，anki_cloze_maker主要针对批量制作带填空的anki卡片的问题，通过关键词挖空生成符合[anki手册](http://ankichina.net/Index/ankishouce)的填空文本，能够顺利导入anki中供使用者学习。

## 填空格式
按anki手册的规范生成的填空文本格式如下：

>秋天的{{c1::山野}}，到处是火红火红的{{c2::枫叶}}，朝霞初照，像落在山腰的{{c3::红云彩}}；晚霞辉映，像一团团玛瑙。

即对文本的关键词添加{{c[索引]::关键词}}的填空符，可生成填空。

## 文本格式
导入anki_cloze_maker处理的.txt文件必须是utf-8编码格式，不然可能会出现异常。同时anki_cloze_maker输出的.txt文件也为utf-8编码格式。

## 相关概念
在使用anki_cloze_maker前，请务必理解以下概念。

### 新词、关键词、停止词

#### 新词
anki_cloze_maker有时不能很好地分辨新的词汇，故可以通过添加新词的方式使其更好地将词汇从文本中分辨出来。

比如anki_cloze_maker通过解析生成以下填空文本：

>{{c1::中国}}人民解放军。

如果你想让anki_cloze_maker识别出“中国人民解放军”这一整体词汇，而不是“中国”，可以通过添加新词“中国人民解放军”的形式，生成以下填空文本：

>{{c1::中国人民解放军}}。

但要注意的是，新词只是为了anki_cloze_maker可以更好地分辨出程序所不能认知的词汇，但不一定对它生成填空符。
新词定义在新词库[new_word.txt](https://github.com/bmxbmx3/anki_cloze_maker/blob/master/res/new_words.txt)中。

#### 关键词
anki_cloze_maker只对关键词生成填空符，关键词包括jieba的tf-idf算法按权重生成的关键词，及自定义的关键词。如果你对anki_cloze_maker生成的填空文本不满意，可以自定义作为填空的关键词。

比如anki_cloze_maker通过解析生成以下填空文本：

>{{c1::中国}}有着上下五千年的历史，是{{c2::世界}}上历史最悠久的国家。

如果你想将“五千年”这一词作特殊标记的关键词，anki_cloze_maker会生成如下文本：

>{{c1::中国}}有着上下{{c2::五千年}}的历史，是{{c3::世界}}上历史最悠久的国家。

只要自定义了关键词，anki_cloze_maker就可以将它自动同步到新词库中，并从停止词库中删除。
关键词定义在关键词库[new_word.txt](https://github.com/bmxbmx3/anki_cloze_maker/blob/master/res/new_words.txt)中。

### 空格率