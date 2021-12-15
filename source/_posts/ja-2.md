---
title: 日本高中课程《漢文》学习笔记
date: 2021-08-16 01:12:41
mathjax: true
category:
    - 日语
---

这门课大致就是讲日本人是如何阅读中文文言文的。学这个纯属是觉得日语这门语言真的很有意思，借鉴中文的文字，却采用了和中文几乎完全不一样的语法。另一方面也是因为好奇中国的典籍传到日本是如何被翻译的。

虽然自己现代日语都还没学明白，但作为随缘学习人，不如再开新坑。

<!-- more -->

# 基本语序

首先我们需要知道，汉语之中的语序是“主谓宾”，而日语却是“主宾谓”。

所以说日本人在阅读文言文的时候，第一件事就是要把各种词语的顺序调节成日本人能够用日语思维理解的样子。这个时候，日本人发明了**返点（返り点）**和**送假名（送り仮名）**，将其标注在文言文原文旁，用于辅助阅读。

## 送假名

日本人阅读文言文的时候，为了标记出宾语（日本一般称为“目的语”）的位置，常常在表示宾语的汉字右下侧标记「ヲ／ニ／ト」三个假名之一，这就是送假名。这三个假名也是现代日语之中相当重要的三个助词，其表明了三种不同的语法关系。比如说（按理应该使用竖排文字，但是考虑到排版问题，就使用横排文字了。角标位置相应逆时针旋转九十度）：

$$
\begin{aligned}
& \text{陳康粛公善}_{\text{レ}}^{\text{クス}} \text{射}^{\text{ヲ}}。 \\
& \text{項梁乃}^{\text{チ}}\text{教}^{\text{フ}}_{\text{一}}\text{籍}^{\text{ニ}}\text{兵法}^{\text{ヲ}}_{\text{二}}。　\\
\end{aligned}
$$

这里“射”“籍”以及“兵法”右上方（由于改为横排，故旋转到右上方）标注的「ヲ／ニ／ト」就是送假名了，其作用就是标注出宾语的位置。

除去这三个假名，还有一些送假名的目的是将汉语汉字添加上黏性词尾转化为日语词。比如说这里的“教”标注了「フ」假名，实际上代表了「教ふ」这个日语词（注意这里使用古代假名用法而非现代用法）。

## 返点

返点指的是用于提示读者调换文字阅读顺序的提示文字，常常标记在汉字左下角（横排的时候位于右下角）。返点常用的三种为「レ点」「一二点」「上下点」，写法就是汉字左下角的「レ」假名、汉字数字「一二三」和汉字「上下」。

「レ点」的作用是**标记此处返回一个文字**，也就是说位于「レ点」两侧的汉字实际的阅读顺序是反过来的。比如说此句：

$$
\text{行}^{\text{ク}}_{\text{レ}}\text{海}^{\text{ニ}}。
$$

这里「レ点」表明“行”和“海”两个汉字的阅读顺序恰好和印刷顺序是相反的，也就是先读“海”，再读“行”。

如果返回汉字的数量超过一个，则使用「一二点」标记。阅读顺序是从标有「一」的位置跳转到标有「二」的位置。比如说此句：

$$
\text{思}^{\text{フ}}_{\text{二}}\text{君}^{\text{ノ}}\text{事}^{\text{ヲ}}_{\text{一}}。
$$

此时的阅读顺序是首先跳过“思”，先阅读“君”和“事”，之后跳转去阅读“思”。

「上下点」用于标记嵌套的跳转。在两个「一二点」嵌套的时候，为了消歧义，将外层的「一二点」变为「上下点」。比如说此句：

$$
\text{有}^{\text{リ}}_{\text{下}} \text{観}^{\text{ル}}_{\text{二}} {}^{\text{ばい}}\text{梅} {}^{\text{か}}\text{花}^{\text{ヲ}}_{\text{一}} \text{者}_{\text{上}}。
$$

**注解：**这里在右上方（横排的时候在左上方）标注的平假名是这个汉字的注音。

此时的阅读顺序是从“梅花”开始，从“花”跳转到“観”，之后向下阅读到“者”，再跳转到“有”。

如果涉及到更为深层的跳转嵌套，可能会使用到天干地支。但对于大部分的转读，这三类就已经够用了。

---

综合这三种返点的话，可以看这一个例句（只保留返点）：

$$
\text{無}_{\text{下}} \text{不}_{\text{レ}} \text{知}_{\text{レ}} \text{愛}_{\text{二}} \text{其} \text{親}_{\text{一}} \text{者}_{\text{上}}。
$$

这一句话首先阅读“其親”，之后根据「一二点」跳转到“愛”，遇到「レ点」则逐个倒着读“知不”，之后向下读到“者”，最后根据「上下点」读到“無”。所以最后的汉字阅读顺序是“其親愛知不者無”，当然这和真正的日语还有距离，但至少汉字顺序已经符合日语思维了，之后就是需要利用送假名填充各种助词和词尾。

## 其他的一些概念

首先需要介绍**置字（置き字）**，其含义是在转读为日语的时候不需要读出的汉字。其不会直接在文章中标出来，基本靠读者自身的文言能力来判定。比如说：

$$
\text{念} \text{莫}_{\text{下}} \text{可}_{\text{レ}} \text{使}_{\text{レ}} \text{用}_{\text{二}} \text{於} \text{秦}_{\text{一}} \text{者}_{\text{上}}。
$$

这里的“於”实际上是不会读出来的，所以说阅读顺序是“念秦用使可者莫”，并不包含汉字“於”。

置字常常是古汉语之中常见的一些助词，比如说“而”“於”“乎”“矣”“焉”之类的。

