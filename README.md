# anki_cloze_maker
根据结巴中文分词的tf-idf算法，及自定义的关键词，对.txt文件批量生成anki填空符。

## 目录
  * [运行环境](#运行环境)
  * [感谢](#感谢)
    + [结巴中文分词](#结巴中文分词)
    + [中文停止词库来源](#中文停止词库来源)
    + [anki批量填空与增量阅读](#anki批量填空与增量阅读)
  * [启发](#启发)
  * [解决问题](#解决问题)
  * [填空符](#填空符)
  * [文本编码](#文本编码)
    + [小技巧](#小技巧)
  * [相关概念](#相关概念)
    + [词的种类](#词的种类)
    + [文件格式](#文件格式)
    + [空格](#空格)
  * [使用说明](#使用说明)
    + [检查python3安装环境](#检查python3安装环境)
    + [检查所缺依赖库](#检查所缺依赖库)
    + [运行anki_cloze_maker](#运行anki_cloze_maker)
    + [主要命令](#主要命令)
  * [相关算法](#相关算法)
    + [tf-idf文本分析](#tf-idf文本分析)
    + [词库中文字符排序](#词库中文字符排序)
  * [维护](#维护)

## 运行环境
1. 跨平台（Python可运行的环境）
2. Python3

## 感谢
### 结巴中文分词
github用户“linhx13”等人所作的[结巴中文分词](https://github.com/fxsjy/jieba)，anki_cloze_maker使用它的tf-idf算法提取关键词，再结合自定义的关键词，对其生成填空符。
### 中文停止词库来源
github用户“yinzm”所作的[常用的中文停用词表](https://github.com/yinzm/ChineseStopWords#中文常用停用词表)，anki_cloze_maker使用它过滤不必要的词，不会生成填空符。
### anki批量填空与增量阅读
知乎用户“余时行”的介绍[使用anki批量填空与增量阅读](https://zhuanlan.zhihu.com/p/23838271)的文章，由此诞生制作anki_cloze_maker的灵感。

## 启发
本人阅读supermemo的[The 20 rules of formulating knowledge in learning](http://super-memory.com/articles/20rules.htm)（中文版请见[这篇文章](https://www.jianshu.com/p/163462164a5b)），有所启发，特别是第五条Cloze deletion is easy and effective讲述填空的部分，深有感触。第五条揭示了对一段文本可以采用关键词挖空的形式多次记忆，形式如下：

>问：秋天的...(填空)，到处是火红火红的枫叶，朝霞初照，像落在山腰的红云彩；晚霞辉映，像一团团玛瑙。

>答：山野

>问：秋天的山野，到处是火红火红的...(填空)，朝霞初照，像落在山腰的红云彩；晚霞辉映，像一团团玛瑙。

>答：枫叶

>问：秋天的山野，到处是火红火红的枫叶，朝霞初照，像落在山腰的...(填空)；晚霞辉映，像一团团玛瑙。

>答：红云彩

>问：秋天的...(填空)，到处是火红火红的...(填空)，朝霞初照，像落在山腰的...(填空)；晚霞辉映，像一团团玛瑙。

>答：山野 枫叶 红云彩

可以看到，通过对一句话分批记忆每个填空，最后再一次性记忆所有的填空，可以大大提高学习效率。

当然，你也可以根据第4条信息最小化原则**将知识点进一步拆分**，采用自问自答的方式强化记忆，但由于自己设置问题需要花费一定的功夫，故不建议这么做。如果没有多少时间，最好采用填空的形式辅助记忆。

## 解决问题
本人查阅了anki批量制卡的工具，很不幸比较少，虽然有一款名为[Word Query](https://ankiweb.net/shared/info/775418273)的比较出色的工具可以辅助提高学习者批量制作单词卡的效率，但因为每个人的需求不同，对anki模板的选择也有所不同，故尚未有功能比较完备的批量制卡工具。

在此前提下，anki_cloze_maker主要针对批量制作带填空的anki卡片的问题，通过对系统自动查找到的关键词挖空，生成符合[anki手册](http://ankichina.net/Index/ankishouce)的填空文本，能够顺利导入anki中供使用者学习。

比如对以下给定文本：

>白云的遐想在天空中绽放，沙土的梦想在大地上开放，小溪的臆想在大海处流放。

anki_cloze_maker通过算法计算出一些词的重要程度，如从这句话中提取出按权重大小（重要程度）从前往后排列的关键词：天空、大地、大海。并结合自定义的关键词，如：白云、沙土。将这二者作为关键词整体，对这段文本自动加入填空符生成如下文本：

>{{c1::白云}}的遐想在{{c2::天空}}中绽放，{{c3::沙土}}的梦想在{{c4::大地}}上开放，小溪的臆想在{{c5::大海}}处流放。

## 填空符
按anki手册的规范生成的填空文本格式如下：

>秋天的{{c1::山野}}，到处是火红火红的{{c2::枫叶}}，朝霞初照，像落在山腰的{{c3::红云彩}}；晚霞辉映，像一团团玛瑙。

即对文本的关键词添加{{c[索引]::[关键词]}}的填空符，可生成填空。

如果你对anki填空模板及填空符的使用依然不懂，可以看[这篇文章](https://zhuanlan.zhihu.com/p/21483899)。

## 文本编码
导入anki_cloze_maker处理的.txt文件必须是utf-8编码格式，不然可能会出现异常。同时anki_cloze_maker输出的.txt文件也为utf-8编码格式。
### 小技巧
如果你想让windows的记事本默认的文本编码是utf-8，可以参考[这篇文章](https://blog.csdn.net/current_person/article/details/52846752)。

## 相关概念
在使用anki_cloze_maker前，请务必理解以下概念。

### 词的种类

#### 新词（new word）
anki_cloze_maker有时不能很好地分辨新的词汇，故可以通过添加新词的方式使其更好地将词汇从文本中分辨出来，也即新词是为了更好地分词。

比如anki_cloze_maker通过解析生成以下填空文本：

>{{c1::中国}}人民解放军。

如果你想让anki_cloze_maker识别出“中国人民解放军”这一整体词汇，而不是“中国”，可以通过添加新词“中国人民解放军”的形式，生成以下填空文本：

>{{c1::中国人民解放军}}。

但要注意的是，新词只是为了anki_cloze_maker可以更好地分辨出其所不能认知的词，但不一定对它生成填空符。

自定义的新词存在新词库[new_words.txt](https://github.com/bmxbmx3/anki_cloze_maker/blob/master/anki_cloze_maker/res/new_words.txt)中。

#### 关键词(tag word)
anki_cloze_maker只对关键词生成填空符，关键词包括jieba的tf-idf算法按权重生成的关键词，及自定义的关键词。如果你对anki_cloze_maker生成的填空文本不满意，可以自定义作为填空的关键词。

比如anki_cloze_maker通过解析生成以下填空文本：

>{{c1::中国}}有着上下五千年的历史，是{{c2::世界}}上历史最悠久的国家。

如果你想将“五千年”这一词作特殊标记的关键词，anki_cloze_maker会生成如下文本：

>{{c1::中国}}有着上下{{c2::五千年}}的历史，是{{c3::世界}}上历史最悠久的国家。

只要自定义了关键词，anki_cloze_maker就可以将它自动同步到新词库中，并从停止词库中删除。

自定义的关键主要存在关键词库[tag_words.txt](https://github.com/bmxbmx3/anki_cloze_maker/blob/master/anki_cloze_maker/res/tag_words.txt)中。

#### 停止词(stop word)
anki_cloze_maker不对停止词生成填空符，jieba的tf-idf算法可以将之从文本中过滤。

比如anki_cloze_maker通过解析生成以下填空文本：

>看着{{c1::人潮}}涌动，我的{{c2::泪}}不禁流了下来。

如果你不想对“泪”一词生成填空符，可以将其标记为停止词，anki_cloze_maker会生成如下文本：

>看着{{c1::人潮}}涌动，我的泪不禁流了下来。

只要自定义了停止词，anki_cloze_maker就可以将它添加到停止词库中，并从新词库和关键词库中删除。

自定义的停止词存在停止词库[stop_words.txt](https://github.com/bmxbmx3/anki_cloze_maker/blob/master/anki_cloze_maker/res/stop_words.txt)中。

#### 新词、关键词、停止词间的关系
+ 新词不一定作为填空。
+ 关键词一定作为填空。
+ 停止词不能作为填空。
+ 关键词是新词的子集。
+ 关键词包括自定义的关键词和jieba找寻的关键词。
+ 停止词与关键词、新词互斥。

为了更清晰地解释新词、关键词和停止词这三者的关系，特意用图来表示：

 <div align="left">
 <img src="https://github.com/bmxbmx3/anki_cloze_maker/blob/master/anki_cloze_maker/res/%E8%AF%8D%E7%9A%84%E5%85%B3%E7%B3%BB.png" width="60%"/>
</div>

### 文件格式
#### 词库.txt文件格式
自定义的新词、关键词、停止词分别放入对应.txt文件的词库中，词库的.txt文件的基本格式为每行一个词，anki_cloze_maker对词库按集合的方式读写，保证每个词库里不会出现重复的词，如果你在自定义的词库中添加无用的空行或重复的词，anki_cloze_maker会将词库重新整理为每行不重复的词的集合，并删去空行。
#### 导入的待填空的.txt文件格式
anki_cloze_maker按照anki手册的规范，将每一行文本当作单独的卡片，对其分析并添加填空符，故导入的待填空的.txt文件的文本排版应注意这一点。

### 空格
#### 每定长字符数
每定长字符数是指一定量的多少个字符作为一个字符数的单位，用来计算空格率。

#### 每定长字符数中所含的空格数
每定长字符数中所含的空格数是指一个字符数的单位下，所含有的空格个数，用来计算空格率。

#### 有效字符数
有效字符数是指一段文本除去标点符号、空格等对记忆无用的字符之外的字符总数，用来计算空格率。

比如以下文本：

>小明问我，WTO 是什么意思？

这段文本的字符数为15，但有效字符数为12（除去了空格、标点符号）。

#### 空格率
空格率是指空格在一段文本中出现的百分比。

计算公式为：空格率=每定长字符数中所含的空格数/每定长字符数。

#### 空格数
空格数是指一段文本按空格率计算所得的空格总数。

计算公式为：空格数（对结果向下取整）=有效字符数×空格率。

如果计算所得空格数为0，系统会自动将空格数置为1。

#### 小技巧
如果你觉得系统为你找到的关键词不够精确，或者数量偏少，可能是你的空格率设置的有点低的缘故，可以通过提高空格率的方法，让系统更为精确地找出你所需要用作填空的关键词。


## 使用说明
### 检查python3安装环境
anki_cloze_maker只是一个python脚本文件,为了易用性和可修改性并没有打包，故在这之前需要检查你的电脑[是否已配置好python3的环境](https://blog.csdn.net/gaoxiaoba/article/details/52385135)，同时检查[系统变量是否有python的安装路径](https://blog.csdn.net/CatStarXcode/article/details/79715530)。如已有python3，可用python的解释器运行本程序。
### 检查所缺依赖库
运行前通过[pip安装一些必要的相关依赖库](https://blog.csdn.net/u012386109/article/details/79778153)，最主要的是jieba、request这两个库，可以参考[python一键安装全部依赖包](https://www.jianshu.com/p/b00277344528)。
### 运行anki_cloze_maker
#### 启动
从github下载anki_cloze_maker项目的文件后，从里面**取出[anki_cloze_maker](https://github.com/bmxbmx3/anki_cloze_maker/tree/master/anki_cloze_maker)文件夹**并放在你自定的文件夹下，即如图：

 <div align="left">
 <img src="https://github.com/bmxbmx3/anki_cloze_maker/blob/master/anki_cloze_maker/res/anki_cloze_maker%E6%96%87%E4%BB%B6%E5%A4%B9%E4%BD%8D%E7%BD%AE.png" width="60%"/>
</div>

打开命令提示符，运行`cd [你自定的文件夹的路径]/anki_cloze_maker/code`命令将当前目录转至anki_cloze_maker的code文件夹下，运行`python main.py`命令启动本程序。
#### 引导页
如果一切顺利，你可以看到以下的引导页：

```
请选择以下操作[按序号]：
1.建立填空
2.自定义空格率
3.自定义新词/关键词/停止词
4.设置 anki 填空符索引
5.更新本地停止词库
6.自定义 anki_cloze_maker 文件根目录
7.对填空符的操作
8.结束程序运行
```

其中，如[按序号]这样的包含在中括号的内容是对你该如何操作所作的提示，按此操作即可。

序号1是你最想要的对文本添加填空符的操作，序号2-7是对anki_cloze_maker的相关设置，序号8为退出程序运行。

注意，**首次运行**进入引导页后，一定要选择**序号6**的操作更新anki_cloze_maker包所在的根目录，防止后续脚本运行时出现异常，对根目录的解释请见“操作说明”第6条。

#### 对引导页的操作说明
##### 1. 建立填空
这里导入你想添加填空的.txt文件，按照提示输出添加填空后的.txt文件即可。
###### 注意
+ 输入的源文件的路径必须存在，且结尾一定加.txt后缀名，格式为“[源文件存放的文件夹的路径]/[源文件名].txt”。如：

>D:/文件/源文件.txt

+ 输出的目标文件的路径的结尾一定加.txt后缀名，格式为“[目标文件存放的文件夹的路径]/[目标文件名].txt”。如：

>D:/文件/目标文件.txt
##### 2. 自定义空格率
参阅“相关概念”的“空格”一栏，按照提示设置空格率。
##### 3. 自定义新词/关键词/停止词
按照提示设置你自定的新词、关键词、停止词，并加入到相应的库中。
##### 4. 设置anki填空符索引
anki_cloze_maker默认添加填空符的形式为{{c[索引]::[关键词]}}，如以下：

>我看到远方连绵起伏的{{c1::雪山}}，在{{c2::太阳}}的映照下泛着一层耀眼的{{c3::银光}。

这里的索引为1、2、3。

这样做的好处是你可以对同一段文本分批次挖空记忆（见最开始的“启发”一栏），从而减小你的记忆负担。

当然如果你想让一段文本一次性出现多个空，就可让anki_cloze_maker将填空符的形式变为{{c1::[关键词]}}的形式，即取消索引，如以下：

>我看到远方连绵起伏的{{c1::雪山}}，在{{c1::太阳}}的映照下泛着一层耀眼的{{c1::银光}。

按照提示设置填空符的索引即可。
##### 5. 更新本地停止词库
如果你想恢复默认的停止词库，选择此选项按提示运行即可。
##### 6. 自定义anki_cloze_maker文件根目录（特别关注）
anki_cloze_maker文件根目录即anki_cloze_maker所在的文件路径。

根目录格式为：
>[你自定的文件夹的绝对路径]/anki_cloze_maker
##### 7. 对填空符的操作
进入这个序号的操作后，你会看到系统显示以下内容：
```
请选择对填空符相应的操作[按序号]：
1.对填空符添加索引
2.对填空符去除索引
3.去除填空符
4.由填空符导入关键词
```
这些选项都是针对填空符的格式修改，下面以具体例子介绍各选项的作用。
###### 1. 对填空符添加索引
将：
>{{c1::春天}}、{{c1::夏天}}、{{c1::秋天}}、{{c1::冬天}}。

变为：
>{{c1::春天}}、{{c2::夏天}}、{{c3::秋天}}、{{c4::冬天}}。

###### 2. 对填空符去除索引
将：
>{{c1::春天}}、{{c2::夏天}}、{{c3::秋天}}、{{c4::冬天}}。

变为：
>{{c1::春天}}、{{c1::夏天}}、{{c1::秋天}}、{{c1::冬天}}。

###### 3. 去除填空符
不论有索引：
>{{c1::春天}}、{{c2::夏天}}、{{c3::秋天}}、{{c4::冬天}}。

还是没有索引：
>{{c1::春天}}、{{c1::夏天}}、{{c1::秋天}}、{{c1::冬天}}。

都变为：
>春天、夏天、秋天、冬天。
###### 4. 由填空符导入关键词
不论有索引：
>{{c1::春天}}、{{c2::夏天}}、{{c3::秋天}}、{{c4::冬天}}。

还是没有索引：
>{{c1::春天}}、{{c1::夏天}}、{{c1::秋天}}、{{c1::冬天}}。

都可以从填空符中提取出里面的关键词“春天”、“夏天”、“秋天”、“冬天”，并保存到关键词库中，由词库间的关系对新词库和停止词库顺便同步。
### 主要命令
设置新词/关键词/停止词的对应指令为new/tag/stop，主要在引导页的第3个操作选项“自定义新词/关键词/停止词”中运行，当然你也可以在第1个操作选项“建立填空”的操作过程中碰到这些命令。

命令有两种用法：1、指令+词；2、指令。

#### 1. 指令+词

命令的形式为`new/tag/stop [多个词以空格分开]`。

比如你输入：

`new 美丽 可爱`

就可以对应添加“美丽”和“可爱”的新词。

#### 2. 指令
 
命令的形式为`new/tag/stop`。

注意与“指令+词”相区分的是这里的指令后没有词。

这种命令主要用于词库之间的同步，比如你如果觉得用“指令+词”添加词的方式太过麻烦，可以在res文件夹下的new_words.txt（新词库）、tag_words.txt（关键词库）、stop_words.txt（停止词库）中手动修改（添加/删除）你所需的词，然后在引导页选择第3个操作选项“自定义新词/关键词/停止词”，输入`new/tag/stop`指令，就可按新词、关键词、停止词间的关系进行词库之间的同步。

比如你先在tag_words.txt中按词库.txt文件格式自定义所需的关键词，然后在引导页序号3下的操作输入：

`tag`

就可以按三个词库间的关系，将关键词库的词导入到新词库中，同时从停止词库中删除。
##### 注意
一定要手动修改完某个词库后，紧接着输入相应指令以同步。千万不能同时修改两个或多个词库，然后再输入指令以完成词库间的同步，这样会使词库之间的关系出现意想不到的的问题。

比如你想手动修改关键词库和停止词库对应的tag_words.txt和stop_words.txt文件，请按如下操作顺序进行：

>手动修改tag_words.txt->引导页序号3的操作下输入指令`tag`->手动修改stop_words.txt->引导页序号3的操作下输入指令`stop`

而不是这样的顺序：

>手动修改tag_words.txt、stop_words.txt->引导页序号3的操作下输入指令`tag`->引导页序号3的操作下输入指令`stop`
## 相关算法
### tf-idf文本分析
anki_cloze_maker借助结巴中文分词的tf-idf算法对文本进行分析后，按权重提取关键词并添加填空符，如果你对tf-idf算法及文本分析比较感兴趣，可以参阅油管主播“开发者学堂”所作的[python文本数据分析系列视频](https://www.youtube.com/watch?v=Xs3hFjGICwg&list=PLGmd9-PCMLhY4tBzO_lCWUUYnF2GmVj1o)。
### 词库中文字符排序
三个词库的各个词是按中文的拼音和笔画从前往后排序，以方便你快速找到自己所需修改的关键词，如果你对这种中文排序如何实现比较感兴趣，可以参考[这篇文章](https://www.jianshu.com/p/715c21c90919)。

## 维护
本人接触anki有一定时间，特别喜欢它的卡片记忆方式，为了方便自己制卡写了这个程序，现将它开源出来，一是考虑到自己由于工作的原因维护此项目比较困难，二是希望有同是喜欢anki的人可以共同完善这个项目，如果你有这个兴趣，或者发现某些错误，都可以在issue里提出来，我和乐意与你一起维护anki_cloze_maker这个开源项目。