另外，有的时候「レ点」「一二点」或者「レ点」「上下点」会出现在同一个汉字的左下角，这个时候就会发生**合写**。这种合写形式的返点是有专门的 Unicode 字符的，但是这里无法打出来，所以直接分立写出：

$$
\text{勿}^{\text{カレ}}_{\text{下}} \text{以}^{\text{テ}}_{\text{二}} \text{悪}^{\text{ノ}} \text{小}^{\text{ナルヲ}}_{\text{一}} \text{為}^{\text{ス}}_{\text{上レ}} \text{之}^{\text{ヲ}}。
$$

这个时候「レ点」「上下点」虽然合写，但发挥各自作用。

此外，有些时候文言之中有部分双字或者四字熟语不能拆分理解，这些熟语的汉字之间会用短横连接，表示作为整体处理（这里由于短横渲染问题，使用左右括号标志）。比如：

$$
\text{欲}^{\text{ス}}_{\text{三}} (\text{学}_{\text{二}} \text{習}^{\text{セント}}) \text{漢} \text{文}^{\text{ヲ}}_{\text{一}}。
$$

# 书下文

书下文（書き下し文）指的是通过返点和送假名，将文言文直接转化成的，符合日语语法习惯但不一定完全通顺的文句。其转写方式是，首先通过返点调转汉字阅读顺序，最后通过送假名填入日语助词和词尾。

比如说这一句：

$$
\text{有}^{\text{リ}}_{\text{下}} \text{観}^{\text{ル}}_{\text{二}} {}^{\text{ばい}}\text{梅} {}^{\text{か}}\text{花}^{\text{ヲ}}_{\text{一}} \text{者}_{\text{上}}。
$$

首先通过返点得到汉字顺序为“梅花観者有”，之后在各个汉字后补上送假名之后就得到这句话的书下文：

$$
\text{梅花を観る者有り。}
$$

不过在转写书下文的时候，要注意三点：

- 注意日语助词和助动词不写作汉字。

- 注意置字不写入书下文。

- 注意再读汉字的转写（具体转写方式见后续）。

比如说这一句：

$$
\text{子} \text{曰}^{\text{ク}} \text{温}^{\text{メテ}}_{\text{レ}} \text{故}^{\text{キヲ}} \text{而} \text{知}^{\text{ラバ}}_{\text{レ}} \text{新}^{\text{シキヲ}} \text{可}^{\text{シ}}_{\text{二}} \text{以}^{\text{テ}} \text{為}^{\text{ル}}_{\text{一レ}} \text{師} \text{矣}。
$$

首先得到汉字的阅读顺序为“子曰故温新知以師為可”，这里“而”和“矣”是置字，不需要写入书下文。之后填充假名得到书下文：

$$
\text{子曰く故きを温めて新しきを知らば以て師為るべし。}
$$

这里注意，「可し（べし）」是一个助动词，不应当写为汉字。

常见的助词包括：

{% note info no-icon %}
の，か，や，より，と，かな，のみ
{% endnote %}

这里，「の」常写作“之”，「のみ」常写作“耳”。

常见的助动词包括：

{% note info no-icon %}
ず，しむ，る，らる，なり，べし，ごとし
{% endnote %}

这里，「べし」常写作“可”，「ごとし」常写作“若”，「なり」常写作“也”。

# 再读汉字

再读汉字指的就是在转写的时候需要读两次的汉字，其往往代表了日语之中的一些固定句式。再读汉字的第一次读的注音用平假名标在汉字右上角（横排的时候在左上方），第二次读的注音用平假名标在汉字左上角（横排的时候在左下方），而第二次的送假名则和返点一样在左下角（横排的时候在右下方），以表区分，这里用空格隔开。

另外注意，**一般而言，再读汉字第一遍读的时候作实词写成汉字，而第二遍读的时候作虚词不写成汉字**。比如说这样的例句：

$$
{}^{\text{まさ}}_{\text{べ}}\text{当}^{\text{ニ}}_{\text{二 シ}} \text{勉} \text{強}^{\text{ス}}_{\text{一}}。
$$

注意到再读汉字“当”读两次，这句话汉字的阅读顺序应该为“当勉强当”，第二遍读“当”的时候不写为汉字。则书下文为：

$$
\text{当に勉強すべし。}
$$

日本高中要求掌握的再读汉字包括：

{% note info no-icon %}
宜，当，未，猶，応，将，盍，須，且
{% endnote %}

这些汉字的读法为（这里平假名标注的是汉字本身的读音，片假名标注的是相应的送假名。而在「」之中标注的，则是紧接在再读汉字第二次读之前的汉字的送假名。「動詞形式」指的是紧接在再读汉字第二次读之前的动词应当采取的变形）：

| 再読漢字 | 読み方 | 意味 | 動詞形式 |
| :-: | :-: | :-: | :-: |
| 未 |　いまダ〜ず　| まだ〜ない | 未然形 |
| 且 & 将 |　まさニ〜「ント」す　| これから〜しようとする | 未然形 |
| 当 & 応 |　まさニ〜べシ　| 当然〜すべきだ | 終止形／ラ変連体形 |
| 須 |　すべかラク〜べシ　| ぜひとも〜する必要がある | 終止形／ラ変連体形 |
| 宜 |　よろシク〜べシ　| 〜するのがよい | 終止形／ラ変連体形 |
| 猶 |　なホ〜ごとシ　| ちょうど〜のようだ | 名詞＋「の」／動詞連体形＋「が」 |
| 盍 |　なんゾ〜ざル　| どうして〜しないのか | 未然形 |

# 使役动词