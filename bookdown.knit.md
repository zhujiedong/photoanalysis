--- 
title: "使用 R 语言分析 LI-6400 和 LI-6800 光合仪的数据"
author: "祝介东<br /> <br /> 北京力高泰科技有限公司"
affiliation: "北京力高泰科技有限公司"
date: "2021-08-28"
documentclass: krantz
bibliography: photoR.bib
biblio-style: apalike
link-citations: yes
colorlinks: yes
lot: yes
lof: yes
site: bookdown::bookdown_site
description: "使用 R 语言分析 LI-6400XT 与 LI-6800 数据"
github-repo: zhujiedong/photoanalysis
graphics: yes
cover-image: images/coverpage.png
---

# 欢迎 {- #welcome}

------



本文纯属个人打发出差漫漫长夜的作品，并非正式资料，并未经过校对等流程，但所用代码皆经过本人和同事的验证，所引文献均真实存在。希望对不熟悉相关知识的童鞋们有所帮助。

<!--chapter:end:index.Rmd-->

\frontmatter

# 前言 {- #frontmatter}

本文内容均来自个人对相关材料的理解，事实上，直到这本书的内容定型时，我的代码水平还处在初级水平，因此，如果你刚接触 R，不要把这本书当作规范，因为成型后我几乎没怎么改过，一是懒，二是忙。文中内容**并未经过权威认证，如有异议，请参考 R 软件及相关软件包的使用手册或相关模型的文献**。本文所使用代码均只做示例代码使用，在做实际分析时，请结合自己的实验数据及模型使用条件做相应的调整。本文内容仅针对
LI-6800 与 LI-6400XT 光合仪数据，尤其是 RACiR\texttrademark 部分，仅针对 LI-6800
进行分析，且目前市面上仅 LI-6800 光合仪可完成此项数据的测量。如您有 LI-6400XT 或 LI-6800 使用操作相关方面的问题请发送至：

[zhujiedong@ecotek.com.cn](zhujiedong@ecotek.com.cn)

如有与本文数据分析相关的内容请发送至我个人邮箱（本文是业余爱好，内容虽与公司仪器相关，但并不是我领薪水的工作内容）：

[zhujiedong@yeah.net](zhujiedong@yeah.net)

以便我们对相关内容做出修正。

当然，如果您有 github 帐号，优先欢迎在 github 提交：

[github地址](https://github.com/zhujiedong/photoanalysis)

gitee 也有同步版本

[gitee 地址](http://zhu_jie_dong.gitee.io/photoanalysis/)

如有其他仪器问题或者仪器购买需求，请使用下面方式与我们联系：

* 北京力高泰科技有限公司 

* 网址：[http://www.ecotek.com.cn](http://www.ecotek.com.cn)

* 电话：010-64093960/66001653

* 电子邮件：[info@ecotek.com.cn](info@ecotek.com.cn)

* 地址：北京市西城区西直门南大街2号成铭大厦A座22F


# 关于作者 {- #author}

祝介东，[北京力高泰科技有限公司](http://www.ecotek.com.cn/) 工程师，本文内容为我在售后服务时所住的酒店所作（遍布国内大多数省份），本为我打发出差住酒店漫漫长夜的业余爱好，**此部分内容并非公司对我的职位所要求的内容，也并非我公司提供的服务内容，因此属于个人作品，仅供参考，我所在公司对此文内容不负任何责任**。文中代码得到了公司技术部同事： 刘美玲、徐粒、焦阳、王昭、张启尧、张云飞等的测试，其中焦阳贡献了 `jiptest` 包中基础荧光参数计算的代码，在此一并感谢。

文中内容如对您有帮助，我很欣慰。但本人水平有限，错误与疏漏之处还请谅解，欢迎讨论，欢迎提出宝贵意见及建议。本人或单位其他售后人员仅有提供相关仪器操作或故障解决的义务，处理数据和分析数据并非我们的本职工作，如有相关需还请多参考本文内容或网上相关资料，不足之处还请谅解。

# 版权 {- #copyright}

**本文旨在对广大 LI-6400XT 和 LI-6800 光合仪的用户提供一个数据分析的参考，本人或所在公司并未从中获取任何利益，内容错误疏漏之处，欢迎指正。本人保留一切权利，禁止一切将本文内容用于商业用途的行为，禁止商业公司使用。**

\cleardoublepage

<!--chapter:end:00-front.Rmd-->

\mainmatter

# R 软件与 Rstudio {#intro}

## R 软件 {#rsoft}

R语言的官方网站是 [R](http://www.r-project.org) ，与R语言有关的网站还有CRAN（镜像），其主站网址是： [CRAN](http://www.cran.r-project.org)。

## Rstudio {#rstudiosoft}

尽管R的功能十分强大，但是其作为一个命令行工具 ^[实际上在Windows系统下安装的时候提供一个十分简陋的GUI。]，在实际使用中尤为不便，因此，一款集成的开发环境十分有必要。[Rstudio](https://www.rstudio.com/products/RStudio/)是一款专门针对R开发的一个集成环境，同时也支持其他多种语言，用户界面十分友好，支持代码高亮，拼写提示，作图展示等功能，因此我们推荐使用Rstudio对我们的数据进行分析。

R 软件和 Rstudio 的安装十分方便，我们按照各自系统的安装方式安装即可，安装后界面及各区功能如下：

\begin{figure}
\includegraphics[width=4.55in]{images/rstudio} \caption{Rstudio 界面及功能}(\#fig:unnamed-chunk-2)
\end{figure}

本文的主要内容并非介绍这些软件的功能，因此，关于软件的操作部分请仔细阅读相关资料，网络上有大量的免费资源及教程，有需要的同学可自行搜索。

>注：R 与 Rstudio 均非我公司产品，而且均免费或者有免费版本，因此请勿邮件或电话索要此两款软件。

\cleardoublepage

<!--chapter:end:01-1-startR.Rmd-->

# 批量处理光合测定数据 {#batch_question}

对于多数人来讲，一个季节用光合仪测量的数据文件至少是两位数的，处理起来非常不方便，针对这个问题，简单写了一个批量读取 LI-6400 和 LI-6800 原始数据的包(因为现有的容易实现的读取 excel 格式的包还不支持 6800 和 6400 这种形式的公式计算)^[特别注意，原始数据可以用文本编辑器打开，但为了方便使用这个软件包，准确输入与行号相关的参数，建议您使用带行号显示的软件，例如 windows 下的 notepad++]，使用非常简单，同时也适合处理未关闭数据文件而导致的无法生成 excel 格式的数据时的问题。

## 安装 {#install_readphoto}

暂时只有我的 github repo 中的版本：


```r
devtools::install_github("zhujiedong/readphoto")
```

## 6400 数据整合{#batch64}

基本参数如下：


```r
library(readphoto)
df64  <- read_bat_6400("./data/6400", header_line = 17, data_start = 27)
```

数据输出如下所示(仅显示前8列数据)：

\begin{table}

\caption{(\#tab:unnamed-chunk-5)LI-6400 批量整合数据}
\centering
\begin{tabular}[t]{llllrrrrr}
\toprule
  & files & Obs & HHMMSS & FTime & EBal. & Photo & Cond & Ci\\
\midrule
1 & aci & 1 & 10:55:14 & 483.0 & 0 & 6.990 & 0.0831 & 251.0\\
4 & aci & 2 & 10:57:35 & 623.5 & 0 & 5.160 & 0.0853 & 192.0\\
7 & aci & 3 & 10:59:55 & 763.5 & 0 & 3.140 & 0.0881 & 136.0\\
10 & aci & 4 & 11:02:26 & 914.5 & 0 & 0.910 & 0.0927 & 81.9\\
13 & aci & 5 & 11:04:46 & 1055.0 & 0 & -0.167 & 0.0966 & 52.7\\
\addlinespace
16 & aci & 6 & 11:07:23 & 1211.5 & 0 & 5.240 & 0.1010 & 305.0\\
19 & aci & 7 & 11:09:43 & 1352.0 & 0 & 6.610 & 0.1040 & 284.0\\
22 & aci & 8 & 11:12:04 & 1492.5 & 0 & 9.280 & 0.1050 & 438.0\\
25 & aci & 9 & 11:14:24 & 1633.0 & 0 & 10.200 & 0.1020 & 616.0\\
28 & aci & 10 & 11:16:44 & 1772.5 & 0 & 10.500 & 0.0943 & 795.0\\
\addlinespace
31 & aci & 11 & 11:19:49 & 1958.0 & 0 & 10.700 & 0.0853 & 970.0\\
34 & aci & 12 & 11:22:09 & 2097.5 & 0 & 11.100 & 0.0812 & 1150.0\\
41 & aq & 2 & 10:12:52 & 737.5 & 0 & 6.450 & 0.0700 & 239.0\\
44 & aq & 3 & 10:15:12 & 878.0 & 0 & 6.450 & 0.0684 & 235.0\\
47 & aq & 4 & 10:17:32 & 1017.5 & 0 & 5.960 & 0.0655 & 241.0\\
\bottomrule
\end{tabular}
\end{table}

如果想另存为 csv 格式：


```r
write.csv(df64, "./combined.csv")
```

header_line 表示你数据表头所在行，data_start 表示你数据起始行，Obs = 1 时所在行，不含表头。这个也要确认好了，不同的测量不能放在一起（当然一般不会出现这种情况，同一台仪器，处理数据当然是希望 aci 和 aci 在一起，lrc 和 lrc 在一起，调查测量和调查测量在一起），不同的测量可能起始行不同，这样就会报错，特别需要注意的是，目前路径写法仅支持 “/” 分隔，不能使用 “\\“ 作为分隔。例如在数据放在 D 盘的 6400 文件夹下，那么写法应为 "d:/6400", 不能为 **"d:\\6400"**, 尽管后者对 R 是合法的，主要是因为我要区分你不同数据来源的文件是哪个，也即下文提到的 `df$files` 列。

其中，数据的来源在表格中第一列，叫做 files，是数据来源的文件名（即你起的名字）,例如本例中你看到的 aci 是我之前数据里面 aci 响应曲线的数据。


这些数据可以用于后文相关的分析中，尤其是像 `fitacis` 这样的函数，因为本质上他们都是符合 `tidyverse` 样式的数据。 


## LI-6800 数据整合{#batch68}

此部分内容保留，但不建议再使用，如果不介意软件包比较大，可以参考：

[图形界面整理数据](https://mp.weixin.qq.com/s?__biz=MzU4ODI3NjkzMg==&mid=2247485449&idx=1&sn=914778c6bc1e288c7cb093da403d920e&chksm=fdde7da2caa9f4b449de6bd4ead60913444c6a6b429e59d6ad80ab5bea10c74e1e0c5da1105d&token=1056052514&lang=zh_CN#rd)

转换为 tidy csv 数据后，可以直接通过简单的代码进行批量处理操作。

~~相比 6400， 6800 参数要少，导入需要两行命令即可：~~


```r
library(readphoto)
df <- read_bat_6800("./data/6800", data_start = 56)
```

~~其中就两个参数，第一个为 LI-6800 **原始数据**所在的文件夹，即文件夹内**包含且仅包含 LI-6800 的原始数据**，不要放其他文件，不要放 excel 数据。第二个参数即为你原始数据中测量数据的起始行，例如本例中使用数据测量值从 56 行开始。~~

~~数据输出如下所示(仅显示前8列数据)：~~

\begin{table}

\caption{(\#tab:unnamed-chunk-8)LI-6800 批量整合数据}
\centering
\begin{tabular}[t]{lrrrlrrr}
\toprule
files & obs & time & elapsed & date & plot & plant & TIME\\
\midrule
aci & 1 & 1513614617 & 0.0 & 20171218 10:30:16 & 1 & 1 & 1513614617\\
aci & 2 & 1513614731 & 114.0 & 20171218 10:32:10 & 1 & 1 & 1513614731\\
aci & 3 & 1513614886 & 269.0 & 20171218 10:34:45 & 1 & 1 & 1513614886\\
aci & 4 & 1513615008 & 391.0 & 20171218 10:36:47 & 1 & 1 & 1513615008\\
aci & 5 & 1513615127 & 510.0 & 20171218 10:38:46 & 1 & 1 & 1513615127\\
\addlinespace
aci & 6 & 1513615287 & 670.5 & 20171218 10:41:27 & 1 & 1 & 1513615287\\
aci & 7 & 1513615410 & 793.0 & 20171218 10:43:29 & 1 & 1 & 1513615410\\
aci & 8 & 1513615566 & 949.0 & 20171218 10:46:05 & 1 & 1 & 1513615566\\
aci & 9 & 1513615701 & 1084.0 & 20171218 10:48:20 & 1 & 1 & 1513615701\\
aci & 10 & 1513615831 & 1214.0 & 20171218 10:50:30 & 1 & 1 & 1513615831\\
\addlinespace
aci & 11 & 1513615940 & 1323.0 & 20171218 10:52:19 & 1 & 1 & 1513615940\\
aci & 12 & 1513616064 & 1447.4 & 20171218 10:54:24 & 1 & 1 & 1513616064\\
lrc & 1 & 1513612721 & 0.0 & 20171218 09:58:40 & 1 & 1 & 1513612721\\
lrc & 2 & 1513612832 & 111.0 & 20171218 10:00:31 & 1 & 1 & 1513612832\\
lrc & 3 & 1513612941 & 220.0 & 20171218 10:02:20 & 1 & 1 & 1513612941\\
\bottomrule
\end{tabular}
\end{table}

~~其他注意事项见 LI-6400 数据整合部分~~

~~另外： 今天添加了使用 regex 读取 LI-6800 原始文件的方法，这个只需要路径即可（2020-3-18），简单测试，有名字重复的警告，是 TIME，不影响大局，我没时间改，过几天有时间了再改：~~


```r
library(readphoto)
df2 <- read_regex68("./data/6800")
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

\begin{table}

\caption{(\#tab:unnamed-chunk-10)LI-6800 regex 方式批量整合数据}
\centering
\begin{tabular}[t]{rrrlrrrr}
\toprule
obs & time & elapsed & date & plot & plant & TIME & E\\
\midrule
1 & 1513614617 & 0.0 & 2017-12-18 10:30:16 & 1 & 1 & 1513614617 & 0.0045726\\
2 & 1513614731 & 114.0 & 2017-12-18 10:32:10 & 1 & 1 & 1513614731 & 0.0047162\\
3 & 1513614886 & 269.0 & 2017-12-18 10:34:45 & 1 & 1 & 1513614886 & 0.0049729\\
4 & 1513615008 & 391.0 & 2017-12-18 10:36:47 & 1 & 1 & 1513615008 & 0.0052632\\
5 & 1513615127 & 510.0 & 2017-12-18 10:38:46 & 1 & 1 & 1513615127 & 0.0053619\\
\addlinespace
6 & 1513615287 & 670.5 & 2017-12-18 10:41:27 & 1 & 1 & 1513615287 & 0.0053909\\
7 & 1513615410 & 793.0 & 2017-12-18 10:43:29 & 1 & 1 & 1513615410 & 0.0052242\\
8 & 1513615566 & 949.0 & 2017-12-18 10:46:05 & 1 & 1 & 1513615566 & 0.0052158\\
9 & 1513615701 & 1084.0 & 2017-12-18 10:48:20 & 1 & 1 & 1513615701 & 0.0053947\\
10 & 1513615831 & 1214.0 & 2017-12-18 10:50:30 & 1 & 1 & 1513615831 & 0.0054908\\
\addlinespace
11 & 1513615940 & 1323.0 & 2017-12-18 10:52:19 & 1 & 1 & 1513615940 & 0.0053860\\
12 & 1513616064 & 1447.4 & 2017-12-18 10:54:24 & 1 & 1 & 1513616064 & 0.0052159\\
1 & 1513612721 & 0.0 & 2017-12-18 09:58:40 & 1 & 1 & 1513612721 & 0.0067439\\
2 & 1513612832 & 111.0 & 2017-12-18 10:00:31 & 1 & 1 & 1513612832 & 0.0065955\\
3 & 1513612941 & 220.0 & 2017-12-18 10:02:20 & 1 & 1 & 1513612941 & 0.0064493\\
\bottomrule
\end{tabular}
\end{table}

## 重计算功能 {#recompute_raw}

如果只是将原始数据批量处理，那么在遇到叶片不能充满叶室的情况时会很麻烦，这里我们提供了重新计算功能，重新计算的参数包括光合速率，蒸腾速率，对水的气孔导度以及胞间二氧化碳浓度，当然计算他们所需要的一些中间数值也做了重计算，只不过多数时候我们用不到，我们仅看这四项。

### LI-6400 数据重计算 {#recompute6400}

参数的重计算函数为 `recomp_6400`, 其参数除了 `read_bat_6400` 所包含的参数外，还有叶面积 S, 以及叶片正反面的气孔比例，默认值分别为 6 和 0.5。


```r
library(readphoto)
x1 <- read_bat_6400("./data/6400")
y1 <- recomp_6400("./data/6400", header_line = 17, data_start = 27, S = 6, K = 0.5)

x1$Photo - y1$Photo
```

```
##  [1] -0.0008873753  0.0026900500 -0.0012036469  0.0003483414  0.0006122641
##  [6] -0.0113872639 -0.0020986076  0.0004962787  0.0188727482 -0.0294595908
## [11] -0.0436611445 -0.0339083408  0.0046772165  0.0036653298  0.0030397988
## [16] -0.0105901673  0.0040624956  0.0017317049 -0.0073252290  0.0054977377
## [21]  0.0039736503  0.0021704065  0.0046772165  0.0036653298  0.0030397988
## [26] -0.0105901673  0.0040624956  0.0017317049 -0.0073252290  0.0054977377
## [31]  0.0039736503  0.0021704065
```

```r
x1$Trmmol - y1$Trmmol
```

```
##  [1] -2.998596e-04  1.407338e-04  3.189451e-05 -4.586467e-04 -3.836822e-04
##  [6]  5.402725e-04 -2.344852e-04 -7.684772e-05  5.979599e-04 -6.534341e-04
## [11] -6.779145e-04  2.469749e-04  3.812201e-04  2.313957e-04  3.508312e-04
## [16] -2.794358e-04 -5.406530e-04  5.230606e-04 -9.183370e-04  7.638850e-04
## [21] -2.578893e-04  2.203045e-04  3.812201e-04  2.313957e-04  3.508312e-04
## [26] -2.794358e-04 -5.406530e-04  5.230606e-04 -9.183370e-04  7.638850e-04
## [31] -2.578893e-04  2.203045e-04
```

```r
x1$Cond - y1$Cond
```

```
##  [1] -1.974217e-04 -3.594216e-04 -3.779119e-04 -3.806675e-04 -3.201411e-04
##  [6] -1.483324e-04 -7.803345e-04 -2.671018e-04  1.028977e-04 -3.966192e-04
## [11] -3.190769e-04 -2.314266e-04 -2.746300e-04  1.094050e-05 -4.584791e-05
## [16] -1.084094e-04 -1.827768e-04 -1.344969e-04 -1.714096e-04 -8.180257e-05
## [21] -4.687906e-05 -1.000424e-04 -2.746300e-04  1.094050e-05 -4.584791e-05
## [26] -1.084094e-04 -1.827768e-04 -1.344969e-04 -1.714096e-04 -8.180257e-05
## [31] -4.687906e-05 -1.000424e-04
```

```r
x1$Ci-y1$Ci
```

```
##  [1]  0.434643936 -0.297820404 -0.308200950 -0.007847373 -0.035490198
##  [6]  0.433706824 -0.416734067 -0.052089770  0.147655545 -0.315797917
## [11] -0.271335987 -0.228968795  0.356519198  0.311487646  0.052196075
## [16]  0.557128947  0.058563406  0.300198435  0.052607786  0.339000061
## [21] -0.252622980 -0.494554616  0.356519198  0.311487646  0.052196075
## [26]  0.557128947  0.058563406  0.300198435  0.052607786  0.339000061
## [31] -0.252622980 -0.494554616
```

```r
# half of original the area
y1 <- recomp_6400("./data/6400",  header_line = 17, data_start = 27, S = 3, K = 0.5)
y1$Photo/x1$Photo
```

```
##  [1] 2.000254 1.998957 2.000767 1.999234 2.007333 2.004346 2.000635 1.999893
##  [9] 1.996299 2.005611 2.008161 2.006110 1.998550 1.998863 1.998980 2.003671
## [17] 1.998391 1.999240 2.003866 1.995584 1.994199 2.010360 1.998550 1.998863
## [25] 1.998980 2.003671 1.998391 1.999240 2.003866 1.995584 1.994199 2.010360
```

```r
# test with random area less than six
area <- 6 - runif(32, 1, 3)
y1 <- recomp_6400("./data/6400",  header_line = 17, data_start = 27, S = area, K = 0.5)
y1$Photo/x1$Photo
```

```
##  [1] 1.271913 1.957425 1.267156 1.345532 1.953322 1.247812 1.675577 1.286947
##  [9] 1.752611 1.376964 1.917707 1.350001 1.735321 1.731820 1.668727 1.286193
## [17] 1.320945 1.257551 1.561785 1.407394 1.478888 1.719351 1.824288 1.314796
## [25] 1.648506 1.354219 1.558566 1.277686 1.354582 1.745864 1.620695 1.789893
```

我们看到各个值之差非常小，因为我们使用的是相同的叶面积，理论上这两次读数的差异应为 0， 但在实际计算过程中，有小数点位数的影响，所以某些值不完全为 0，但该差值足够小。我们将所有的数据叶面积减半后，二者比值也约等于 2.

### LI-6800 数据重计算 {#recompute6800}

#### 原始数据的批量计算 {#raw-batch-68}

此部分内容保留，但不建议继续使用
~~参数的重计算函数为 `recomp_6800`, 其参数除了 `read_bat_6800` 所包含的参数外，还有叶面积 S, 以及叶片正反面的气孔比例，默认值分别为 6 和 0.5。~~


```r
library(readphoto)
x3 <- read_bat_6800("./data/6800")
y3 <- recomp_6800("./data/6800", S = 6, K = 0.5)
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```r
x3$A - y3$A
```

```
##  [1] 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
```

```r
x3$E - y3$E
```

```
##  [1] 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
```

```r
x3$gsw - y3$gsw
```

```
##  [1] -0.01231889 -0.01271539 -0.01330812 -0.01416506 -0.01491902 -0.01586479
##  [7] -0.01566857 -0.01545520 -0.01563183 -0.01537453 -0.01494554 -0.01433677
## [13] -0.02284560 -0.02291318 -0.02268118 -0.02266862 -0.02250392 -0.02213352
## [19] -0.02061493 -0.02006006 -0.01856400 -0.01678649 -0.01591079 -0.01457109
## [25] -0.01322393 -0.02345109 -0.02312573 -0.02227497
```

```r
x3$Ci-y3$Ci
```

```
##  [1]  4.43187567  3.13678828  1.78620310  0.49515242 -0.08437000  3.51420195
##  [7]  3.66611259  5.56360761  6.76351008  7.70784028  8.45263315  9.25926640
## [13]  2.58978666  2.52629775  2.49784796  2.38619050  2.23225682  1.92200639
## [19]  1.29213044  0.92018214  0.76352452  0.55412066  0.20248257 -0.08219604
## [25] -0.26216626  2.36039345  2.40636618  2.54353742
```

```r
# half of original the area
y3 <- recomp_6800("./data/6800", S = 3, K = 0.5)
```

```
## Warning: Missing column names filled in: 'X125' [125]

## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```r
y3$A/x3$A
```

```
##  [1] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
```

```r
# test with random area less than six
area <- 6 - runif(28, 1, 3)
y3 <- recomp_6800("./data/6800", S = area, K = 0.5)
```

```
## Warning: Missing column names filled in: 'X125' [125]

## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```
## Warning: Missing column names filled in: 'X125' [125]
```

```
## Warning: Duplicated column names deduplicated: 'TIME' => 'TIME_1' [69]
```

```r
y3$A/x3$A
```

```
##  [1] 1.234725 1.386181 1.734084 1.401400 1.387123 1.897486 1.301125 1.568791
##  [9] 1.418195 1.766689 1.328404 1.424649 1.260445 1.307652 1.348474 1.845582
## [17] 1.509451 1.610886 1.303266 1.822998 1.374819 1.670735 1.696006 1.262896
## [25] 1.774859 1.616441 1.217518 1.854937
```

~~测量结果相比 6400 的数据，某些值差异略大，我仔细核对过公式，并无问题，可能仅仅是小数点后数据的原因，如果童鞋们发现我这里有错误，可以提交 github 或者邮箱发送给我，便于我改正。~~

#### Excel 格式的重计算 {#excel-recompute-68}

偶然发现了 `XLConnect` 软件包的一个功能（以前知道这个软件包，但忽视了），那就是直接读取 LI-6800 Excel 格式的数据并重计算，我将其写成了函数，放在了我的 `readphoto` 软件包里，软件包的安装：


```r
remotes::install_github("zhujiedong/readphoto")
```

当然，最近连我自己安装 github 的软件包都经常出问题，如果大家同样遇到问题，可以按照下面的方式安装：


```r
remotes::install_git("https://gitee.com/zhu_jie_dong/readphoto")
```

其中：

- path 是 Excel 文件的路径；

- start_row 是数据开始的行号；

- S 为修改的叶面积，默认值为 6，如果叶面积无需更改，则使用默认的 NULL。如果使用 aperture 更改了面积，且叶片能够充满叶室，比方说是 2 cm^2^，该值必须输入一个长度和测量值数量完全一致的向量，例如有 3 个测量值，我们输入 S 的长度则为 3，例如，一共有三个测量值，只有第一个叶片没充满叶室，面积为 1.5，其他的为 2，则输入方式为 `S = c(1.5, 2, 2)`。


我们直接使用下面的例子解释，导入的数据是 6 cm^2^ 的默认面积：


```r
library(readphoto)

df6 <- xlconnect_read("./data/aci-xlc.xlsx")
df6$A

##  [1] 24.7381184 18.1379358 10.8055345  3.0239340 -0.9144044 26.9519572
##  [7] 27.5088717 40.9101889 50.1393342 55.3865984 58.0662751 59.3556428
```

将面积改为 3 cm^2^


```r
df3 <- xlconnect_read("./data/aci-xlc.xlsx", S = rep(3, 12))
df6$A/df3$A

##  [1] 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5
```

模拟 12 个不同的叶面积，均值为 3，方差为 0.1


```r
df_random <- xlconnect_read("./data/aci-xlc.xlsx", S = rnorm(12, 3, 0.1))
df6$A/df_random$A

##  [1] 0.5404604 0.4946930 0.5055978 0.5131122 0.5091636 0.5010179 0.5118623
##  [8] 0.4928284 0.4838221 0.5126210 0.4926683 0.4925639
```

#### 批量处理 LI-6800 的数据 {#batch-6800-xlsx}

为了避免麻烦，不再建议前面 \@ref(recompute6800)的方式进行处理，其实基本的代码处理也很简单，例如下面演示的一些方法，供参考：


```r
library(readphoto)
file_names <-
  list.files(path = "./data/extdata",
             full.names = TRUE,
             pattern = '*.xlsx')

data_list <- lapply(file_names, xlconnect_read)

## 整理好数据为正常格式并导出，例如导出 csv 文件

# 根据需要，若文件夹不存在可创建
#dir.create( "./data/extdata/csvdata")
short_names <-
  list.files(path = "./data/extdata", pattern = '*.xlsx')
short_name <- gsub(".xlsx", "", short_names)
csv_file <-
  paste0("./data/extdata/csvdata", "/", short_name, ".csv")
mapply(x = data_list, file = csv_file, write.csv)



# 若需要修改叶面积，则可以将叶面积重新另存为只有叶面积数据的文件
# 面积数据的文件仅包含S列，且文件名与待修改面积的文件名保持一致


# 批量读取叶面积的数据
# 为方便使用，我将面积存为了 csv 格式
# 根据需要，若文件夹不存在可创建
#dir.create( "./data/extdata/areacsv")
file_area <-
  list.files(path = "./data/extdata/area",
             full.names = TRUE,
             pattern = '*.csv')
area_list <- lapply(file_area, read.csv)

# mapply 使用多个list 读取数据，注意将list命名为参数的名字，
# 以免出现使用位置的方式匹配参数，出现错误
data_list_csv <-
  mapply(
    path = file_names,
    S = area_list,
    xlconnect_read,
    start_row = 17,
    SIMPLIFY = F
  )
short_names_csv <-
  list.files(path = "./data/extdata", pattern = '*.xlsx')
short_name_csv <- gsub(".xlsx", "", short_names_csv)
csv_file_area <-
  paste0("./data/extdata/areacsv", "/", short_name_csv, ".csv")
mapply(x = data_list_csv, file = csv_file_area, write.csv)


##如果要合并数据到一个文件内，可以在合并时，使用文件名作为
# 单独的一列，例如列名都叫做 from_file
# 例如使用我们刚刚修改叶面积的数据为例，使用刚刚导出的csv文件，节省代码


add_col_name <- function(path, file_name){
  df <- read.csv(path)
  df$from_file <- file_name
  df
  }

csv_data_path <-
  list.files(path = "./data/extdata/areacsv",
             full.names = TRUE,
             pattern = '*.csv')

data_combine <-
  mapply(
    path = csv_data_path,
    file_name = short_name_csv,
    add_col_name,
    SIMPLIFY = F
  )

# 或者使用data.table::rbindlist(listOfDataFrames)
# 或者使用 dplyr::bind_rows(listOfDataFrames)

df_combined <- dplyr::bind_rows(data_combine)

# 检验数据
head(df_combined$from_file)
tail(df_combined$from_file)
```


\cleardoublepage

<!--chapter:end:01-2-batch-data.Rmd-->

# CO~2~ 响应曲线的拟合 {#response_fit}

## FvCB 模型 {#fvcb_mod}

在 `plantecophys` 包中使用的模型为 @Farquhar1980A 建立的 C3 植物模型 FvCB，其基于 C3 植物碳反应的三个阶段：

* 核酮糖-1,5-双磷酸羧化酶/加氧酶 (Rubisco)的催化下, 核酮糖-1,5-双磷酸(RuBP)与 CO~2~发生羧化作用, 生成3-磷酸甘油酸(PGA)。

* 在腺苷三磷酸(ATP)和还原型烟酰胺腺嘌呤 二核苷酸磷酸(NADPH)的作用下, PGA被还原成磷 酸丙糖(TP)。每6个TP中有1个输出到细胞液中,
用 于蔗糖或者淀粉的合成。

* 剩下的5个TP 在ATP的作用下再生为 3 个RuBP。一部分再生的 RuBP在Rubisco的催化下被氧化成PGA和2-磷酸乙 醇酸,
2-磷酸乙醇酸在ATP的作用下形成PGA, 并且 释放CO~2~ (光呼吸)。

在光照下, C3 植物净光合速率 (A) 取决于 3 个同时存在的速率: RuBP羧化速率(Vc)、RuBP氧化速率 (或光呼吸速率,
Vo)和线粒体在光照下的呼吸速率 (或明呼吸速率, Rd; 此名为了与暗呼吸速率对应和区分)。RuBP氧化过程中每结合1 mol
O~2~ 就会释放 0.5 mol CO~2~ 。因此, 净光合速率 A 的计算为：

\begin{figure}
\includegraphics[width=1\linewidth]{images/fvcb} \caption{光合速率的不同的限制阶段}(\#fig:fvcb)
\end{figure}

\begin{equation}
A\ =V_{c}\ -0.5V_{o}\ -\ R_{d}
(\#eq:aeta)
\end{equation}

线粒体Rd不同于暗呼吸速率(Rn )。Rn是叶片在 黑暗中的线粒体呼吸速率, 随着光照的增加, 线粒体呼吸速率下降。因此 Rd <
Rn 在黑暗条件下测定的叶 片 CO~2~ 交换速率即 Rn , 但是 Rd 的测定比较困难, 因为光照条件下 Rd 与
Vc、Vo 同时存在。@Hikosaka2006Temperature 总结了几种测定
Rd 的方法，式 \@ref(eq:aeta) 又可表达为：

\begin{equation}
A\ =V_{c}\ (1\ -0.5\alpha)\ R_{d}
(\#eq:achange)
\end{equation}

式 \@ref(eq:achange) 中 $\alpha$ 为氧化速率和羧化速率的比值，由 Rubisco 动力学常数确定：

\begin{equation}
\begin{split}
\alpha & =\frac{V_{O}}{V_{C}}\\
& = \frac{O}{C_c} \times \frac{V_{omax} K_c}{V_{cmax}K_{o}}\\
& = \frac{O}{C_{c}} \times \frac{1}{S_{\frac{c}{o}}}
\end{split}
(\#eq:dynamic)
\end{equation}

式 \@ref(eq:dynamic) 中，Cc 和 O 分别为叶绿体部位 CO~2~ 和 O~2~ 浓度。Cc 和
O 通常以气体摩尔分数 ($\mu mol\text{·}mol^{-1}$ ) 或分压 (Pa) 表示, 但光合过程是在叶绿体的液相基质中发生的,
用分压表示更加恰当。Kc 与 Ko 为 Rubisco 羧化(氧化)的米氏常数, 代表了羧化(氧化)速率达到最大羧化(氧化)速率一半时的
CO~2~ 和 $O_{2}$ 浓度。是 Rubisco 特异性因子, $S_{\frac{c}{o}}$表示 Rubisco
对CO~2~ 和 $O_{2}$ 的偏好程度。

当 A = Rd , 即 RuBP 羧化的 CO~2~ 吸收速率刚好 等于 RuBP 氧化的 CO~2~ 释放速率
(Vc = 2Vo，羧化时 CO~2~ 提供的为 CO ) 时, $\alpha$ = 0.5。此时叶绿体的 CO~2~ 浓度就是叶绿体 CO~2~
光合补偿点, 标记为 $\Gamma^{*}$。即：

\begin{equation}
\Gamma^{*}\ =\ \frac{0.5O}{S_{\frac{c}{o}}}
(\#eq:com)
\end{equation}

由公式 \@ref(eq:dynamic)和公式 \@ref(eq:com)可得：

\begin{equation}
\alpha =\frac{2\Gamma^{*}}{C_{c}}
(\#eq:combine)
\end{equation}

代入公式 \@ref(eq:achange) 得到：

\begin{equation}
A\ =V_{c}\ (1\ -\frac{\Gamma^{*}}{C_{c}})\ R_{d}
(\#eq:combine2)
\end{equation}

在 Cc 浓度很低的时候，RuBP 供应充足 (图 \@ref(fig:fvcb) Rubisco 阶段)，Vc 等于 Rubisco
所能支持的羧化速率 w~c~，：

\begin{equation}
w_{c}\ =\ \frac{V_{cmax\ }C_{c}}{C_{c\ }+\ K_{c\ }(1\ +\ \frac{O}{K_{o}})}
(\#eq:wc)
\end{equation}

随着 Cc 浓度的增加，Rubisco 支持的羧化速率超过了 RuBP 供应速率, Vc 受 RuBP 再生速率的限制（图 \@ref(fig:fvcb)
RuBP 阶段），此时 Vc 由 RuBP 的再生速率限制，而 RuBP 又由电子传递速率（J）决定，故：

\begin{equation}
w_{j}\ =\ \frac{J\ C_{c}}{4C_{c\ }+\ 8\Gamma^{*}}
(\#eq:wj)
\end{equation}

当 Cc 浓度很高，光合磷酸化超过了淀粉和蔗糖的合成速率的时候，Vc 受到 TP 利用速率（Vp）的限制（图 \@ref(fig:fvcb)
TPU 阶段），一般情况下，

\begin{equation}
w_{p\ }=\ \frac{3V_{p}C_{c}}{C_{c}-\Gamma^{*}}
(\#eq:wp)
\end{equation}

最终，C3 植物叶片的光合速率 A 由 w~c~、w~j~、w~p~
的最小者决定（图 \@ref(fig:fvcb) 实现部分），当c > $\Gamma^{*}$时：

\begin{equation}
A=min\{w_{c},w_{j,}w_{p}\}(1-\frac{\Gamma^{*}}{C_{c}})-R_{d}
(\#eq:fvcbfin)
\end{equation}


## CO~2~ 响应曲线测量的注意事项 {#co2_note}

尽管上文对其分段性做了数学上的解释，相比来讲，不是那么通俗易懂，根据 @matthew2018 文章中的内容，我们后面两小节的内容对其进行概述：

### 分段性 {#model_3}

与光响应曲线不同， A/Ci 曲线是分段的，这也增加了其复杂性，在其最初阶段，$CO_2$ 浓度较低，在此阶段， Rubisco 更多的与 $O_2$ 结合，因此，即使是较小浓度的 $CO_2$ 的增加，也会显著提高羧化速率，我们将此阶段称之为 $CO_2$ (@Wullschleger1993Biochemical) 限制或 Rubisco 限制 (@Long2003Gas)。净光合速率 A 与 Ci 在此阶段斜率比较陡峭，实践中往往利用计算该斜率来计算 $V_{cmax}$。

在较高的 $CO_2$ 浓度下，曲线斜率开始便的平缓，作为底物的 $CO_2$ 已经不在是限制因素，随着羧化速率达到最大，RUBP 的量成为了其限制因素，而 RUBP 的再生速率受最大电子传递速率 $J_{max}$ 的限制。此时曲线的弯曲点由 $V_{cmax}$ 限制转变为 $J_{max}$，许多研究中将饱和光下和 $CO_2$ 浓度下测量的光合速率称之为做大光合速率(@heath2005rising)。而另一些研究中将最大光合速率定义为外界 $CO_2$ 时，在饱和光强下达到的最大光合速率(@marshall1980a)。这些术语上的差别需要注意。

在之后，有可能继续观测到磷酸盐限制 RUBP 再生的情况，导致光合速率的下降。因为此时大量的磷酸丙糖与正磷酸盐结合，导致 ATP 合成受限制(@ellsworth2015phosphorus)。这就是 TPU 限制阶段。

### 测量注意事项 {#note_detail}

尽管您的操作是严格按照说明书操作的，但说明书是指仪器的正确操作方式，无法对所有测量都采用相同设置，要获得好的测量结果，有更多的因素需要注意：

使用 LI-6400 或 LI-6800 测量 A/Ci 曲线的过程也就是控制叶室或参比室气体浓度变化的过程，**只要诱导的时间足够**，气孔会在相应设置的环境条件下开到足够大，这样 Ci 会随 Ca 而变化，一般来讲二者的比例为 0.7，但也可能在 0.5~0.7 间变化。

一般来讲，测量参数是在温度为 25 $^{\circ}$C 时获得，但实际测量过程中，因为外界温度过高或过低等无法控制叶室温度到 25 $^{\circ}$C，这其实并非严重的问题，因为这可以通过数学上的方法将计算参数标准化为 25 $^{\circ}$C 时的结果。所以，在测量时只需控制叶室温度稳定即可（通常为 20  $^{\circ}$C ~ 30  $^{\circ}$C 之间）。 此外就是控制恒定的 VPD 及一个饱和光强。另外就是需要注意，开始测量之前，必须在外界环境的 $CO_2$ 浓度下诱导足够长的时间，使光合速率达到稳定，一般需要20 ~ 30 min。对于没有稳定的光保护机制的植物，一般不建议在 50 ppm 或更低的浓度下进行设置，此时饱和光强和建议的温度下，植物没有足够的底物进行光合作用，这样会因为光化学反应的降低发生氧化性损伤。@centritto2003 研究表明，长时间的在 50 ppm 下诱导气孔打开到最大时，可以观测到最大的气孔导度（非标准方式测量）。

对于存在干旱胁迫的测量，由于干旱会导致气孔关闭（@lauteri2014）, 此时没有足够多的 $CO_2$ 进入气孔，此时的测量是没有意义的，可在 50 ppm 诱导 1 h 等待气孔完全打开再快速升高 Ca 的值来进行测量（@centritto2003）。该方法对于 $V_{cmax}$ 不受影响而 $J_{max}$ 降低的情况适用（@Aganchich2009）。但在某些情况下，气孔关闭速度太快，无法完成整个 A/Ci 曲线过程 (Haworth2017)（需要考虑 LI-6800 RACiR）。更重要的是，如果想采用拟合方式求 gm，那么气孔必须完全打开使叶片对 $CO_2$ 吸收的限制降到最低。对于灌溉情况良好的植物或者土壤水分情况比较好的植物，气孔不对高于外界浓度的 Ca 的升高而响应(@haworth2015coordination)，这可能需要更多的测量点或延长测量点的时间间隔来提高曲线的分辨率。另外，测量点的数量也要根据研究而改变，例如重点测量 Vcamx 时，50 ~ 300 ppm 的数据点要多一些，而如果研究对象是土壤磷酸盐对植物生理的限制，那么 1600 ~ 2000 ppm 的数据点要适当增多。

一个更精确的了解植物生理指标的方法是将 A/Ci 曲线改为 A/Cc 曲线，但这需要了解 gm 数据。因为 Cc 通过如下方式计算：

\begin{equation}
C_c = C_i - \frac{A}{gm}
(\#eq:notecc)
\end{equation}

对于 gm 的计算，比较易操作的有几种：采用光合荧光联合测量的方式计算求得。当然也可以采用曲线拟合的方式，或者 @yin2009using 使用的方式，在低氧气体下，采用不同的光照水平求得。

此外，测量气体交换非常重要的误差来源就是气体的扩散，因为测量时，多数时间内外界气体浓度要高于叶室内的气体浓度，那么即使使用密封性非常好的材料，由外界高 $CO_2$ 浓度气体向叶室低 $CO_2$ 浓度气体的扩散无法避免，尤其是在连续长时间测量时该效应尤为明显，因此需要经常更换叶室垫圈。具体可以通过一些方法来校正（@Flexas2007leak，rodeghiero2007major），但如果采用 LI-6800 测量这将不是问题，它采用的叶室增加技术并根据测量的漏气情况对结果自动修正。

\cleardoublepage

<!--chapter:end:02-1-fvcb-theory.Rmd-->

## `plantecophys` 软件包 {#plantecophys}

Remko A. Duursma 在2015年发表了一篇文章 @Duursma2015Plantecophys，`plantecophys` 是其开发的一个R包工具集，用于对叶片气体交换数据进行分析和建模。实现了如下功能：

* CO~2~ 响应曲线 (A-Ci curves) 的拟合、作图及模拟。
* 不同气孔导度模型。 
* 根据 Cowan-Farquhar 的假设估算最适的气孔导度。 
* 耦合气体交换模型的实现。 
* 基于 Ci 模拟 C4 光合。 
* RHtoVPD：常用单位的转换（相对湿度、水汽压亏缺、露点温度）。

各参数的基本用法请参考后文内容，或官方帮助文档:

[plantecophys](https://cran.r-project.org/web/packages/plantecophys/plantecophys.pdf)。

## LI-6400XT CO~2~ 响应曲线的拟合 {#fit6400}

LI-6400XT CO~2~ 响应曲线的拟合需要借助 `plantecophys` 的 `fitaci` 实现，fitaci 函数为根据 FvCB 模型对 A-Ci 曲线的测量数据进行拟合，并估算 J$_{max}$、V$_{cmax}$、R$_{d}$及他们的标准差，并根据
@Medlyn2002Temperature 的方法考虑了温度的影响。

### fitaci 函数介绍 {#fitaci_intro}

`fitaci` 的用法如下^[仅针对 C3 植物]：


```r
fitaci(data, varnames = list(ALEAF = "Photo", 
  Tleaf = "Tleaf", Ci = "Ci", PPFD = "PARi", 
  Rd = "Rd"), Tcorrect = TRUE, Patm = 100, 
  citransition = NULL, quiet = FALSE, 
  startValgrid = TRUE, fitmethod = 
  c("default", "bilinear", "onepoint"), 
  algorithm = "default", fitTPU = FALSE, 
  useRd = FALSE, PPFD = NULL, Tleaf = NULL, 
  alpha = 0.24, theta = 0.85, gmeso = NULL, 
  EaV = 82620.87, EdVC = 0, delsC = 645.1013,
  EaJ = 39676.89, EdVJ = 2e+05, 
  delsJ = 641.3615, GammaStar = NULL, 
  Km = NULL, id = NULL, ...)

## S3 method for class 'acifit'
plot(x, what = c("data", "model", "none"),
     xlim = NULL, ylim = NULL, whichA = c(
       "Ac", "Aj", "Amin", "Ap"), add = FALSE,
pch = 19, addzeroline = TRUE, addlegend = 
  !add, legendbty = "o",transitionpoint = TRUE, 
linecols = c("black", "blue", "red"), lwd = c(1,
2), ...)
```


*主要参数注释：*

* data：需要分析的数据，必须为 data.frame^[具体参考 R 语言相关文档，为 R 语言最常用的数据格式]。
格式。
* varnames：数据的表头，此处函数默认的表头为 LI-6400 的表头，分析 LI-6400 的数据时可以不用填写，直接使用默认的参数即可^[在 R 中，使用参数的值为默认值时可以不用填写该参数，例如使用默认选项分析 LI-6400 数据时，可只填写 data 项，具体参考
R 的相关入门手册]。
* Tcorrect：如果为 TRUE，那么 J$_{max}$、V$_{cmax}$ 的结果为温度校正结果，若 Tcorrect = FALSE，则为测量温度下的结果。
* Patm：为外界大气压。
* citransition：参见详，若提供该选项，则 J$_{max}$、V$_{cmax}$
的区域则分别拟合^[为Rubisco和RuBP限制的Ci转换点，物种间差异较大，可以通过预实验确定]。
* fitmethod：参见详解。
* fitTPU：是否拟合 TPU 限制，默认为 FALSE，参见详解。
* x：对于plot.acifit，x 为fitaci返回的对象，简单理解为 将 fitaci 函数拟合结果赋值给一个变量，此处plot函数实际上为plot.acifit。
* what：利用基础做图工具，默认为对数据和模型进行作图。
* whichA：默认为对所有的光合速率进行作图（Aj=Jmax-limited (蓝色), Ac=Vcmax- limited (红色),
Hyperbolic minimum (黑色)), TPU-limited rate (Ap, 如果模型有计算结果）。

其他参数请参考 FvCB 模型 @Farquhar1980A 或查看 plantecophys 的帮助文档。

#### fitaci函数详解 {#fitaci_detail}

* 默认为非线性拟合，详见 @Duursma2015Plantecophys。
* bilinear 方法使用两次线性拟合方法首先拟合 V$_{cmax}$ 和 R$_{d}$，然后在拟合J$_{max}$，过渡点的选择为对所有数据拟合最适的点，类似 @Gu2010Reliable 的方法。该方法的优势时无论如何，都会返回拟合结果，尤其是非线性拟合失败时使用该方法，但若默认方法失败时，需首先检查是否数据存在问题。两种拟合方法的结果有轻微的差别^[若默认拟合方法失败，数据也无问题，那么是非线性拟合初始值设定的原因]。
* onepoint 参考 @De2016A。
* citransition 使用时，数据将被区分为 V$_{cmax}$ 限制（Ci <
citransition ）区域，以及 J$_{max}$ 限制 (Ci > citransition) 区域。

* fitTPU：如果要计算TPU，要设置 fitTPU = TRUE，并且 fittingmethod
= "bilinear"。但需要注意，当 TPU 被计算时，没有 J$_{max}$ 限制的点的存在是可能的。TPU限制的发生是在A值不随
CO$_{2}$ 的增加而增加时发生的，因此计算 TPU 是否有返回值，取决于测量数据是否有此情况出现。

\cleardoublepage

<!--chapter:end:02-2-plantecophys-intr.Rmd-->

## 使用 `plantecophys` 拟合 LI-6400XT CO~2~ 响应曲线数据 {#plantecophy_use}

### 数据的前处理 {#data6400}

虽然 R 软件支持直接导入 xlsx 的数据，但因为 LI-6400XT 的数据记录文件内有其他空行或 remark 等内容，增加了处理代码的量，**故而推荐将其数据先整理为如表 \@ref(tab:head6400) 样式，并另存为 csv 格式**^[即仅保留测量值，删除其他所有头文件、空行、 remark 等信息]：



\begin{longtable}[t]{rlrrrrrr}
\caption{(\#tab:head6400)推荐 LI-6400 整理后数据样式}\\
\toprule
Obs & HHMMSS & FTime & EBal. & Photo & Cond & Ci & Trmmol\\
\midrule
1 & 15:46:59 & 271.5 & 0 & 14.2848912 & 0.2730691 & 286.39751 & 2.226126\\
2 & 15:48:26 & 358.0 & 0 & 10.6562220 & 0.2826303 & 217.32002 & 2.292845\\
3 & 15:49:54 & 446.0 & 0 & 6.4525814 & 0.2909460 & 150.67623 & 2.361704\\
4 & 15:51:26 & 538.5 & 0 & 1.7971569 & 0.3057164 & 85.82530 & 2.459459\\
5 & 15:52:54 & 626.5 & 0 & -0.6575974 & 0.3150002 & 53.47985 & 2.515992\\
\addlinespace
6 & 15:54:50 & 742.5 & 0 & 15.4296572 & 0.3255415 & 292.56161 & 2.579840\\
\bottomrule
\end{longtable}

### 使用示例 {#fitaci-p}

`plantecophys` 并非 base 的安装包，首次使用需要从 `CRAN` 安装，可以使用图形界面安装，也可以直接用命令行安装^[首次使用安装，更换电脑或者升级 R 软件后，如果没有拷贝 library，也需要运行安装命令]，推荐同时安装依赖。


```r
install.packages("plantecophys", dependencies = TRUE)
```



```r
# 载入 plantecophys 
library("plantecophys")

# 利用read.csv读取数据文件，
# 我的路径为当前工作路径的data文件夹内
aci <- read.csv("./data/aci.csv")

# 防止可能出现的NA值
aci <- subset(aci, Obs > 0)

# 不修改默认参数对数据进行拟合
acifit <- fitaci(aci)
# 查看拟合结果的参数名称，方便导出数据使用
attributes(acifit)
```

```
## $names
##  [1] "df"             "pars"           "nlsfit"         "Tcorrect"      
##  [5] "Photosyn"       "Ci"             "Ci_transition"  "Ci_transition2"
##  [9] "Rd_measured"    "GammaStar"      "Km"             "kminput"       
## [13] "gstarinput"     "fitmethod"      "citransition"   "gmeso"         
## [17] "fitTPU"         "alphag"         "RMSE"           "runorder"      
## 
## $class
## [1] "acifit"
```

```r
# 查看拟合结果
summary(acifit)
```

```
## Result of fitaci.
## 
## Data and predictions:
##            Ci      Ameas     Amodel         Ac        Aj   Ap       Rd VPD
## 5    53.47985 -0.6575974 -0.5146882 -0.3552036  0.000000 1000 0.159449 1.5
## 4    85.82530  1.7971569  1.9292621  2.0888575  5.068534 1000 0.159449 1.5
## 3   150.67623  6.4525814  6.4176037  6.5777528 12.755502 1000 0.159449 1.5
## 2   217.32002 10.6562220 10.5354626 10.6965875 17.519644 1000 0.159449 1.5
## 1   286.39751 14.2848912 14.3365887 14.4993980 20.749310 1000 0.159449 1.5
## 6   292.56161 15.4296572 14.9749157 15.1383702 20.852616 1000 0.159449 1.5
## 7   292.96456 15.7134791 15.0564801 15.2200522 20.831098 1000 0.159449 1.5
## 8   450.64285 22.2659015 23.0115187 23.1975997 25.186939 3000 0.159449 1.5
## 9   622.03873 26.5135040 27.6485003 30.4281393 27.837462 3000 0.159449 1.5
## 10  992.08737 30.3898585 30.6300461 42.3998173 30.797660 3000 0.159449 1.5
## 11 1558.96968 33.6267056 32.6638021 54.9948264 32.828110 3000 0.159449 1.5
## 12 1756.16396 33.3152783 33.0981490 58.5844507 33.261965 3000 0.159449 1.5
##       Tleaf         Cc     PPFD Patm Ci_original
## 5  31.12332   53.47934 1800.490  100    53.47985
## 4  30.99093   85.82723 1800.558  100    85.82530
## 3  30.82872  150.68265 1800.140  100   150.67623
## 2  30.63983  217.33057 1800.524  100   217.32002
## 1  30.46890  286.41186 1800.701  100   286.39751
## 6  31.26338  292.57660 1799.923  100   292.56161
## 7  31.41866  292.97963 1799.975  100   292.96456
## 8  31.54122  450.66588 1799.826  100   450.64285
## 9  31.63493  622.06640 1799.578  100   622.03873
## 10 31.73910  992.11803 1800.055  100   992.08737
## 11 31.86938 1559.00238 1800.022  100  1558.96968
## 12 31.96654 1756.19709 1799.585  100  1756.16396
## 
## Root mean squared error:  1.889701 
## 
## Estimated parameters:
##         Estimate Std. Error
## Vcmax  49.261616  1.5152405
## Jmax  126.620537  2.2816267
## Rd      0.159449  0.4001302
## Note: Vcmax, Jmax are at 25C, Rd is at measurement T.
## 
## Curve was fit using method:  default 
## 
## Parameter settings:
## Patm = 100
##  alpha = 0.24
##  theta = 0.85
##  EaV = 82620.87
##  EdVC = 0
##  delsC = 645.1013
##  EaJ = 39676.89
##  EdVJ = 2e+05
##  delsJ = 641.3615
## 
## Estimated from Tleaf (shown at mean Tleaf):
## GammaStar =  58.61138 
## Km =  1223.279
```

```r
acifit_linear <- fitaci(aci,  fitmethod = "bilinear", quiet = TRUE)
summary(acifit_linear)
```

```
## Result of fitaci.
## 
## Data and predictions:
##            Ci      Ameas     Amodel         Ac        Aj   Ap        Rd VPD
## 5    53.47985 -0.6575974 -0.7389447 -0.3560483  0.000000 1000 0.3828608 1.5
## 4    85.82530  1.7971569  1.7108198  2.0938246  5.138366 1000 0.3828608 1.5
## 3   150.67623  6.4525814  6.2098476  6.5933940 12.931333 1000 0.3828608 1.5
## 2   217.32002 10.6562220 10.3375299 10.7220229 17.761317 1000 0.3828608 1.5
## 1   286.39751 14.2848912 14.1477696 14.5338762 21.035722 1000 0.3828608 1.5
## 6   292.56161 15.4296572 14.7876514 15.1743678 21.139657 1000 0.3828608 1.5
## 7   292.96456 15.7134791 14.8694171 15.2562440 21.117718 1000 0.3828608 1.5
## 8   450.64285 22.2659015 22.8464806 23.2527613 25.533374 3000 0.3828608 1.5
## 9   622.03873 26.5135040 27.8030690 30.5004944 28.220254 3000 0.3828608 1.5
## 10  992.08737 30.3898585 30.8295808 42.5006398 31.221072 3000 0.3828608 1.5
## 11 1558.96968 33.6267056 32.8913778 55.1255986 33.279305 3000 0.3828608 1.5
## 12 1756.16396 33.3152783 33.3316070 58.7237587 33.719013 3000 0.3828608 1.5
##       Tleaf         Cc     PPFD Patm Ci_original
## 5  31.12332   53.47911 1800.490  100    53.47985
## 4  30.99093   85.82701 1800.558  100    85.82530
## 3  30.82872  150.68244 1800.140  100   150.67623
## 2  30.63983  217.33037 1800.524  100   217.32002
## 1  30.46890  286.41167 1800.701  100   286.39751
## 6  31.26338  292.57641 1799.923  100   292.56161
## 7  31.41866  292.97944 1799.975  100   292.96456
## 8  31.54122  450.66572 1799.826  100   450.64285
## 9  31.63493  622.06656 1799.578  100   622.03873
## 10 31.73910  992.11823 1800.055  100   992.08737
## 11 31.86938 1559.00260 1800.022  100  1558.96968
## 12 31.96654 1756.19733 1799.585  100  1756.16396
## 
## Root mean squared error:  2.013045 
## 
## Estimated parameters:
##          Estimate Std. Error
## Vcmax  49.3787547  3.4815555
## Jmax  128.5546403         NA
## Rd      0.3828608  0.4697008
## Note: Vcmax, Jmax are at 25C, Rd is at measurement T.
## 
## Curve was fit using method:  bilinear 
## 
## Parameter settings:
## Patm = 100
##  alpha = 0.24
##  theta = 0.85
##  EaV = 82620.87
##  EdVC = 0
##  delsC = 645.1013
##  EaJ = 39676.89
##  EdVJ = 2e+05
##  delsJ = 641.3615
## 
## Estimated from Tleaf (shown at mean Tleaf):
## GammaStar =  58.61138 
## Km =  1223.279
```

```r
# 仅查看拟合参数, 比较两种拟合参数的差异
coef(acifit_linear)
```

```
##       Vcmax        Jmax          Rd 
##  49.3787547 128.5546403   0.3828608
```

```r
coef(acifit)
```

```
##      Vcmax       Jmax         Rd 
##  49.261616 126.620537   0.159449
```

```r
# 设置作图参数，图形的边距及分为1行两列输出图形
par(mar = c(4.5, 4.5, 2, 2))
par(mfrow = c(1, 2))
# 对两种拟合参数的结果作图，查看模型拟合是否正常
plot(acifit, addlegend = FALSE)
legend(x = 500, y = 10, 
       legend = c(expression(paste(A[c])), 
                expression(paste(A[j])),
                "Limiting rate"),
       lty = c(1, 1, 1),
       col =c("red", "blue", "black") 
         )
mtext(" fitmethod = 'default' ")

plot(acifit_linear, addlegend = FALSE)
legend(x = 500, y = 10, 
       legend = c(expression(paste(A[c])), 
                  expression(paste(A[j])),
                  "Limiting rate"),
       lty = c(1, 1, 1),
       col =c("red", "blue", "black") 
)
mtext("fitmethod = 'bilinear' ")
```

![(\#fig:fitaci6400)光合速率的不同的限制阶段](bookdown_files/figure-latex/fitaci6400-1.pdf) 

如果需要导出数据做他用，直接根据 `attributes` 中看到的名称，选择对应的数据导出即可，如果使用 Rstudio 的话，其自动补全的功能在选择数据上更方便。例如导出预测值和系数分别使用如下方式：


```r
# 将模型拟合结果中df（即计算数据）赋给predictaci，
# 并用write.csv导出
predictaci <- acifit$df
write.csv(acifit$df, file = "acipredict.csv")
write.csv(coef(acifit), file = "coefaci.csv")
```

>需要注意的是，因为非线性拟合需要一个初始值，因此，使用默认方式（非线性拟合）的时候，会存在可能的拟合失败现象，此时可以使用 `fitmethod = "bilinear"`，二者结果略有差别。

#### fitmethod = "onepoint" 介绍 {#onepoint}

@De2016A 发表了关于 one point 方法计算 $V_{cmax}$ 和 $J_{max}$ 方法的文章，在 2017 年 11 月的更新中，plantecophys 增加了响应的 R 软件实现方法， 该方法并非使用一个点计算 $V_{cmax}$ 和 $J_{max}$，而是对数据集中的每一个点的值进行估计，使用的方法为逆向了光合作用方程。输出为对每个原始数据加入了 $V_{cmax}$ 和 $J_{max}$，当然一如既往的可以使用温度校准的方法。**并不建议该方法应用于整个 ACi 曲线的数据，它的假设是在外部环境 CO~2~ 浓度和饱和光下，受到 Rubisco 羧化速率的限制而不是 RUBP 的限制。**

基于上面的描述，他们的模型如下：

\begin{equation}
\hat{V}_{cmax} = (A_{sat} + R_{day}) \frac{C_i + K_m}{C_i - \Gamma^*} 
(\#eq:inverseA)
\end{equation}

其中：K~m~ 为米氏常数，其计算为：

\begin{equation}
K_m = K_c (1 + \frac{O_i}{K_o}) 
(\#eq:km)
\end{equation}

未知参数均由文献中的方法进行计算，具体可参考 @De2016A 的原文,但上述方法的缺陷为还要使用 ACi 曲线来估算 R~day~，因此作者使用了1.5% V~cmax~ 作为 R~day~，因此公式 \@ref(eq:inverseA) 可变换为：

\begin{equation}
\hat{V}_{cmax} = A_{sat} (\frac{C_i + K_m}{C_i - \Gamma^*} - 0.015)
(\#eq:onepoint)
\end{equation}

另一个重要的模型的假设为 J~max~ 与 V~cmax~ 是成比例的， J~max~ 的计算是通过 C~i~ transition point 来实现的，文章中的比值均值为 1.9，范围在 1.68 ~ 2.14 之间。 

### 使用 'onepoint' 单独计算 V~cmax~ 和 J~max~ {#onpoint_fit}

目前我手头没有相应数据，仅有使用 LI-6400 测试 auto log 2 时的一个数据，我们用这个来示范该方法的使用：


```r
one_data <- read.csv("./data/onepoint.csv")
knitr::kable(head(one_data), booktabs = TRUE,
             caption = 'onepoint 使用的数据')
```

\begin{table}

\caption{(\#tab:onepointdata)onepoint 使用的数据}
\centering
\begin{tabular}[t]{rlrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrr}
\toprule
Obs & HHMMSS & FTime & EBal. & Photo & Cond & Ci & Trmmol & VpdL & CTleaf & Area & BLC\_1 & StmRat & BLCond & Tair & Tleaf & TBlk & CO2R & CO2S & H2OR & H2OS & RH\_R & RH\_S & Flow & PARi & PARo & Press & CsMch & HsMch & CsMchSD & HsMchSD & CrMchSD & HrMchSD & StableF & BLCslope & BLCoffst & f\_parin & f\_parout & alphaK & Status & fda & Trans & Tair\_K & Twall\_K & R.W.m2. & Tl.Ta & SVTleaf & h2o\_i & h20diff & CTair & SVTair & CndTotal & vp\_kPa & VpdA & CndCO2 & Ci\_Pa & Ci.Ca & RHsfc & C2sfc & AHs.Cs\\
\midrule
1 & 14:12:43 & 347.0 & 0 & 10.519216 & 0.1369336 & 263.0905 & 1.963046 & 1.445782 & 25.89411 & 6 & 1.42 & 1 & 2.84 & 25.86572 & 25.89411 & 25.86697 & 412.5374 & 398.9832 & 16.98853 & 19.29724 & 50.16200 & 56.97891 & 500.3231 & 1198.761 & 7.497697 & 98.84273 & 2.011996 & -0.3824501 & 0.1618589 & 0.0039255 & 0.1864786 & 0.0030409 & 1.0000000 & -0.2195652 & 2.737392 & 1 & 0 & 0.16 & 111115 & 0.8338718 & 0.0019630 & 299.0441 & 299.0157 & 191.8018 & 1.191025 & 3.353173 & 33.92433 & 14.62709 & 25.87991 & 3.350355 & 0.1306349 & 1.907392 & 1.442964 & 0.0821903 & 26.00458 & 0.6594024 & 57.74196 & 393.9829 & 0.0154169\\
2 & 14:13:04 & 369.0 & 0 & 10.361122 & 0.1357092 & 264.1135 & 1.946022 & 1.445559 & 25.89542 & 6 & 1.42 & 1 & 2.84 & 25.86854 & 25.89542 & 25.86977 & 412.5459 & 399.1890 & 17.01363 & 19.30231 & 50.22723 & 56.98380 & 500.3223 & 1198.748 & 7.486364 & 98.84175 & 2.018435 & -0.3787848 & 0.2174792 & 0.0025482 & 0.2455426 & 0.0025012 & 1.0000000 & -0.2195652 & 2.737392 & 1 & 0 & 0.16 & 111115 & 0.8338705 & 0.0019460 & 299.0454 & 299.0185 & 191.7997 & 1.199722 & 3.353433 & 33.92729 & 14.62498 & 25.88198 & 3.350765 & 0.1295200 & 1.907874 & 1.442891 & 0.0814842 & 26.10544 & 0.6616252 & 57.73418 & 394.2638 & 0.0151724\\
3 & 14:13:25 & 373.0 & 0 & 10.207166 & 0.1342114 & 264.8348 & 1.925734 & 1.445717 & 25.89660 & 6 & 1.42 & 1 & 2.84 & 25.87186 & 25.89660 & 25.87317 & 412.5393 & 399.3762 & 17.03833 & 19.30316 & 50.29006 & 56.97489 & 500.3188 & 1198.737 & 7.489428 & 98.84142 & 2.018435 & -0.3787848 & 0.2174792 & 0.0025482 & 0.2455426 & 0.0025012 & 1.0000000 & -0.2195652 & 2.737392 & 1 & 0 & 0.16 & 111115 & 0.8338647 & 0.0019257 & 299.0466 & 299.0219 & 191.7980 & 1.210137 & 3.353668 & 33.92979 & 14.62663 & 25.88423 & 3.351212 & 0.1281551 & 1.907951 & 1.443261 & 0.0806199 & 26.17665 & 0.6631213 & 57.71117 & 394.5242 & 0.0149311\\
4 & 14:13:41 & 389.5 & 0 & 9.547416 & 0.1281947 & 269.6337 & 1.854272 & 1.454248 & 25.94020 & 6 & 1.42 & 1 & 2.84 & 25.91845 & 25.94020 & 25.92104 & 413.8090 & 401.4666 & 17.12559 & 19.30638 & 50.40359 & 56.82203 & 500.3162 & 1199.245 & 7.449210 & 98.83208 & 2.018435 & -0.3787848 & 0.2174792 & 0.0025482 & 0.2455426 & 0.0025012 & 1.0000000 & -0.2195652 & 2.737392 & 1 & 0 & 0.16 & 111115 & 0.8338604 & 0.0018543 & 299.0902 & 299.0684 & 191.8792 & 1.247158 & 3.362337 & 34.02071 & 14.71433 & 25.92933 & 3.360173 & 0.1226580 & 1.908089 & 1.452084 & 0.0771402 & 26.64846 & 0.6716216 & 57.48324 & 396.9282 & 0.0138266\\
5 & 14:16:16 & 540.0 & 0 & 10.288968 & 0.1376602 & 267.9198 & 2.009217 & 1.471788 & 26.01813 & 6 & 1.42 & 1 & 2.84 & 26.01478 & 26.01813 & 26.02164 & 413.9044 & 400.5999 & 16.92567 & 19.28878 & 49.53554 & 56.44850 & 500.3043 & 1200.785 & 7.497018 & 98.81870 & 2.018435 & -0.3787848 & 0.2174792 & 0.0025482 & 0.2455426 & 0.0025012 & 0.6666667 & -0.2195652 & 2.737392 & 1 & 0 & 0.16 & 111115 & 0.8338405 & 0.0020092 & 299.1681 & 299.1648 & 192.1257 & 1.174669 & 3.377880 & 34.18260 & 14.89382 & 26.01646 & 3.377546 & 0.1312960 & 1.906093 & 1.471453 & 0.0826090 & 26.47549 & 0.6687965 & 57.30143 & 395.7090 & 0.0148991\\
\addlinespace
6 & 14:16:32 & 555.5 & 0 & 10.178603 & 0.1381995 & 269.5898 & 2.016100 & 1.471138 & 26.01657 & 6 & 1.42 & 1 & 2.84 & 26.02212 & 26.01657 & 26.02939 & 413.6639 & 400.4885 & 16.92345 & 19.29468 & 49.49181 & 56.42595 & 500.2979 & 1200.879 & 7.569045 & 98.80596 & 2.018435 & -0.3787848 & 0.2174792 & 0.0025482 & 0.2455426 & 0.0025012 & 0.6666667 & -0.2195652 & 2.737392 & 1 & 0 & 0.16 & 111115 & 0.8338298 & 0.0020161 & 299.1666 & 299.1721 & 192.1406 & 1.172561 & 3.377567 & 34.18384 & 14.88916 & 26.01934 & 3.378122 & 0.1317865 & 1.906429 & 1.471693 & 0.0829197 & 26.63708 & 0.6731525 & 57.32389 & 395.6501 & 0.0147473\\
\bottomrule
\end{tabular}
\end{table}

数据如上所示，为同一个叶片连续记录数据，故所有的光合速率十分接近。

使用方法：


```r
library(plantecophys)

one_data <- subset(one_data, Obs > 0)
one_data$Rd <- 0.5
aci_fit <- fitaci(one_data, fitmethod = "onepoint")
```

\begin{table}

\caption{(\#tab:unnamed-chunk-22)onepoint 法计算的结果}
\centering
\begin{tabular}[t]{rrr}
\toprule
aci\_fit.Photo & aci\_fit.Vcmax & aci\_fit.Jmax\\
\midrule
10.519216 & 47.06893 & 71.73759\\
10.361122 & 46.22091 & 70.51059\\
10.207166 & 45.44617 & 69.35838\\
9.547416 & 41.86217 & 64.24400\\
10.288968 & 45.09335 & 69.36954\\
\addlinespace
10.178603 & 44.37360 & 68.41628\\
\bottomrule
\end{tabular}
\end{table}

需要注意，为保证结果的精确，如果不设定 Rd, 也即文献中的 Rday， 模型是无法计算的，因此上面的示例中虚构了一个，实际操作用一般使用低氧的 ACi 测量计算。

### 多条 CO~2~ 响应曲线的拟合 {#multi_curve}

fitacis 函数实际上是 fitaci 函数的扩展，方便一次拟合多条曲线^[需要注意，此时fitmethod一般推荐使用bilinear。]。函数的参数如下：


```r
fitacis(data, group, fitmethod = c("default", 
        "bilinear"),progressbar = TRUE, 
        quiet = FALSE, id = NULL, ...)

## S3 method for class 'acifits'
plot(x, how = c("manyplots", "oneplot"), 
     highlight = NULL, ylim = NULL, 
     xlim = NULL, add = FALSE, what = c("model",
     "data", "none"), ...)
```

**主要参数详解：**

实际上 fitacis 与 fitaci 模型算法完全一致，只不过增加了一个 group 参数，用于区分不同测量的数据，具体请参考举例内容。

#### fitacis 函数应用举例 {#fitacis_exa}

下文代码根据 plantecophys 中的示例代码修改，进行演示，原代码请参考其帮助文档。


```r
library(plantecophys)
# 只提取前10个不同测量的数据，节省时间进行举例
manyacidat2 <- droplevels(manyacidat[manyacidat$Curve %in% 
                                       levels(manyacidat$Curve)[1:10],])

# 对多条曲线进行拟合，使用bilinear方法，
# 仅仅因为其比非线性拟合节省时间
fits <- fitacis(manyacidat2, group = "Curve", fitmethod="bilinear", quiet = TRUE)

# 拟合结果为list，我们可以只提取第一个的拟合结果
fits[[1]]
```

```
## Result of fitaci.
## 
## Data and predictions:
##            Ci      Ameas     Amodel         Ac        Aj   Ap        Rd VPD
## 2    53.23129 -0.4401082  0.1014381  0.9601119  2.548123 1000 0.8586158 1.5
## 3    79.47367  2.4824630  2.1937702  3.0526198  7.036734 1000 0.8586158 1.5
## 4   116.74688  5.4531712  4.9419337  5.8011511 11.392394 1000 0.8586158 1.5
## 5   188.00801  9.7099879  9.5705964 10.4310194 16.447715 1000 0.8586158 1.5
## 6   278.44662 14.8225766 14.4261545 15.2897486 19.977583 1000 0.8586158 1.5
## 7   343.03259 17.7982155 17.4602014 18.3289218 21.639847 1000 0.8586158 1.5
## 1   344.72152 17.9244012 17.3165146 18.1849643 21.534276 1000 0.8586158 1.5
## 14  344.74839 16.7933747 17.6853306 18.5545917 21.774261 1000 0.8586158 1.5
## 8   588.08078 23.8925326 24.1309683 27.0327638 25.020148 3000 0.8586158 1.5
## 9   833.25547 26.5674409 25.7783026 33.0856065 26.647921 3000 0.8586158 1.5
## 10 1136.99222 25.9787890 26.8108335 38.1296944 27.676768 3000 0.8586158 1.5
## 11 1436.86370 26.6110657 27.5409345 42.0628463 28.405453 3000 0.8586158 1.5
## 12 1536.46772 27.4018784 27.7965881 43.3773689 28.660781 3000 0.8586158 1.5
## 13 1731.76400 28.6752069 28.0952804 45.2475932 28.959041 3000 0.8586158 1.5
##       Tleaf         Cc     PPFD Patm Ci_original
## 2  24.55873   53.23139 1799.959  100    53.23129
## 3  24.58292   79.47586 1799.590  100    79.47367
## 4  24.71278  116.75183 1799.819  100   116.74688
## 5  24.73687  188.01759 1800.371  100   188.00801
## 6  24.67508  278.46106 1800.233  100   278.44662
## 7  24.76596  343.05006 1799.575  100   343.03259
## 1  24.51593  344.73886 1800.356  100   344.72152
## 14 24.94098  344.76609 1799.964  100   344.74839
## 8  24.83785  588.10494 1799.477  100   588.08078
## 9  24.91185  833.28127 1799.969  100   833.25547
## 10 24.87314 1137.01906 1799.525  100  1136.99222
## 11 24.95914 1436.89126 1799.615  100  1436.86370
## 12 25.04542 1536.49554 1799.784  100  1536.46772
## 13 25.07566 1731.79212 1799.160  100  1731.76400
## 
## Root mean squared error:  2.196037 
## 
## Estimated parameters:
##          Estimate Std. Error
## Vcmax  65.0009909  1.3720635
## Jmax  131.7980133         NA
## Rd      0.8586158  0.2876248
## Note: Vcmax, Jmax are at 25C, Rd is at measurement T.
## 
## Curve was fit using method:  bilinear 
## 
## Parameter settings:
## Patm = 100
##  alpha = 0.24
##  theta = 0.85
##  EaV = 82620.87
##  EdVC = 0
##  delsC = 645.1013
##  EaJ = 39676.89
##  EdVJ = 2e+05
##  delsJ = 641.3615
## 
## Estimated from Tleaf (shown at mean Tleaf):
## GammaStar =  42.31453 
## Km =  698.2084
```

```r
# 使用sapply提取拟合结果的RMSE(均方根误差)
rmses <- sapply(fits, "[[", "RMSE")
plot(rmses, type='h', ylab="RMSE", xlab="Curve nr")
```

![(\#fig:fitacisr-1)fitacis作图结果](bookdown_files/figure-latex/fitacisr-1.pdf) 

```r
# 对最差的拟合结果进行作图
plot(fits[[which.max(rmses)]])
```

![(\#fig:fitacisr-2)fitacis作图结果](bookdown_files/figure-latex/fitacisr-2.pdf) 

可以看出，`fitaci` 和 `fitacis` 用法基本一致，各行代码均已经注释，更详细用法请参函数考帮助。

### `findCiTransition` 函数 {#transition}

计算 CiTransition 的函数，第一点为 Ac & Aj，第二点为 Aj & Ap，并且仅在计算 TPU 的前提下才会有第二点出现。


```r
findCiTransition(object, ...)
```

参数使用，object 为 fitaci 函数对象，或者整个的 `Photosyn` 函数。
... 为使用 `Photosyn` 时可传递的参数。


\cleardoublepage

<!--chapter:end:02-3-plantecophys-C3.Rmd-->

## C4 植物光合 {#c4}

之前的部分模型全部为关于 C3 植物的拟合，而 @Caemmerer2000Biochemical 的方法，则是针对
C4 植物的 A-Ci 曲线的实现。


```r
AciC4(Ci, PPFD = 1500, Tleaf = 25, VPMAX25 = 120, 
      JMAX25 = 400, Vcmax = 60, Vpr = 80, 
      alpha = 0, gbs = 0.003, O2 = 210, 
      x = 0.4, THETA = 0.7, Q10 = 2.3, 
      RD0 = 1, RTEMP = 25, TBELOW = 0, 
      DAYRESP = 1, Q10F = 2, FRM = 0.5, ...)
```

**参数详解**

* Ci：胞间二氧化碳浓度 ($\mu mol\cdot m^{-2}\cdot s^{-1}$)。
* PPFD：光合光量子通量密度 ($\mu mol\cdot m^{-2}\cdot s^{-1}$)。
* Tleaf：叶片温度 (\textcelsius)。
* VPMAX25：PEP 羧化最大速率 ($\mu mol\cdot m^{-2}\cdot s^{-1}$)。
* JMAX25：最大电子传递速率 (\textcelsius))。
* Vcmax：最大羧化速率($\mu mol\cdot m^{-2}\cdot s^{-1}$)。
* Vpr：PEP 再生($\mu mol\cdot m^{-2}\cdot s^{-1}$)。
* alpha：维管束鞘细胞中 PSII 活性的比例。
* gbs：维管束鞘导度 ($mol\cdot m^{-2}\cdot s^{-1}$)。
* O2：叶肉细胞氧气浓度。
* x：电子传递的分配因子。
* THETA：曲角参数。
* Q10：Michaelis-Menten 系数中依赖于温度的参数。
* RD0：基温下的呼吸 ($mol\cdot m^{-2}\cdot s^{-1}$)。
* RTEMP：呼吸的基温(\textcelsius)
* TBELOW：此温度以下呼吸为0。
* DAYRESP：明呼吸和暗呼吸的比值。
* Q10F：呼吸依赖于温度的参数。
* FRM：明呼吸中为叶肉呼吸的比例。

以上参数均来自 @Caemmerer2000Biochemical，括号中的参数值均为默认值，具体应用时请按照实际情况修改。

### C4 植物光合速率的计算 {#c4_sim}


```r
# 模拟 C4 植物的 Ci 值，计算光合速率并作图
library(plantecophys)
aci <- AciC4(Ci=seq(5,600, length=101))
with(aci, plot(Ci, ALEAF, type='l', ylim=c(0,max(ALEAF))))
```

![(\#fig:unnamed-chunk-26)C4 植物 A-Ci 作图](bookdown_files/figure-latex/unnamed-chunk-26-1.pdf) 

\cleardoublepage






<!--chapter:end:02-4-plantecophys-C4.Rmd-->

# 气孔导度模型的拟合 {#stomotal_sim}

气孔导度模型的拟合是通过 `fitBB` 来实现的，可以拟合三个 **Ball-Berry 类型的气孔导度模型**，共有下面几个参数：

* 气孔导度 (gs), 
* 光合 (A), 
* 外界 CO~2~ 浓度 (Ca) 
* 水气压亏缺 (VPD).

其三个模型的简介如下：

##  BallBerry 模型 {#ballberry}

@Ball1987A 发表的文章中的模型：

\begin{equation}
g_s = g0 + g1(\frac{A h_r}{C_a})
(\#eq:ballberry)
\end{equation}

其中 A 为净光合速率，g~0~ 和 g~1~ 为拟合参数，h~r~ 为叶片表面的相对湿度，C~a~ 为叶片处CO~2~浓度。

## BBLeuning 模型 {#bbleuning}

@leuning1995a 发表的文章中的模型：

\begin{equation}
g_s = g_0 + g_1(\frac{A}{(C_a - \Gamma)(1 + \frac{D}{D_0})})
(\#eq:BBLeuning)
\end{equation}

其中 $\Gamma$ 为 CO~2~ 补偿点，g~0~、g~1~ 和 D~0~ 为拟合参数。

## BBOptiFull 模型 {#bboptifull}

@Medlyn2011Reconciling 发表的文章中的模型：

\begin{equation}
g_s^* \approx g_0 + g_1(1 + \frac{g_1}{D}) \frac{A}{C_a}
(\#eq:BBOptiFull)
\end{equation}

额外的参数 g~k~ 来自于 @Duursma2013Near
\begin{equation}
g_s = g_0 + 1.6(1 + \frac{g_1}{D}(1-g_k)) \frac{A}{C_a}
(\#eq:Duursm)
\end{equation}

## `fitBB` 函数 {#fitbb-p}


```r
fitBB(data, varnames = list(
  ALEAF = "A", GS = "gsw", VPD = "VPDleaf",
  Ca ="CO2_s", RH = "RHcham"), 
  gsmodel = c("BBOpti", "BBLeuning", "BallBerry",
              "BBOptiFull"), fitg0 = FALSE)
```


**参数的意义:**

* data：待分析的数据文件。 
* varnames：注意，函数默认数据为 6400 格式，因此 6800 的数据文件要安装上文的参数修改。 相对湿度只有在使用  BallBerry 时才需要输入。
* gsmodel：上述三个模型之一。
* fitg0：默认不计算g0，若需要，改为TRUE。

**代码示例：**


```r
library(plantecophys)

aci <- read.csv("./data/aci.csv")
aci <- subset(aci, Obs > 0)
fitBB(aci, varnames = list(ALEAF = "Photo", GS = "Cond", VPD = "VpdL",
  Ca = "CO2S", RH = "RH_S"), gsmodel = "BBOpti", fitg0 = TRUE)
```

```
## Result of fitBB.
## Model :  BBOpti 
## Both g0 and g1 were estimated.
## 
## Coefficients:
## g0  g1
## 0.326 -0.992 
## 
## For more details of the fit, look at summary(myfit$fit)
## To return coefficients, do coef(myfit).
## (where myfit is the name of the object returned by fitBB)
```

## `fitBBs` 函数 {#fitbbs}

如果我们有多个物种的数据，那么使用  `fitBBs`  则可以快速拟合多条曲线的数据。我们先整合两次的数据，然后再查看运行结果：


```r
aci01 <- read.csv("./data/aci01.csv")
aci01 <- subset(aci01, Obs > 0)
multiBB <- data.frame(
  A = c(aci$Photo, aci01$Photo),
  GS = c(aci$Cond, aci01$Cond),
  CO2S = c(aci$CO2S, aci01$CO2S),
  VPD = c(aci$VpdL, aci01$VpdL),
  RH = c(aci$RH_S, aci01$RH_S),
  species = c(rep("species1", length(aci$Photo)),
  rep("species2", length(aci01$Photo)))
)

mod2 <- fitBBs(multiBB, group = "species",  
               varnames = list(
               ALEAF = "A", GS = "GS", VPD = "VPD",
               Ca ="CO2S", RH = "RH"), 
               gsmodel = "BallBerry", fitg0 = TRUE)
```

```
## RH provided in % converted to relative units.
## RH provided in % converted to relative units.
```

```r
coef(mod2)
```

```
##      group         g0         g1
## 1 species1 0.32638852 -0.1734554
## 2 species2 0.05158725 -0.0218842
```

\cleardoublepage

<!--chapter:end:03-stomatal.Rmd-->

# 光合最优气孔导度耦合模型 {#stomotal_couple}

@Cowan1977Stomatal 关于最优气孔导度的描述概括如下：
最优气孔行为理论认为气孔的最优化行为就是在某一时间段内， 最大化光合碳固定的同时最小化蒸腾作用，也就是说，对于一定的水分消耗，最大化光合碳固定。即使得：

\begin{equation}
A - \lambda E
(\#eq:weff)
\end{equation}

有最大化，其中 $\lambda$ 是临界水分利用效率，也即植物损耗单位水分的 C 生产量，单位一般为 $mol \: CO_2 \cdot mol^{-1} H_2O$，可通过光合速率和蒸腾速率计算。

## `FARAO` 函数 {#farao}

`FARAO` 函数用于找到最大化 $A - \lambda E$ 的** Ci **值。


```r
FARAO(lambda = 0.002, Ca = 400, VPD = 1,
      photo = c("BOTH", "VCMAX","JMAX"), 
      energybalance = FALSE, C4 = FALSE, 
      Tair = 25, Wind = 2, Wleaf = 0.02,
      StomatalRatio = 1, LeafAbs = 0.86, ...)

FARAO2(lambda = 0.002, Ca = 400, 
       energybalance = FALSE, ...)
```

其参数同 `fitaci` 和 `Photosyn`，在此不多做介绍，可参考[@Cowan1977Stomatal, @Buckley2014Is, @Medlyn2011Reconciling]。

\cleardoublepage

<!--chapter:end:04-optimize.Rmd-->

# 光合气孔导度耦合模型 {#photo_stomo}

@Duursma2015Plantecophys 对于气体交换耦合模型的简述如下 ^[详细内容请参考原文]：

* 对于 FvCB 模型有：

\begin{equation}
A_n = min(A_c, A_j)-R_d
(\#eq:minfvcb)
\end{equation}

* 在假定 gm 为恒定的前提下：

\begin{equation}
C_c = C_i - \frac{A_n}{g_m}
(\#eq:cgm)
\end{equation}

* 根据 Ficker 定理

\begin{equation}
A_n = \frac{g_s}{1.6}(C_a - C_i)
(\#eq:ficker)
\end{equation}

以及前文 \@ref(ballberry) 提到的气孔导度模型

\begin{equation}
g_s = g_0 + g_1 \frac{A_n}{C_a} f(D)
(\#eq:driver)
\end{equation}

整合公式 \@ref(eq:minfvcb), \@ref(eq:cgm), \@ref(eq:ficker) 和  \@ref(eq:driver)，气体交换耦合模型有很多更大尺度上的应用，例如 @Duursma2012MAESPA 和 wang1998a，可用于预测 An，gs 和蒸腾速率对主要环境驱动因子的响应（除土壤水分），并包含了主要的叶片性状（g1，V~cmax~, J~max~, R~d~ 以及他们的温度依赖性）。

## `Photosyn` 函数 {#photosyn}

Photosyn 为耦合的光合–气孔导度模型，基于 Farquhar 光合模型和 Ball-Berry 气孔导度模型。


```r
Photosyn(VPD = 1.5, Ca = 400, PPFD = 1500,
         Tleaf = 25, Patm = 100,RH = NULL,
         gsmodel = c("BBOpti", "BBLeuning", 
                     "BallBerry", "BBdefine"),
         g1 = 4, g0 = 0, gk = 0.5, vpdmin = 0.5,
         D0 = 5, GS = NULL, BBmult = NULL, 
         alpha = 0.24, theta = 0.85, Jmax = 100,
         Vcmax = 50, gmeso = NULL, TPU = 1000, 
         alphag = 0, Rd0 = 0.92, Q10 = 1.92,
         Rd = NULL, TrefR = 25, Rdayfrac = 1, 
         EaV = 82620.87, EdVC = 0, delsC = 645.1013, 
         EaJ = 39676.89, EdVJ = 2e+05, delsJ = 641.3615,
         GammaStar = NULL, Km = NULL, Ci = NULL, 
         Tcorrect = TRUE,returnParsOnly = FALSE,
         whichA = c("Ah", "Amin", "Ac", "Aj"))

Aci(Ci, ...)
```

因为是光合气孔导度模型的耦合，故而参数与之前的函数相同，参见 \@ref(fitaci-p) 和 \@ref(fitbb-p) 部分的内容。

### `Photosyn` 使用举例 {#photo_exam}


```r
library(plantecophys)
# 仅使用下面几个参数运行模型
#（其他参数使用默认值）
# 利用已测量或计算的参数
Photosyn(VPD=2, g1=4, Ca=500)
```

```
##         Ci    ALEAF        GS    ELEAF       Ac       Aj   Ap   Rd VPD Tleaf
## 1 369.3981 14.19466 0.1706377 3.412753 15.12654 17.03685 1000 0.92   2    25
##    Ca       Cc PPFD Patm
## 1 500 369.3981 1500  100
```

```r
# 部分参数相同，而某一参数或某几个参数不同时，
# 可以将不同的参数设置为一个序列（vectors）
r <- Photosyn(VPD=seq(0.5, 4, length=25), 
              Vcmax=50, Jmax=100)
with(r, plot(VPD, ALEAF, type='l'))
```

![(\#fig:vpd)VPD VS. An](bookdown_files/figure-latex/vpd-1.pdf) 

不同 VPD 下的光合速率见 \@ref(fig:vpd)。


```r
# 设定叶肉导度的拟合
run1 <- Photosyn(PPFD=seq(50,1000,length=25), 
                 gmeso=0.15, Vcmax=40, Jmax=85)
with(run1, plot(PPFD, GS, type='l'))
```

![(\#fig:ppfd-1)PPFD VS. GS](bookdown_files/figure-latex/ppfd-1.pdf) 

```r
# 运行ACi曲线数据（提供Ci值而不是计算）
arun1 <- Aci(Ci=seq(50, 1200, length=101), 
             Vcmax=40, Jmax=85)
arun2 <- Aci(Ci=seq(50, 1200, length=101), 
             Vcmax=30, Jmax=70)
with(arun1, plot(Ci, ALEAF, type='l'))
with(arun2, points(Ci, ALEAF, type='l', lty=5))
```

![(\#fig:ppfd-2)PPFD VS. GS](bookdown_files/figure-latex/ppfd-2.pdf) 

不同 ci 下的光合速率见 \@ref(fig:ppfd)。


```r
# 找出CO2需求和供给的交叉点
# 设定部分参数
gs <- 0.2 # 对水的气孔导度
Ca <- 400 # 外部CO2
gctogw <- 1.57 # 转换系数
gc <- gs / gctogw # 对 CO2 的气孔导度
# 需求曲线
p <- Aci(seq(60,500,length=101), Ca=400)
# 提供气孔导度及交叉点
g <- Photosyn(GS=gs, Ca=Ca)
# 交叉点可视化
par(yaxs="i")
with(p, plot(Ci, ALEAF, type='l', 
             ylim=c(0,max(ALEAF))))
with(g, points(Ci, ALEAF, pch=19, col="red"))
abline(gc * Ca, -gc, lty=5)
legend("topleft", c(expression(
  "Demand:"~~A==f(C[i])),
   expression("Supply:"~~A==g[c]*(C[a]-C[i])),
              "Operating point"),
   lty=c(1,5,-1),pch=c(-1,-1,19),
   col=c("black","black","red"),
   bty='n', cex=0.9)
```

![(\#fig:supply)supply VS. demand](bookdown_files/figure-latex/supply-1.pdf) 

需求与供给的作图 \@ref(fig:supply)。

## `PhotosynEB` 函数 {#photsyneb}

使用同 `Photosyn`，只不过使用能量平衡法来计算叶温。
目前版本尚存在bug，不能直接提供 GS 来计算，否则会出现未知错误。

## `PhotosynTuzet` 函数 {#photosyntuzet}

同样为光合气孔导度耦合模型，只不过基于 @tuzet2003a 的气孔导度模型，现将其描述如下：

\begin{equation}
g_{co2} = g_0 + \frac{a A}{C_i - \Gamma} f_{\Psi v}
(\#eq:tuzet)
\end{equation}

其中 g~co2~ 为对 CO~2~ 的气孔导度， g~0~ 是残余导度（residual conductance）(在补偿点时限制 g~CO2~ 的值）， A 为光合速率，C~i~ 为胞间二氧化碳浓度，$\Gamma$ 为 CO~2~ 补偿点，a 是经验系数。

一个根据经验的逻辑方程用于描述气孔对水势的敏感性：

\begin{equation}
f_{\Psi v} = \frac{1 + exp(s_f \Psi_f)}{1 + exp(s_f(\Psi_f - \Psi_v))}
(\#eq:ftuzet)
\end{equation}

$\Psi_v$ 是指叶片水势，$\Psi_f$ 是参比势能， 该方程假设在水势接近于 0 时，气孔对叶片水势不敏感，并且气孔随着 $\Psi_v$ 的下降快速关闭。$\Psi_f$ 和 $s_f$ (曲线形状参数) 依赖于不同物种的形态学适应以及生长过程中所处的环境。

### `PhotosynTuzet` 的参数 {#photosyntuzet_para}

除 @tuzet2003a 模型中使用的参数外，其他参数都继承了 `Photosyn` 的参数


```r
PhotosynTuzet(g1 = 8, Ca = 400, psis = 0,
              kl = 2, sf = 3, psif = -2,
...)
```

* g1：斜率参数，要远比 `fitBB` 中的大。
* Ca：外界大气 CO~2~ 浓度
* psis，土壤水势（Mpa）。
* kl：Leaf-speciﬁc hydraulic conductance（叶片导水参数 ($mmol \cdot m^{-2} \cdot s^{-1} \cdot MPa^{-1}$)，叶片蒸腾量、叶片面积及叶片水势计算）。
* sf：曲线形状参数。
* 在气孔导度为最大值 50% 时的叶片水势。

\cleardoublepage

<!--chapter:end:05-coupled.Rmd-->

# RHtoVPD 函数 {#rhtovpd}

该函数为一系列的工具，用于温度、湿度不同单位之间的换算 @Jones1993Plants ：


```r
# RH 转为 VPD  
RHtoVPD(RH, TdegC, Pa = 101) 
 
# VPD 转为 RH   
VPDtoRH(VPD, TdegC, Pa = 101) 
 
#  饱和水气压计算 
esat(TdegC, Pa = 101) 
 
#  露点温度转换为VPD 
DewtoVPD(Tdew, TdegC, Pa = 101) 
 
#  基于叶温的 VPD 转换为基于空气温度的 VPD
VPDleafToAir(VPD, Tleaf, Tair, Pa = 101) 
 
#  基于空气温度的 VPD 转换为基于叶温的 VPD 
VPDairToLeaf(VPD, Tair, Tleaf, Pa = 101) 
 
#  基于叶温的相对湿度转换为基于空气温度的相对湿度  
RHleafToAir(RH, Tleaf, Tair, Pa = 101) 
 
#  基于空气温度的相对湿度转换为基于叶温的相对湿度  
RHairToLeaf(RH, Tair, Tleaf, Pa = 101) 
```

注意事项及单位：

部分参数的转换需要准确的大气压用于计算，因此，有大气压选项的参数必须填入实际值。

RH：相对湿度 (\%) 。

TdegC：温度 (\textcelsius) (叶片或温度)。

Pa：大气压 (kPa)

VPD：水气压亏缺 (kPa)。

Tdew： 露点温度(\textcelsius) 。

Tleaf：叶温(\textcelsius) 。

Tair：空气温度(\textcelsius) 。

\cleardoublepage

<!--chapter:end:06-units.Rmd-->

# 光响应曲线的拟合 {#lrc_fit}

光响应曲线模型有很多，主要分为四大类，直角双曲线，非直角双曲线，指数以及直角双曲线修正模型，我们分别对这四类进行阐述。

## 直角双曲线模型 {#rec_mod}

@BalyEC1935 提出了直角双曲线模型，它的表达式为：

\begin{equation}
P_{n}  = \frac{\alpha I\ P_{nmax}}{\alpha I + P_{nmax}}- R_{d}
(\#eq:rec)
\end{equation}

* 其中，$P_{n}$ 为净光合速率；
* I 为光强；
* $\alpha$ 为光响应曲线在光强为 0 时的斜率，即光响应曲线的初始斜率，也称之为初始量子效率；
* $P_{nmax}$ 为最大净光合速率；
* $R_{d}$：为暗呼吸速率。

对 \@ref(eq:rec) 求导可知其导数大于 0，也就是直角双曲线是一个没有极值的渐近线，因此，无法由 \@ref(eq:rec)  求得最大光合速率的饱和光强^[直角双曲线和非直角双曲线模型类似，如果测量时饱和光强之后光合速率不下降，则计算的P$_{nmax}$则远大于实测值。]。

因此就需要使用弱光条件下
($\leq$ 200 $\mu mol\cdot m^{-2}\cdot s^{-1}$) 的数据得到表观量子效率（apparent
quantum efficiency，AQE），利用非线性最小二乘法估算 P$_{nmax}$ ，然后利用 @YEZiPiao2010 的式 \@ref(eq:aqe) 求解 $I_{sat}$，

\begin{equation}
P_{nmax}= AQE \times I_{sat} - R_{d}
(\#eq:aqe)
\end{equation}

但此方法测得的光饱和点远小于实测值，我们采用 0.7P$_{nmax}$ @ZhangXS2009、0.9P$_{nmax}$
@HuangHY2009、或其他设定的值来的来估算$I_{sat}$。

### 直角双曲线模型的实现 {#rec_fit}

若没有安装 `minpack.lm`, 则需要首先：


```r
install.packages("minpack.lm")
```

具体实现过程如下：


```r
# 调用非线性拟合包minpack.lm，也可以直接使用nls
library(minpack.lm)
# 读取数据，同fitaci数据格式
lrc <- read.csv("./data/lrc.csv")
lrc <- subset(lrc, Obs > 0)

# 光响应曲线没有太多参数，
# 直接调出相应的光强和光合速率
# 方便后面调用
lrc_Q <- lrc$PARi
lrc_A <- lrc$Photo 

# 采用非线性拟合进行数据的拟合
lrcnls <- nlsLM(lrc_A ~ (alpha * lrc_Q * Am) * 
                (1/(alpha * lrc_Q + Am)) - Rd,  
              start=list(Am=(max(lrc_A)-min(lrc_A)),
              alpha=0.05,Rd=-min(lrc_A))
)
fitlrc_rec <- summary(lrcnls)

# 补偿点时净光合速率为0，
# 据此利用uniroot求解方程的根
Ic <- function(Ic){(fitlrc_rec$coef[2,1] * Ic *
    fitlrc_rec$coef[1,1]) * (1/(fitlrc_rec$coef[2,1] * 
    Ic + fitlrc_rec$coef[1,1])) - fitlrc_rec$coef[3,1] 
}

uniroot(Ic, c(0,50))$root
```

```
## [1] 3.650053
```

```r
# 根据饱和点定义，0.75最大光合速率为饱和点，
# 也可以是其他比例
# 据此利用uniroot求解方程的根

Isat <- function(Isat){(fitlrc_rec$coef[2,1] * 
        Isat * fitlrc_rec$coef[1,1]) * 
    (1/(fitlrc_rec$coef[2,1] * Isat + 
          fitlrc_rec$coef[1,1])) - fitlrc_rec$coef[3,1] -
    0.75 * fitlrc_rec$coef[1,1]
}
# 求值区间根据具体实验确定

uniroot(Isat, c(0,2500))$root
```

```
## [1] 700.0946
```

```r
# 使用ggplot2进行作图并拟合曲线
library(ggplot2)

light <- data.frame(lrc_Q = lrc$PARi, lrc_A = lrc$Photo)

p <- ggplot(light, aes(x = lrc_Q, y = lrc_A))

p1 <- p + geom_point(shape = 16, size = 3, color = "green") + 
  geom_smooth(method="nls", formula = y ~ (alpha * x * Am) * 
  (1/(alpha * x + Am)) - Rd, se = FALSE,
  method.args = 
  list(start = c(Am=(max(lrc_A)-min(lrc_A)),
  alpha=0.05,Rd=-min(lrc_A)), 
  aes(x =lrc_Q, y = lrc_A, color='blue', size = 1.2))
  ) +
  labs(y=expression(paste("photosynthetic rate  ", 
       "(", mu, mol%.%m^-2%.%s^-1, ")")), 
       x=expression(paste("PAR ", 
       "(", mu, mol%.%m^-2%.%s^-1, ")"))
       )


# 自定义坐标轴
p1 + scale_x_continuous(breaks = seq(0, 2100, by = 200)) +  
  scale_y_continuous(breaks= round(light$lrc_A)) +
  theme(axis.text.x  = element_text(
    size = 10, angle=30, vjust=0.5), 
    axis.text.y = element_text(size = 10),
    axis.title.x = element_text(size = 12, face = 'bold'),
    axis.title.y = element_text(size = 12, face = 'bold')
  )
```

![(\#fig:recr)直角双曲线模型拟合](bookdown_files/figure-latex/recr-1.pdf) 

代码目的见注释，其实现过程主要分三步：

* 数据的导入，这与之前相同，具体格式方法参考前文 \ref{fitaci}。
* 光响应曲线的拟合，使用到了非线性模型 nlsLM，也可以使用 nls，具体实现方法请查看参考文档。
* 求饱和点和补偿点，补偿点的计算根据其定义，净光合速率为 0，求解模型在一定区间的根来计算，而饱和点则较为麻烦，若使用式 \@ref(eq:aqe)
计算，那么饱和点远远低于我们实际需求的，因此，我们使用了 0.75P$_{nmax}$ 来计算，求得目标区间的根。当然也可以采用其他比例来作为饱和点光合速率。



\begin{table}

\caption{(\#tab:rectable)直角双曲线计算参数}
\centering
\begin{tabular}[t]{lrrrr}
\toprule
  & Estimate & Std. Error & t value & Pr(>|t|)\\
\midrule
Am & 16.6721752 & 0.1522849 & 109.480151 & 0.0000000\\
alpha & 0.0783312 & 0.0026774 & 29.256870 & 0.0000000\\
Rd & 0.2810926 & 0.0789338 & 3.561117 & 0.0051716\\
\bottomrule
\end{tabular}
\end{table}

最终的数据拟结果如图 \@ref(fig:recr) 所示，拟合的参数及结果见表 \@ref(tab:rectable)。

\cleardoublepage

<!--chapter:end:07-rec.Rmd-->

## 非直角双曲线模型{#nonrec-mod}

@Thornley1976 提出了非直角双曲线模型，它的表达式为：

\begin{equation}
P_{n} = \frac{\alpha I + P_{nmax} \sqrt{(\alpha I + P_{nmax})^{2} - 4  \theta \alpha I P_{nmax}}}{2 \theta} - R_{d}
(\#eq:nrec)
\end{equation}

其中，$\theta$ 为表示曲线弯曲程度的曲角参数，取值为$0\leq \theta \leq 1$。其他参数意义同式 \@ref(eq:rec)。同样如同直角双曲线模型，式仍然没有极值，无法求得 $I_{sat}$，可以仍然参考直角双曲线模型的方式进行计算。

### 非直角双曲线模型的实现{#nonrec_mode_exam}



```r
library(minpack.lm)

# 读取数据，同fitaci数据格式
lrc <- read.csv("./data/lrc.csv")
lrc <- subset(lrc, Obs > 0)

# 光响应曲线没有太多参数，
# 直接调出相应的光强和光合速率
# 方便后面调用
lrc_Q <- lrc$PARi
lrc_A <- lrc$Photo 

# 非直角双曲线模型的拟合
lrcnls <- nlsLM(lrc_A ~ 
                (1/(2*theta))*
                (alpha*lrc_Q+Am-sqrt((alpha*lrc_Q+Am)^2 - 
                4*alpha*theta*Am*lrc_Q))- Rd,
                start=list(Am=(max(lrc_A)-min(lrc_A)),
                alpha=0.05,Rd=-min(lrc_A),theta=1)) 

fitlrc_nrec <- summary(lrcnls)

# 光补偿点
Ic <- function(Ic){
  (1/(2 * fitlrc_nrec$coef[4,1])) * 
    (fitlrc_nrec$coef[2,1] * Ic + fitlrc_nrec$coef[1,1] - 
    sqrt((fitlrc_nrec$coef[2,1] * Ic + fitlrc_nrec$coef[1,1]
    )^2 -  4 * fitlrc_nrec$coef[2,1] * 
    fitlrc_nrec$coef[4,1] * fitlrc_nrec$coef[1,1] * Ic)) -
    fitlrc_nrec$coef[3,1]
}

uniroot(Ic, c(0,50))$root  
```

```
## [1] 2.234292
```

```r
# 光饱和点
Isat <- function(Isat){
  (1/(2 * fitlrc_nrec$coef[4,1])) * (fitlrc_nrec$coef[2,1] * 
  Isat + fitlrc_nrec$coef[1,1] - sqrt(
  (fitlrc_nrec$coef[2,1] * Isat +fitlrc_nrec$coef[1,1])^2 - 
  4*fitlrc_nrec$coef[2,1] * fitlrc_nrec$coef[4,1] * 
  fitlrc_nrec$coef[1,1] * Isat)) - 
  fitlrc_nrec$coef[3,1] - (0.9*fitlrc_nrec$coef[1,1])}

uniroot(Isat, c(0,2000))$root
```

```
## [1] 1596.286
```

```r
# 使用ggplot2进行作图并拟合曲线
library(ggplot2)
light <- data.frame(lrc_Q = lrc$PARi, lrc_A = lrc$Photo)

p <- ggplot(light, aes(x = lrc_Q, y = lrc_A))

p1 <- p + geom_point(shape = 16, size = 3, color = "green") + 
  geom_smooth(method="nls", formula = y ~ 
  (1/(2*theta))*(alpha*x+Am-sqrt((alpha*x+Am)^2 - 
   4*alpha*theta*Am*x))- Rd, se = FALSE,
   method.args = list(start = c(Am=(max(lrc_A)-min(lrc_A)), 
   alpha=0.05, Rd=-min(lrc_A), theta=1), 
    aes(x =lrc_Q, y = lrc_A, color='blue', size = 1.2))
) +
  labs(y=expression(paste("photosynthetic rate  ", 
          "(", mu, mol%.%m^-2%.%s^-1, ")")), 
       x=expression(paste("PAR ", 
           "(", mu, mol%.%m^-2%.%s^-1, ")")))

# 自定义坐标轴
p1 + scale_x_continuous(breaks = seq(0, 2100, by = 200)) +  
  scale_y_continuous(breaks= round(light$lrc_A)) +
   theme(axis.text.x  = element_text(
    size = 10, angle=30, vjust=0.5), 
    axis.text.y = element_text(size = 10),
    axis.title.x = element_text(size = 12, face = 'bold'),
    axis.title.y = element_text(size = 12, face = 'bold')
  )
```

![(\#fig:nrecr)非直角双曲线模型拟合](bookdown_files/figure-latex/nrecr-1.pdf) 

\begin{table}

\caption{(\#tab:nrectable)非直角双曲线计算参数}
\centering
\begin{tabular}[t]{lrrrr}
\toprule
  & Estimate & Std. Error & t value & Pr(>|t|)\\
\midrule
Am & 15.8017296 & 0.1513064 & 104.435285 & 0.0000000\\
alpha & 0.0658067 & 0.0020216 & 32.551422 & 0.0000000\\
Rd & 0.1461717 & 0.0420800 & 3.473659 & 0.0070082\\
theta & 0.3700908 & 0.0493403 & 7.500783 & 0.0000369\\
\bottomrule
\end{tabular}
\end{table}

最终的数据拟结果如图 \@ref(fig:nrecr) 所示，拟合的参数及结果见表 \@ref(tab:nrectable)。单纯从作图来看，本例数据使用非直角双曲线与散点图重合程度更高。

\cleardoublepage

<!--chapter:end:08-nonrec.Rmd-->

## 指数模型 {#lrc_exp}

光合指数模型较多，我们此处使用的指数函数的模型 @Prado1997Photosynthetic，其表达式为：

\begin{equation}
P_{n} = P_{nmax}[1 - e^{-b(I-I_{C})}]
(\#eq:exp)
\end{equation}

其中，$I_{c}$ 为光补偿点，$e$ 为自然对数的底，b为常数，其他参数意义同 \@ref(eq:exp)。同样，该方程仍然是没有极值的函数，但我们可以直接求得光补偿点。

### 指数模型的实现 {#lrc_exp_exam}


```r
library(minpack.lm)

# 读取数据，同fitaci数据格式
lrc <- read.csv("./data/lrc.csv")
lrc <- subset(lrc, Obs > 0)

# 光响应曲线没有太多参数，
# 直接调出相应的光强和光合速率
# 方便后面调用
lrc_Q <- lrc$PARi
lrc_A <- lrc$Photo 

# 模型的拟合
lrcnls <- nlsLM(lrc_A ~ Am*(1-exp((-b)*(lrc_Q-Ic))),
                start=list(Am=(max(lrc_A)-min(lrc_A)),
                           Ic=5, b=1)
                )
fitlrc_exp <- summary(lrcnls)

# 光饱和点
Isat <- function(Isat){fitlrc_exp$coef[1,1]*
    (1-exp((-fitlrc_exp$coef[3,1])*(Isat-
    fitlrc_exp$coef[2,1])))-0.9*fitlrc_exp$coef[1,1]}

uniroot(Isat, c(0,2000))$root
```

```
## [1] 558.6038
```

```r
## 拟合图形
library(ggplot2)
light <- data.frame(lrc_Q = lrc$PARi, lrc_A = lrc$Photo)

p <- ggplot(light, aes(x = lrc_Q, y = lrc_A))

p1 <- p + 
  geom_point(shape = 16, size = 3, color = "green") + 
  geom_smooth(method="nls", formula = 
    y ~ Am*(1-exp((-b)*(x -Ic))), 
    se = FALSE, method.args = list(
    start = c(Am=(max(lrc_A)-min(lrc_A)),
    Ic=5, b=0.002), aes(x =lrc_Q, y = lrc_A, 
    color='blue', size = 1.2))
  ) +
  labs(y=expression(paste("photosynthetic rate  ", 
          "(", mu, mol%.%m^-2%.%s^-1, ")")), 
       x=expression(paste("PAR ", 
           "(", mu, mol%.%m^-2%.%s^-1, ")")))


# 自定义坐标轴
p1 + scale_x_continuous(breaks = seq(0, 2100, by = 200)) +  
  scale_y_continuous(breaks= round(light$lrc_A)) +
   theme(axis.text.x  = element_text(
    size = 10, angle=30, vjust=0.5), 
    axis.text.y = element_text(size = 10),
    axis.title.x = element_text(size = 12, face = 'bold'),
    axis.title.y = element_text(size = 12, face = 'bold')
  )
```

![(\#fig:nexpr)指数模型拟合](bookdown_files/figure-latex/nexpr-1.pdf) 

\begin{table}

\caption{(\#tab:nexptable)指数模型计算参数}
\centering
\begin{tabular}[t]{lrrrr}
\toprule
  & Estimate & Std. Error & t value & Pr(>|t|)\\
\midrule
Am & 13.6547568 & 0.1723363 & 79.233185 & 0.0000000\\
Ic & -0.5133438 & 2.3370250 & -0.219657 & 0.8305573\\
b & 0.0041183 & 0.0002012 & 20.467032 & 0.0000000\\
\bottomrule
\end{tabular}
\end{table}

最终的数据拟结果如图 \@ref(fig:nexpr) 所示，拟合的参数及结果见表 \@ref(tab:nexptable)。


\cleardoublepage

<!--chapter:end:09-exp.Rmd-->

## 直角双曲线的修正模型 {#rev_rec}

@YEZiPiao2010 直角双曲线修正模型的表达式如式 \@ref(eq:mrec) 所示：

\begin{equation}
P_{n} = \alpha \frac{1-\beta I}{1+\gamma I} I - R_{d}
(\#eq:mrec)
\end{equation}

其中，$\beta$ 和 $\gamma$ 为系数，$\beta$光抑制项，$\gamma$光饱和项，单位为
$m^{2}\cdot s\cdot\mu mol^{-1}$，其他参数与上文相同，因为该式 \@ref(eq:mrec)
存在极值，因此，必然存在饱和光强和最大净光合速率，分别用式 \@ref(eq:isat) 和式 \@ref(eq:ic) 求得。

\begin{equation}
I_{sat} = \frac{\sqrt{\frac{(\beta+\gamma)}{\beta}} - 1}{\gamma}
(\#eq:isat)
\end{equation}

\begin{equation}
P_{nmax} = \alpha\left(\frac{\sqrt{\beta+\gamma}-\sqrt{\beta}}{\gamma}\right)^{2}-R_{d}
(\#eq:ic)
\end{equation}

该模型的优点为拟合结果中光饱和点和最大净光合速率均接近实测值，还可以拟合饱和光强之后光合速率随光强下降段的曲线。

### 直角双曲线修正模型的实现 {#rev_rec_exam}


```r
library(minpack.lm)

# 读取数据，同fitaci数据格式
lrc <- read.csv("./data/lrc.csv")
lrc <- subset(lrc, Obs > 0)

# 光响应曲线没有太多参数，
# 直接调出相应的光强和光合速率
# 方便后面调用
lrc_Q <- lrc$PARi
lrc_A <- lrc$Photo 

# 模型的拟合
lrcnls <- nlsLM(lrc_A ~ alpha * ((1 - 
              beta*lrc_Q)/(1 + gamma * lrc_Q)) * lrc_Q - Rd,
                start=list(alpha = 0.07, beta = 0.00005,
                           gamma=0.004, Rd = 0.2)
                )
fitlrc_mrec <- summary(lrcnls)

# 饱和点计算
Isat <-  (sqrt((fitlrc_mrec$coef[2,1] + fitlrc_mrec$coef[3,1])/
              fitlrc_mrec$coef[2,1]) -1)/fitlrc_mrec$coef[3,1]
# 补偿点计算
Ic <- (
  -(fitlrc_mrec$coef[3, 1] * fitlrc_mrec$coef[4, 1] - 
  fitlrc_mrec$coef[1, 1]) - sqrt((fitlrc_mrec$coef[3, 1] * 
  fitlrc_mrec$coef[4, 1] - fitlrc_mrec$coef[1, 1])^2- 
  (4 * fitlrc_mrec$coef[1, 1] * fitlrc_mrec$coef[2, 1] * 
  fitlrc_mrec$coef[4, 1])))/
  (2*fitlrc_mrec$coef[1,1]*fitlrc_mrec$coef[2,1])

## 拟合图形
library(ggplot2)
light <- data.frame(lrc_Q = lrc$PARi, lrc_A = lrc$Photo)

p <- ggplot(light, aes(x = lrc_Q, y = lrc_A))

p1 <- p + 
  geom_point(shape = 16, size = 3, color = "green") + 
  geom_smooth(method="nls", formula = 
   y ~ alpha * ((1 - 
              beta*x)/(1 + gamma * x)) * x - Rd, 
    se = FALSE, method.args = list(
    start = c(alpha = 0.07, beta = 0.00005,
              gamma=0.004, Rd = 0.2), 
    aes(x =lrc_Q, y = lrc_A, 
    color='blue', size = 1.2))
  ) +
  labs(y=expression(paste("photosynthetic rate  ", 
          "(", mu, mol%.%m^-2%.%s^-1, ")")), 
       x=expression(paste("PAR ", 
           "(", mu, mol%.%m^-2%.%s^-1, ")")))


# 自定义坐标轴
p1 + scale_x_continuous(breaks = seq(0, 2100, by = 200)) +  
  scale_y_continuous(breaks= round(light$lrc_A)) +
   theme(axis.text.x  = element_text(
    size = 10, angle=30, vjust=0.5), 
    axis.text.y = element_text(size = 10),
    axis.title.x = element_text(size = 12, face = 'bold'),
    axis.title.y = element_text(size = 12, face = 'bold')
  )
```

![(\#fig:mrecr)直角双曲线修正模型拟合](bookdown_files/figure-latex/mrecr-1.pdf) 

\begin{table}

\caption{(\#tab:mrectable)直角双曲线修正模型计算参数}
\centering
\begin{tabular}[t]{lrrrr}
\toprule
  & Estimate & Std. Error & t value & Pr(>|t|)\\
\midrule
alpha & 0.0730858 & 0.0021209 & 34.460183 & 0.0000000\\
beta & 0.0000501 & 0.0000133 & 3.776115 & 0.0043751\\
gamma & 0.0040622 & 0.0001955 & 20.773916 & 0.0000000\\
Rd & 0.2156186 & 0.0543505 & 3.967190 & 0.0032685\\
\bottomrule
\end{tabular}
\end{table}

尽管修正模型可以方便的计算饱和点和补偿点，但如同 @Lobo2013Fitting 所指出，双曲线模型对其结果的计算常有超出生态学意义范围的值^[例如本例的数据结果]，因此对模型的选择不能一概而论，需根据实际情况而选择。

\cleardoublepage

<!--chapter:end:10-modified-rec.Rmd-->

# 关于非线性拟合的初始值 {#start_con}

在解释初始值之前我们首先需要了解一个数学上的概念——迭代，

>“迭代法”也称“辗转法”是一种不断用变量的旧值递推新值的过程。

用通俗但不是特别严谨的说法可解释为：每次执行这种算法时，程序都会从原值（也就是我抄的上面迭代法定义的旧值）推出一个新值。

之所以先介绍这个迭代，原因很简单，非线性拟合就是通过迭代的方法，需要对每一个变量**最初的估计值进行不断的迭代，得到一个向一个点收缩或汇聚的值，这个估计值必须在实际值的一定范围内，程序通过不断调整这个值来改善拟合结果**。这就解释了上面的问题，初始值是让程序开始运行的前提，不然没法迭代，必须设定。我下面的内容将以 LI-6800 的光响应曲线的测试数据，使用非直角双曲线模型进行拟合来讲解具体的 R 中的一些实现方法，我们首先导入数据，然后再利用这些数据逐个举例不同的确定初始值的方式。


```r
nls <- read.csv("data/nlstest.csv")


# 光响应曲线比较简单，我们将需要的数据直接提取，方便后面操作
lrc_Q <- nls$Qin
lrc_A <- nls$A
```

## nlsLM 解决方案 {#nlslm}

nlsLM 来自于 @Elzhov2016minpack 的 `minpack.lm`，利用 C 语言的 MINPACK 库，修改了 Levenberg-Marquardt 算法，在实际操作中，很多时候并不准确的输入初始值，他也能得出比较好的拟合结果。但结果未必完美，出现下面让人烦恼的报错：

> singular gradient matrix at initial parameter estimates

的概率会大大降低，而且尽管结果不如意，我们也可以利用他的结果缩小初始值的范围，继续尝试其他初始值。

例如下面的例子中，非直角双曲线的 Rd 的初始值我们可以利用暗呼吸的实测值大致估计，同理最大光合速率也是如此，剩下的分别为非直角双曲线曲率，我们暂定为 1，alpha 也暂定为 0.1，使用 `nlsLM` 进行拟合，结果如下：


```r
library(minpack.lm)

lrcnls_lm <- nlsLM(lrc_A ~ (1/(2*theta))*
        (alpha*lrc_Q+Am-sqrt((alpha*lrc_Q+Am)^2 - 
        4*alpha*theta*Am*lrc_Q))- 
        Rd, start=list(Am=(max(lrc_A)-min(lrc_A)),
        alpha=0.1,Rd=-min(lrc_A),theta=0.8)) 
```

结果没有报错，看上去没有问题，那我们观察一下具体的拟合结果：


```r
summary(lrcnls_lm)
```

```
## 
## Formula: lrc_A ~ (1/(2 * theta)) * (alpha * lrc_Q + Am - sqrt((alpha * 
##     lrc_Q + Am)^2 - 4 * alpha * theta * Am * lrc_Q)) - Rd
## 
## Parameters:
##        Estimate Std. Error t value Pr(>|t|)    
## Am    12.307570   0.406739  30.259 2.30e-10 ***
## alpha  0.045706   0.003423  13.352 3.09e-07 ***
## Rd     0.656638   0.132646   4.950 0.000791 ***
## theta  0.707522   0.079738   8.873 9.59e-06 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.1852 on 9 degrees of freedom
## 
## Number of iterations to convergence: 8 
## Achieved convergence tolerance: 1.49e-08
```

结果看上去还可以^[有些时候结果并不理想，该方法并不是万无一失]。

## 作图比对法 {#plot_comp}

模型很多参数可以用已有数据去估计，我们可以只来分析难以判断的参数，流程如下：

* Rd、Am等我们可以利用测量值来确定一个范围。
* 剩余的参数，我们也可以根据经验或文献来有一个大致的判断。
* 然后我们根据数学的方式来判断哪个参数对曲线形状影响最大（例如在分母上的参数，或者是乘以该参数，该参数可以显著改变计算结果，例如整体乘以或除以 0.1 还是 0.01，像 Rd  之类的参数本身就很小，多数公式中都是减去该值，对结果影响很小，我们通常直接使用实测值 ）。
* 将该参数取一系列值带入模型来求解净光合速率。
* 将计算的A值与光强进行作图，看我们计算的曲线与测量数据点的重合程度，必要时在修改其他参数，使曲线和散点重合度最好，重合程度最高的参数值即为我们需要的初始值。

### 实现过程 {#plot_exam}


```r
# 我们选择的模型，将其写为一个函数，用于计算净光合速率
expfct <- function(x, Am, alpha, Rd, theta) {
  (1/(2 * theta)) * (alpha * x + Am - 
  sqrt((alpha * x + Am)^2 - 4 * alpha * theta * Am * x)) - Rd
}

# 我们的数据
test <- data.frame(x = lrc_Q, y = lrc_A)
```


```r
# 先做实测数据的散点图
plot(y ~ x, data = test)

# 利用上面的函数，假定 alpha 的值为0.8，看计算值与测量值重合程度
curve(expfct(x, Am = (max(lrc_A)-min(lrc_A)),
     alpha=0.8, Rd=-min(lrc_A), theta=0.8), add = TRUE
             )
```

![(\#fig:pomp)初步判断 alpha 的初始值](bookdown_files/figure-latex/pomp-1.pdf) 

观察上图 \@ref(fig:pomp) 的结果可以看到，曲线在 0-600 的范围内，拟合值明显偏大，观察模型的方程式，以及其他起始值的设定方式，我们初步判断 alpha 的值偏大，于是乎我们将其改小观察，观察曲线和测量点的重合仍然不是很好，我们尝试修改 theta 值与 alpha 值（也即曲线高于测量点，则需要减小纵坐标的值，低于测量点，则需要增加该值，该过程省略，我大概设置了五分钟完成），最终得出的结果如下：



```r
plot(y ~ x, data = test)
curve(expfct(x, Am = (max(lrc_A)-min(lrc_A)),
     alpha=0.06, Rd=-min(lrc_A), theta=0.82), add = TRUE)
```

![(\#fig:alpp)初修正后断 alpha 的初始值](bookdown_files/figure-latex/alpp-1.pdf) 

图 \@ref(fig:alpp) 尽管看上去效果仍然不满意，但我们可试着进行拟合，看能否得到显著差异的结果：


```r
lrcnls_manual <- nls(lrc_A ~ 
        (1/(2*theta))*
        (alpha*lrc_Q+Am-sqrt((alpha*lrc_Q+Am)^2 - 
                               4*alpha*theta*Am*lrc_Q))- 
        Rd, start=list(Am=(max(lrc_A)-min(lrc_A)),
                       alpha=0.03,Rd=-min(lrc_A),theta=0.6))
summary(lrcnls_manual)
```

```
## 
## Formula: lrc_A ~ (1/(2 * theta)) * (alpha * lrc_Q + Am - sqrt((alpha * 
##     lrc_Q + Am)^2 - 4 * alpha * theta * Am * lrc_Q)) - Rd
## 
## Parameters:
##        Estimate Std. Error t value Pr(>|t|)    
## Am    12.307585   0.406741  30.259 2.30e-10 ***
## alpha  0.045706   0.003423  13.352 3.09e-07 ***
## Rd     0.656642   0.132646   4.950 0.000791 ***
## theta  0.707518   0.079739   8.873 9.59e-06 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.1852 on 9 degrees of freedom
## 
## Number of iterations to convergence: 7 
## Achieved convergence tolerance: 4.601e-06
```



```r
# 对拟合之后的结果作图，观察使用我们的估计值，
# 迭代的最终值与元数据的重合程度
plot(y ~ x, data = test, ylim = c(-2, 14))
curve(expfct(x, Am = 12.307586,
     alpha=0.045706, Rd= 0.656643, theta=0.707518), add = TRUE)
```

![(\#fig:alpf)检验作图法的初始值判断](bookdown_files/figure-latex/alpf-1.pdf) 

从 \@ref(fig:alpf) 的呈现以及 F 检验的 p 值来讲，图形已经比较完美了。**也就是说尽管我们作图的时候看到重合度并不高，但是非线性拟合本来就是一个迭代的过程，只要我们的数据与真实值相差不大，还是能够得到完美结果的**。

### 直观展示 {#show_demo}

上面的表述太啰嗦，直接用下面的图形说明一下，其中 alhpa 的取值在此处选择从 0.01 到 0.07，每次增加 0.05，其他值分别为 Am = 12.31, Rd= 0.66, theta=0.71 （此处为展示效果和方便，将这些值直接按照拟合结果设定了，实际差别不大）


```r
library(ggplot2)
library(purrr)

lrc <- read.csv("data/nlstest.csv")

# 光响应曲线比较简单，我们将需要的数据直接提取，方便后面操作
lrc_Q <- lrc$Qin
lrc_A <- lrc$A
n <- length(lrc_A)


alp <- paste0("a=", seq(0.01, 0.07, by = 0.005))
alpn <- rep(alp, each = n)

expfct <- function(x, Am, alpha, Rd, theta) {(1/(2 * theta)) * (alpha * x + Am - sqrt((alpha * x + Am)^2 - 4 * alpha * theta * Am * x)) - Rd
}

paras <- data.frame(alpha = rep(seq(0.01, 0.07, by = 0.005), each = n), 
           x = rep(lrc_Q, n), Am = rep(12.31, n), Rd = rep(0.66, n), 
           theta = rep(0.71, n))
y = unlist(pmap(paras, expfct))

show <- data.frame(x = rep(lrc_Q, 14),
           y = c(lrc_A, y), 
           a = factor(c(rep("measured", n), alpn),
           level = c("measured", alp)
             ))

ggplot(data = show, aes(x, y, group = a, color=a)) + 
  geom_point() + 
  geom_smooth(se = FALSE) 
```

![(\#fig:malp)多个 alpha 取值的差异](bookdown_files/figure-latex/malp-1.pdf) 

从上图 \@ref(fig:malp) 我们我们可以看到，实测值在 alpha =0.04 和 alpha = 0.05 两条曲线之间，在 0.045 时最接近测量点，也就是我们把初始值设为 0.04 和 0.05 之间最接近，本例中可认为是0.045，实际这三个值均可。

## 自动多次尝试法 {#mult_try}

该方法实际为使用 `nls2` 来实现，具体方法参考 @nls2 的文章，可简单概括为使用一系列的起始值梯度（例如下面的代码中， alpha 的取值在 0.01 到 0.08 之间 ），然后软件循序使用不同的起始值，即排列组合所有的起始值序列，最终找到合适的值，具体实现如下：



```r
library(nls2)

grid.test <- expand.grid(list(
  Am=c(12),
  alpha = seq(0.01, 0.08, by =0.01),
  Rd = seq(0, 3),
  theta=seq(0.1, 1, by = 0.1)
  ))

lrcnls2 <- nls2(lrc_A ~ 
        (1/(2*theta))*
        (alpha*lrc_Q+Am-sqrt((alpha*lrc_Q+Am)^2 - 
                               4*alpha*theta*Am*lrc_Q))- 
        Rd, start = grid.test, algorithm = "brute-force")
```

```
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
## Error in numericDeriv(form[[3L]], names(ind), env, central = nDcentral) : 
##   Missing value or an infinity produced when evaluating the model
```

```r
summary(lrcnls2)
```

```
## 
## Formula: lrc_A ~ (1/(2 * theta)) * (alpha * lrc_Q + Am - sqrt((alpha * 
##     lrc_Q + Am)^2 - 4 * alpha * theta * Am * lrc_Q)) - Rd
## 
## Parameters:
##        Estimate Std. Error t value Pr(>|t|)    
## Am    12.000000   0.623023  19.261 1.27e-08 ***
## alpha  0.050000   0.006414   7.795 2.72e-05 ***
## Rd     1.000000   0.260153   3.844  0.00394 ** 
## theta  0.800000   0.102143   7.832 2.62e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.3663 on 9 degrees of freedom
## 
## Number of iterations to convergence: 320 
## Achieved convergence tolerance: NA
```

通过结果可以看到，虽然和之前采用手动方法判定的结果比较接近，但是还是略有差异，可以看一下他们各自的结果同测量值的重合程度：


```r
plot(lrc_Q, lrc_A)
lines(lrc_Q,  predict(lrcnls2), col="red")
lines(lrc_Q,  predict(lrcnls_manual), col="blue")
```

![(\#fig:mcomp)两种方法结果的对比展示](bookdown_files/figure-latex/mcomp-1.pdf) 


图 \@ref(fig:mcomp) 可以看到，使用 `nls2` 的拟合结果似乎和测量值更匹配，当然这只是第一印象，后续的判断还要进一步通过 F 检验、 AIC、BIC 等统计方式才能判定。

## 小结 {#sum_start}

采用如上三种方式都可以有效的解决起始值的问题，`nlsLM` 操作上更易实现，对初始值的大小不敏感，但设置不能太离谱，否则仍然会报错。作图比对法操作上更麻烦一些，但是这种方式一定能得出合理的初始值设置。采用 `nls2` 类似于将手动作图方式自动化，类似于 SPSS 中非线性拟合中需要给出一个初始值的范围，且该范围不能过大。如有一定的经验，操作起来将非常迅速。

需要注意的是，这三种方法结合起来使用会更好，例如，即使使用 `nlsLM` 的结果不合理，也可以参考他们参数的范围（部分结果也可能是差异显著），然后将这些结果用于手动作图判定参数或者 `nls2` 中判定参数范围，或者使用作图法确定大致的范围，将该范围输入到 `nls2` 中，这样会节省时间，也更加方便。


\cleardoublepage

<!--chapter:end:11-start-values.Rmd-->

# LI-6800 的数据分析 {#anay_6800}

## 数据格式 {#data6800}

~~LI-6800 的数据前处理同 LI-6400 相同，如表 \@ref(tab:head6800) 所示的样式。~~

并非此处格式无效，只是有更简洁的数据读取方式，见 \@ref(excel-recompute-68)。


\begin{longtable}[t]{rrrlrr}
\caption{(\#tab:head6800)推荐 LI-6800 整理后数据样式}\\
\toprule
obs & time & elapsed & date & TIME & E\\
\midrule
1 & 1471425750 & 0.0 & 20160817 17:22:30 & 1471425726 & 0.0007614\\
2 & 1471425855 & 104.5 & 20160817 17:24:14 & 1471425829 & 0.0009221\\
3 & 1471426153 & 403.1 & 20160817 17:29:13 & 1471426144 & 0.0009900\\
4 & 1471426244 & 494.0 & 20160817 17:30:44 & 1471426235 & 0.0010249\\
5 & 1471426335 & 585.1 & 20160817 17:32:15 & 1471426326 & 0.0010632\\
\addlinespace
6 & 1471426426 & 676.0 & 20160817 17:33:46 & 1471426417 & 0.0011190\\
\bottomrule
\end{longtable}

## LI-6800 与 LI-6400 使用时的差别 {#dif}

`plantecophys` 使用时建立在 LI-6400XT 基础之上的软件包，因此在 LI-6800 代码中，需要改动的是 fitaci、fitacis 及 fitBB 中的 varnames 选项，也就是将 LI-6400XT 的表头改为 LI-6800 的表头。
以 fitaci 函数为例：


```r
 fitaci(aci, varnames = 
        list(ALEAF = "A", Tleaf = "Tleaf", Ci = "Ci", 
          PPFD  = "Qin", Rd = "Rd"))
```


---

> 注：我个人觉得这样使用对于 LI-6800 来讲十分不方便，修改了一下 `plantecophys`，专门给 LI-6800 使用，具体可参考:

1.[使用示例](https://mp.weixin.qq.com/s?__biz=MzU4ODI3NjkzMg==&mid=2247484404&idx=1&sn=d239f828729afb6a3f6e99840f2ae580&chksm=fdde765fcaa9ff495d2bccaeafbdf7e2f277d049dc5c6adb6dda732acba29ab89cb166947084&mpshare=1&scene=1&srcid=1117xK7YP4teJLDvMqin9Sgf&sharer_sharetime=1605599821632&sharer_shareid=54a1abf54404775401f1afca5de45145&key=bf89f9a930e5471c34caab372884db37d25c335995749574e006f066cb416fb41ba01a2f69a3731482d05a92f7dc00398f5d8d1f847a524e226ab794caff8ea178345558686bb885d13916942d992b349b14905c8e7853860700ff1285c5a18a21dbf60af892e0ded188ed442d1d0168dd7c06de8f984d4ae2a98035b2592292&ascene=1&uin=MzMzODgyNDY5Mw%3D%3D&devicetype=Windows+10+x64&version=6300002f&lang=zh_CN&exportkey=A0TNUwEPJDhBuUPV1y9veGY%3D&pass_ticket=mdhrxAzIfs74t2CBHIkfTz7IvbpYSartOOH1ofi44h03G2TjZhlDYLS8QC1%2F0ynL&wx_header=0)

2. [plantecophys2](https://github.com/zhujiedong/plantecophys2)

## 光响应曲线注意事项 {#notice}

光响应曲线的拟合相对简单，仅需要光强和光合速率的值，其中需要修改的部分仅为光强的赋值部分，在文件名一致的前提下，修改如下代码即可：


```r
lrc_Q <- lrc$Qin
lrc_A <- lrc$A 
```

\cleardoublepage

<!--chapter:end:12-1-plantecophys-6800.Rmd-->

## LI-6800 RACiR\texttrademark 的测量与拟合 {#racir68}

在评估作物性状时，V$_{cmax}$ 及 J$_{max}$时非常有用，传统的 A–Ci 曲线测量要求植物叶片要在一定浓度 CO$_{2}$
下适应几分钟后完成测量，这样的测量有几个缺点：

* 测量时间长，一条曲线至少需要 20 – 30 min，样本量多，重复多时，这种方法几乎没有可行性。
* 整个测量过程中，时间长，酶的激活状态会有变化，叶绿体会移动，气孔的开度也会发生变化。

而 LI-6800 独有的 auto control 功能在算法上允许用户自定义 CO$_{2}$ 的起始浓度和种植浓度、变化方式（线性或其他）、所花费的时间，再加上其
IRGAs 极快的响应频率，使得短时间内的 A–Ci 的测量成为现实，即快速 CO$_{2}$ 响应曲线 RACiR\texttrademark
测量实验，该功能使得 5 min 内测量 A–Ci 曲线成为可能。该方法的实现可参考  @stinziano2017
的文章。

@stinziano2018 针对 RACiR\texttrademark 技术的疑问做了解答并提出了准确测量的建议，概括如下：

* 首先，采用 100 ppm/min 的变化速率是与标准方法重合度最高的测量。
* 其次，明确研究问题，目前已有研究表明Vcmax 与 Jmax 的计算结果与标准测量方法结果无显著差异。
* 任何条件的改变，都需要做空叶室校准，例如：流速，气体浓度变化方向、温度，斜率等。
* 空叶室校准与叶片测量采用严格的同一次校准，因为 IRGA  的漂移，需要再次匹配时，或者环境条件改变时，需要重新做空叶室校准。是否需要匹配，可通过不加叶片的最初状态查看，此时 A 值应接近为0，reference 和 sample 气体浓度读数接近相等。
* IRGA 分析器使用 5 此多项式进行校准，推荐使用 1 次到 5  次多项式进行拟合，然后根据 BIC 指数来确定最合适的空叶室校准系数（即非参数拟合的模型选择的问题）。
确定最合适的浓度变化范围。通常需要去掉最初和最后 30 s的数据。
* 最小化校准和测量值之间的水分摩尔分数差异。甚至有可能需要控制 reference 或 sample 的水的摩尔分数而不是 Vpdleaf。
通过预实验来确定最合适的 $CO_2$ 变化范围和随时间的斜率。

**本文之后关于racir 的空叶室校正部分全部不再支持，因为LI-6800 1.5之后的系统版本，关于两个分析器在快速变化环境条件下的校正均由仪器完成，分析工作直接使用 `plantecophys` 等软件拟合即可。**

例如下面的数据采用了 1.5.02 的 BP 程序 `DAT_CO2_Continuous.py`，直接测量的 RACiR 数据，结果如下：


```r
remotes::install_github("zhujiedong/plantecophys2")
```



```r
adyn <- read.csv("data/racir_adyn.csv")

adyn_fit <- plantecophys2::fitaci(adyn)
```

```
## Registered S3 methods overwritten by 'plantecophys2':
##   method         from        
##   coef.BBfit     plantecophys
##   coef.BBfits    plantecophys
##   coef.acifit    plantecophys
##   coef.acifits   plantecophys
##   fitted.acifit  plantecophys
##   plot.acifit    plantecophys
##   plot.acifits   plantecophys
##   print.BBfit    plantecophys
##   print.BBfits   plantecophys
##   print.acifit   plantecophys
##   print.acifits  plantecophys
##   summary.acifit plantecophys
```

```
## 
## Formula: ALEAF ~ acifun_wrap(Ci, PPFD = PPFD, Vcmax = Vcmax, Jmax = Jmax, 
##     Rd = Rd, Tleaf = Tleaf, Patm = Patm, TcorrectVJ = Tcorrect, 
##     alpha = alpha, theta = theta, gmeso = gmeso, EaV = EaV, EdVC = EdVC, 
##     delsC = delsC, EaJ = EaJ, EdVJ = EdVJ, delsJ = delsJ, Km = Km, 
##     GammaStar = GammaStar)
## 
## Parameters:
##       Estimate Std. Error t value Pr(>|t|)    
## Vcmax 26.99477    0.11547  233.79   <2e-16 ***
## Jmax  46.99725    0.11780  398.96   <2e-16 ***
## Rd     1.38036    0.02146   64.31   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.1347 on 295 degrees of freedom
## 
## Number of iterations to convergence: 7 
## Achieved convergence tolerance: 5.627e-06
```

```r
plot(adyn_fit)
```

![(\#fig:adyn_plot)使用 Adyn 来进行RACiR 的直接测量数据](bookdown_files/figure-latex/adyn_plot-1.pdf) 

 \@ref(fig: dyn_plot) 展示的使用 LI-6800 自带程序测量的数据，无需修正，测量作物为早春温室的水稻。


## racir 软件包实现 RACiR\texttrademark 数据分析  {#racir_pkg}

~~可能软件包作者没有更改叶面积的需求，他的软件包只支持原始数据的处理，但这对有更改需求的客户来讲没那么友好，而且这样导致了大量代码的重复，我根据作者原来的代码，结合我自己数据分析的习惯，重新制作了一份软件包，下面代码实现是基于我的软件包实现的，如有需求，也可使用作者原来的代码实现~~


```r
devtools::install_github("zhujiedong/racir")
library(racir)
```

软件包的函数很多，但可以分为下面几类：

* 首先使用 `find_cut` 来查看使用数据的范围（排除野点）。

* 使用 `racircheck` 来检查校正数据 (非必须，但建议进行数据质量检查)

* 使用 `racircal` 进行数据的校准

### 实现方法 1 {#racir_pkg_1}

手上暂时没有原始数据格式的 racir 数据，因而现在使用以前的 csv 数据进行操作（空叶室测量数据和带叶片测量数据转为 csv 格式）。

首先检查空叶室校准：


```r
library(racir)
# only check the empty chamber data here
em1 <- read.csv("./data/racirem/em-1.csv")
le1 <- read.csv("./data/racirle/le-1.csv")
find_cut(em1)
```

上述代码查看参比室 $CO_2$ 浓度在两者之间的数据，确定后面代码使用的 `mincut`, `maxcut` 范围。此处我选择的范围如下问代码：


```r
racircheck(em1, mincut = 21 , maxcut = 463 )
```

没有问题可直接进行校准


```r
x <- racircal(em1, le1, mincut = 21, maxcut = 463)
plot(x$CO2_r, x$A)
```
前两个参数分别为空叶室数据，带叶片测量数据，后面的最大最小值为 `racircheck` 确定，剩余工作就是使用 `plantecophys` 进行分析，在此不再重复这个过程。

**注意：RACiR 的本意是表型研究中求 Vcmax 和 Jmax，因而，请忽略 Rd 等参数的计算结果**

### 实现方法 2 {#racir_pkg_2}

#### 从低到高的 $CO_2R$ 变化 {#low-to-high}

以上的实现方法是基于肉眼判断，删除不需要的数据，自动化程度较低，同时不适合 @Coursolle12019 这样的方法:


直接批量删除是不可取的。容易出错或者残留无效数据，针对这个情况我进行的修改和测试为：使用 BP 进行测量，目前我已经完成 BP 相关的程序，本节的示例则是基于我实测的验证数据来进行（相关测试已经录好视频，可能后面公司公众号会推，如有 BP 自动测量 RACiR 的需要，请跟我们公司联系）。这个程序也是基于 @Coursolle12019 的方法，测量步骤和修改如下：


1. 在接近外界的 $CO_2$ 浓度下进行诱导，控制 reference 的 $CO_2$ 420 ppm ，控制 $H_20$ 为 20 $mmol \cdot mol^{-1}$，**此处不做修改。**

2. 使用自动控制，在 2 min 时间内，将  $CO_2$  的 reference 气路浓度，下降到 20，然后等待 10-15 s 的时间。**此处的修改为，不记录这些数据，减少数据处理的麻烦。** 例如 \@ref(fig: racir-steady-start) 中三角形区域的数据。

3. 运行自动控制，将 $CO_2$ 的 reference 浓度由 20 上升到 1520 ppm （仅作为测试范围，并非绝对），**本步骤不做修改，记录数据，此为所需要数据。**

**实际上主要的修改就是将这个步骤自动化，需要人为操作的步骤仅仅是点 start 按键启动整个测量。**

对于软件包的修改：

主要的修改：

* 独立的 `xls_read` 函数用于读取 excel 格式数据并修改叶面积。
* 独立的“清洗”空叶室数据的函数 `tidy_data_empty`。
* 独立的“清洗"带叶片测量数据的函数 `tidy_data`。
* 将数据的清洗使用 A 值的一个上限和一个下限来解决。

使用的方法如下（有使用标准 ACi 测量数据的比较内容）：

##### 查看相邻数据的差值设置 {#delta_max-check}

数据的清洗其实比较简单，主要利用相邻的两个测量 A 值的差值来进行，为方便确定差值，针对空叶室和带叶片测量的数据，单独设定了两个函数，来确定合理的过滤范围，这样说太抽象，我们导入数据后直接看图：

**1. 导入数据：**

此函数本质上是 `xlconnect_read`，如无需修改叶面积，则使用默认值。如果需要修改叶面积，需要输入 S 参数，叶面积修改值必须和 A 的值一一对应，否则因为叶面积错误，则导致其他的错误，例如上面修改叶面积为原来的一半：


```r
library(racir)
library(plantecophys)

empty <- xls_read("data/racir-empty.xlsx")
leaf <- xls_read("data/racir-leaf.xlsx")

#  标准的aci数据
aci <- xls_read("data/aci-curve.xlsx")

half_leaf <- xls_read("data/racir-leaf.xlsx", S = leaf$S/2)
head(half_leaf$A/leaf$A)
tail(half_leaf$A/leaf$A)
```

修改面积仅作演示用，如无需修改，则不要输入面积选项。

**2. 关于空叶室的数据过滤范围的选取:**


```r
check_delta_empty(empty, delta_max = 0.5, lower_A = -1)
```
这里首先不要被第二幅图的波动所迷惑，主要是坐标轴差异太小。然后第一幅图中，这里设定了一个最大最小光合速率的范围（阈值，upper_A 和 lower_A），默认最大值是 2 以及最小值是 -2，上面我使用了最小值为 -1，也就是删除小于 -1 的光合速率，最大值 2 并不是删除，二是寻找 -2 < A < 2 范围的数据（确定起始测量时 A 开始变为合理的范围），从这里面找一个最小值，保留这一行的数据到测量数据结束。 另一个阈值删除相邻两个A值过大差值的数据的阈值（`delta_max`），对于这个阈值，LI-6800 其实并不敏感，因为本身数据非常稳定。

**3. 关于带叶片测量的数据过滤范围的选取:**


```r
check_delta(leaf, delta_max = 0.5, lower_A = -1)
```
带叶片的数据同上，没有特殊的参数，从右图的数据来看，他在修正之前的光合速率，符合我们的预期。

**4. 确定范围后数据的清洗:**


```r
tidy_leaf <- tidy_data(leaf, delta_max = 0.5, lower_A = -1)
tidy_empty <- tidy_data_empty(empty, delta_max = 0.5, lower_A = -1)

# 修正带叶片测量数据
z <- correct_leaf(tidy_empty, tidy_leaf)
```
**4. 拟合后的数据：**


```r
fit_racir <- fitaci(z,
               varnames =
                 list(
                   ALEAF = "A",
                   Tleaf = "Tleaf",
                   Ci = "Ci",
                   PPFD  = "Qin",
                   Rd = "Rd"
                ), alpha = 1,
               theta = 1.2)
fit_normal <- fitaci(aci,
               varnames =
                 list(
                   ALEAF = "A",
                   Tleaf = "Tleaf",
                   Ci = "Ci",
                   PPFD  = "Qin",
                   Rd = "Rd"
                 ), alpha = 1,
               theta = 1.2)
```

RACiR 的结果：



ACi 的结果：



结果的比较：




此处的数据和视频里的数据以及函数的参数略有差别，因为我修改了一下 `racir` 软件包的过滤条件，使得筛选数据更符合要求。本身因为不是同一时间测量，测量 RACiR 时叶片的气孔导度略到，因此，其 $V_{cmax}$ 略大符合预期。

数据的重合程度 （实际上 RACiR 数据点要浓密的多，我使用了透明度为 80%，这样看上去仍然颜色非常深，这是 RACiR 采集数据点达到几百个的原因。 ACi 测量点为 50-2000 ppm，RACiR 为 20-1520 ppm）：


```r
library(RColorBrewer)
library(latex2exp)

line_col <- brewer.pal(2,"Set1")

racir_col <- scales::alpha(line_col[1], 0.2)
aci_col <- line_col[2]

plot(
  z$Ci,
  z$A,
  cex = 2.3,
  col = racir_col,
  pch = 20,
  xlim = c(50, 1400),
  ylim = c(-1, 38),
  xlab = TeX('$C_i$ $(\\mu mol \\cdot mol^{-1})$'),
  ylab = TeX('A $(\\mu mol \\cdot mol^{-1})$')
)
points(aci$Ci,
       aci$A,
       cex = 2.3,
       col = aci_col,
       pch = 20)

legend(
  10,
  35,
  legend = c("RACiR","ACi"),
  col = c(racir_col, aci_col),
  pch = 20,
  bty = "n",
  pt.cex = 2.3
)
```
#### 从高到低的 $CO_2R$ 变化 {#high-to-low}

以上内容是按照 @Coursolle12019 的方式，从低浓度到高浓度的控制，就我个人而言，我感觉无需从高到底测试，但是就实验而言，可以仿照 @Coursolle12019 的方式，从高浓度到低浓度做一次测试，来检验哪种方式更合适。于是在测试完上面的数据的第二天又重新测试了反向的测试，为了数据处理的方便，在此将反向实验的数据处理方式写一下。


```r
#导入数据
empty <- xls_read("data/racir-empty-reverse.xlsx")
leaf <- xls_read("data/racir-leaf-reverse.xlsx")
```

浓度从 420 上升至 1520 ppm 的时间应相对充分，否则会在测量刚开始时有一个剧烈的光合速率的变化，需要在两个 AUTOENV 控制之间增加等待的时长，也就是确保 $CO2_S$ 气路的气体冲刷时间足够。

基本方式没有变化，首先利用图形检验过滤噪音的阈值（实际敏感度低，因为 LI-6800 不是一般的稳）

**1.检验空叶室数据的测量**


```r
check_delta_empty(
  empty,
  delta_max = 0.5,
  upper_A = -1.5,
  lower_A = -5,
  reverse = TRUE
)
```

这里的设置与正向测量相似，只是需要指定反向 `reverse = TRUE`，对于反向测量的空叶室，数据在稳定后肯定是马上下降为负数，我们指定一个略宽的 A 值范围，然后取其最大值所在行到数据行的末尾，也就是 \@ref(fig:empty-delta-max-rev) 右侧的数据。对于相邻的两个 A 值的差值，从右图也可以看出来，LI-6800 真的不敏感，除非测量过程发生意外状况，不然这个数值完全可设置的略大。我使用 0.5 是因为想展示不过滤的数据点的情况，非常平稳。


**2.检验带叶片测量数据的测量**


```r
check_delta(
  leaf,
  delta_max = 0.5,
  upper_A = 30,
  lower_A = 29,
  reverse = TRUE
)
```

过滤方式相同空叶室，数据仍然是非常稳定。我们看一下最终结果：


```r
leaf_rev <- tidy_data(
  leaf,
  delta_max = 0.5,
  upper_A = 30,
  lower_A = 29,
  reverse = TRUE
)

empty_rev <- tidy_data_empty(
  empty,
  delta_max = 0.5,
  upper_A = -1.5,
  lower_A = -5,
  reverse = TRUE
)

zrev <- correct_leaf(empty_rev, leaf_rev)

fit_racir_rev <- fitaci(zrev,
               varnames =
                 list(
                   ALEAF = "A",
                   Tleaf = "Tleaf",
                   Ci = "Ci",
                   PPFD  = "Qin",
                   Rd = "Rd"
                ), alpha = 1,
               theta = 1.2)
```

RACiR 的结果：




结果的比较：



因为季节原因，测量相比正向测量更换了枝条（客观原因，11 月帝都的室外，实在不好找合适的植物，采取剪枝条测量的方式）。

### 数据的批量处理 {#racirbatch}

#### readphoto 导入 {#imbyphoto}

如果数据的导入是使用 `readphoto` 批量处理，那么所在数据文件有 files 列，作为不同文件数据的区分，那么可以使用向量化处理方式实现数据的分析，参考如下代码：


```r
library(readphoto)
# read all empty data set
all_empty <- read_bat_6800("./empty_data")
# read all leaf data set
all_leaf <- read_bat_6800("./leaf_data")
listem  <-  split(all_empty, files)
listle <-  split(all_leaf, files)
cordata <- purrr::map2(listem, listle, racircal, mincut = 21, maxcut = 463)
```
所有的数据均保存在 cordata 中，特别注意 **此处要求 listem 与 listle 长度相同，因此，如果一个空叶室对多个带叶片测量数据，请将空叶室测量文件复制几份（注意对应顺序），使其每个带叶片测量的数据文件和空叶室测量数据文件数量相同，并且一一对应，例如 emtpy-1-1 数据文件对应 leaf-1-1**，文件命名前即制定好命名规则，方便后续数据处理。

#### 处理大量 csv 文件 {#imbycsv}


```r
# suppose leaf and empty data in 2 files in disk D:
le=list.files("d:/racirle/")
em=list.files("d:/racirem/")

# construct the path of these csv files
pathle = paste0("d:/racirle/", le)
pathem = paste0("d:/racirem/", em)

# import all leaf data and empty data separately
xx <- lapply(pathle, read.csv)
yy <- lapply(pathem, read.csv)

# correct all the data
all_correc <- purrr::map2(yy, xx, racircal, mincut = 21, maxcut=463)

#  export all data to csv files, these file have the same name with leaf data
dirc <- dir.create("d:/data/")
finame <- "d:/data/"
finame <- paste0(finame, le)
purrr::walk2(all_correc,  finame, write.csv)
```

以上代码仅供参考，主要是实现批量导入并校正，在批量将校正后数据导出为 csv 文件，为方便，将导出的文件名字命名为同原来带叶片测量数据相同的文件名，。

## 批量计算 $V_{cmax}$ 和 $J_max$ {#fitbatracir}


```r
#use csv data above
csvdata <- lapply(all_correc, plantecophys::fitaci, 
    varnames = list(ALEAF = "A", Tleaf = "Tleaf",
    Ci = "Ci", PPFD= "Q", Rd = "Rd"), fitmethod ="bilinear")

# use readphoto data above
photodata <- lapply(cordata, plantecophys::fitaci, 
    varnames = list(ALEAF = "A", Tleaf = "Tleaf",
    Ci = "Ci", PPFD= "Q", Rd = "Rd"), fitmethod ="bilinear")
```

批量分析同样使用向量化来进行，避免了循环，效率比较高，也可以将拟合结果 `coef` 中的 $V_{cmax}$ 和 $J_max$ 结果批量导入文件，参考 @\ref(imbycsv) 部分的代码。


\cleardoublepage

<!--chapter:end:12-2-autoracir.Rmd-->

## LI-6800 RACiR\texttrademark 簇状叶的测量与拟合 {#racir-conifer}

@Coursolle12019 测量了簇状叶黑云杉和香脂冷杉两种簇状叶植物的 RACiR，其中的试验方法和结论值得在测量时借鉴，测量方法上：

- 簇状叶室体积远大于荧光叶室和其他叶室，使用的 $CO_2$ 的变化为： 15 min 内从 20 ppm 到 1520 ppm 的变化，即变化的速率为 $100 ppm \cdot min^{-1}$。但也测试了 200 - 800 ppm的部分曲线。

- 拟合使用了测量的 Rd，测量方法为：控制 reference 气路在 420 ppm 的 $CO_2$ 和 22 $mmol \cdot mol^{-1}$ 的 H~2~O 浓度，控制温度为 25 C，诱导后测量 Rd。

得到了一些有帮助的结论：

- 使用更大的叶室测量 RACiR 是可行的（36 $cm^2$），叶室环境的控制需要通过预实验来确定。

- 该实验使用的 ACi 曲线测量时间在 30 到 36 min，而 RACiR 使用的**完整的二氧化碳的浓度范围**时，曲线耗时最大的时间接近 22 min。但使用 200 - 800 ppm 范围的变化，则时间可以下降 50%，**这些部分范围的测量则可以应用于植物胁迫和表型平台的研究**。

- 实验结果证明只要 match 的调整值保持不变即无需进行空叶室校准（也就是无需匹配的意思，实际的时间间隔取决于仪器的状态），但最新的 range match 功能可有效的增加空叶室校准的时间间隔（新功能，作者试验时尚未推出该功能）。

- 作者建议最好测量暗呼吸的速率，以获得最佳的 Vcmax 和 Jmax 计算结果。如果有第二台光合仪来测量则可有效的缩短测量时间。

### 数据的拟合 {#conifer-fit-racir}

#### 数据文件的命名 {#name-files}

因为作者读取数据和修正数据都依赖于正则表达式，因此，所有文件的命名规则必须满足以下的要求：

- 空叶室校准的文件必须以 "mpty" 起始

- RACiR 的文件必须以 "fast" 起始

- 常规 ACi 测量必须以 "slow" 起始

- Rd 测量数据文件必须以 "dark" 起始

例如作者示例数据分别命名为：

Empty_2.xlsx，Fast.xlsx， Slow.xlsx， Dark.xksx，实际使用的正则表达式为:

```r
pattern_empty       = "^(mpty)+.xls",      
pattern_rapidACi    = "^(fast)+.xls",      
pattern_standardACi = "^(slow)+.xls",      
pattern_dark        = "^(dark)+.xls",
```
如不满足要求，自然会出现错乱。准备好命名的数据文件之后，那么可以把他们放在一个文件夹内。

#### 匹配的问题 {#match-time}

若进行了匹配，要重新开始空叶室校准，RapidACi 软件包采用的规则是，使用 Match 分组下的 time来区分，首先使用该时间和空叶室一致的 RACiR 数据配对校准，若无一致的数据，则选择该时间和 RACiR 测量相差最短的空叶室数据来校准 RACiR 数据。

### 拟合过程 {#conifer-racir-fit}

作者给出了详细的代码和数据，此处不再重复和演示，请参考：

[github 演示](https://github.com/ManuelLamothe/RapidACi)

[发表文章的附加材料](https://www.frontiersin.org/articles/10.3389/fpls.2019.01276/full#supplementary-material)

**注：今天测试了他附加材料的代码，有可能遇到问题，或者有一些注意事项，请参考下面的代码（本地已经有数据了，因此并未执行下载的代码，改为本地读取）。**


```r
#安装及加载大量的依赖
remotes::install_github("ManuelLamothe/RapidACi")
if (!require(tidyverse))
  install.packages("tidyverse")
if (!require(readxl))
  install.packages("readxl")
if (!require(XLConnect))
  install.packages("XLConnect")
if (!require(plantecophys))
  install.packages("plantecophys")

library(RapidACi)
library(XLConnect)
library(tidyverse)
library(readxl)
library(plantecophys)




#build_list:
#测量最好直接存放 “data”文件夹下
#然后建立了4个匹配规则，分别是：
#pattern_empty = "(mpty).*\\.xls",
#pattern_rapidACi = "(fast).*\\.xls",
#pattern_standardACi = "(slow).*\\.xls",
#pattern_dark = "(dark).*\\.xls",

#如果后面需要修改叶面积，那么需要提供额外的dataframe，其中必须有两列：
# sample_ID, 以及其中的一列必须命名为 LeafArea_mm2

# 默认 match 的 time 列为 BN，若有其他用户自定义选项，请修改默认参数
# 空叶室校准的选择原则为：默认选择距离racir测量时间最近的空叶室（empty开头的）测量
# 进行校准


list_files <- build_list()
list_files$sample_ID[1] <- "fastdemo"

# 了解一下 build_list 的作用
list_files

results <- Rapid_aci_correction(list_files)
results

# 诊断校准的结果
# 如果报错：unable to start png() device
# 那么可能是权限的原因无法建立 figure 文件夹
dir.create("./figure/", showWarnings = FALSE)
# delta 为 A diff的差值，默认设定了 0.05
diagnose_sample(results, sample_name = "fastdemo")

# 添加 racir 
Raci <- results[["fastdemo"]]$Raci

# range sheet measurements 绝对引用 I17
Dark <-
  read_excel(
    "./ds/Dark.xlsx",
    range = "Measurements!I17",
    col_types = "numeric",
    col_names = FALSE
  )

Raci$Rd <- pull(Dark) 

fRaci <- fitaci(Raci, useRd=TRUE) 
par(mgp=c(2.5,1,0), family="serif", ps=14) 
plot(fRaci, las=1,xlim=c(0,1200), ylim=c(0,30)) 
```


\cleardoublepage

<!--chapter:end:12-3-autoracir-conifer.Rmd-->

## RACiR\texttrademark 分析的手动实现 {#racir68_exam}

以下内容是我之前写的内容，部分代码的实现和上述软件包相似，仅供参考，尤其是当您使用 `racir` 软件包报错时，可以参考下文代码手动实现 RACiR 数据的分析。


```r
# 加载需要的软件包  ---------------------------------------------------------------

library("plantecophys")

#准备数据 ------------------------------------------------------------------

#-----------------------------------------------------
# 也可以使用上文中的 xls_read() 来直接读取 excel 数据
#-----------------------------------------------------
empty_uc <- read.csv("./data/racir-csv/uncorr_emp500.csv")
leaf_uc <- read.csv("./data/racir-csv/uncorr_leaf500.csv")
aci <- read.csv("./data/racir-csv/aci_ex.csv")

# 防止读入空白行
empty_uc <- subset(empty_uc, obs > 0)
leaf_uc <- subset(leaf_uc , obs > 0)
aci <- subset(aci, obs > 0)

# 空叶室校准的系数计算 --------------------------------------------------------------

# 观察空叶室未校准数据reference对A的图形
plot(empty_uc$CO2_r, empty_uc$A)
# 选取线性部分用于校准
locator()

# 执行locator命令后，在上图中的目标位置选点，
# 选好后按 esc 可以返回所选点的坐标（选点即为在
# 预期位置鼠标单击）
# 根据上面的点，利用二氧化碳的值过滤掉不需要的数据
# 只要在线性范围内选点，拟合结果相差很小

empty_ct <- empty_uc[which(empty_uc$CO2_r >
  45.28 & empty_uc$CO2_r < 459.12), ]
plot(empty_ct$CO2_r, empty_ct$A)

# 采用 1~5 次多项式分别拟合
cal1st <- lm(A ~ CO2_r, data = empty_ct)
cal2nd <- lm(A ~ poly(CO2_r, 2), data = empty_ct)
cal3rd <- lm(A ~ poly(CO2_r, 3), data = empty_ct)
cal4th <- lm(A ~ poly(CO2_r, 4), data = empty_ct)
cal5th <- lm(A ~ poly(CO2_r, 5), data = empty_ct)
# 利用 BIC 找出最合理的校准方程
bics <- BIC(cal1st, cal2nd, cal3rd, cal4th, cal5th)
# noquote也就是没引号，成为名字
best <- noquote(rownames(bics)[bics$BIC == min(bics$BIC)])
best


# 校准带叶片测量的数据 --------------------------------------------------------------

leaf_uc$A <- leaf_uc$A - predict(cal4th, leaf_uc)
leaf_uc$Ci <- ((leaf_uc$gtc - leaf_uc$E / 2) * leaf_uc$CO2_s -
                 leaf_uc$A) / (leaf_uc$gtc + leaf_uc$E / 2)

# 对校准后的数据进行作图，查看校准效果
plot(leaf_uc$CO2_r,
     leaf_uc$A,
     pch = 2,
     ylim = c(-20, 40))

# 选取带叶片测量的有效数据的范围
locator()

leaf_ct <- leaf_uc[which(leaf_uc$CO2_r > 13.6 &
   leaf_uc$CO2_r < 471), ]
plot(leaf_uc$CO2_r, leaf_uc$A)


# 拟合 ----------------------------------------------------------------------
racir <- fitaci(
  leaf_ct,
  varnames =
    list(
      ALEAF = "A",
      Tleaf = "Tleaf",
      Ci = "Ci",
      PPFD  = "Qin",
      Rd = "Rd"
    ),
  Patm = 84.09
)

slow <- fitaci(
  aci,
  varnames =
    list(
      ALEAF = "A",
      Tleaf = "Tleaf",
      Ci = "Ci",
      PPFD  = "Qin",
      Rd = "Rd"
    ),
  Patm = 84.09
)

# 查看拟合数据
racir$pars
slow$pars

# 对快速曲线作图拟合结果进行查看
plot(racir, linecols = c("green", "blue", "red"))
```

注意，注意数据表头的大小写，此处代码中，为处理数据的方便，我更改了大小写，分析自己的数据时需要注意。

\cleardoublepage

<!--chapter:end:12-4-man-racir.Rmd-->

## 多个速率的 RACiR 曲线研究 {#multi1}

自 RACiR 技术诞生以来，极大的缩短了 Vcmax 以及 Jmax 的测量时间(@stinziano2017)，但也引起了一系列争议，作者也对业内的质疑进行了一一的解答 (@stinziano2018)，但除了因为时间长短导致酶活性，叶绿体位置等差异外，RACiR 还能说明哪些问题呢？@stinziano2019 最新的研究给出一系列结论：

* 扩散限制（$CO_2$ 总导度） 和光呼吸导致了表观上的标准 ACi 曲线和 RACiR 测量之间的偏差，表明他们的差异是由**生物因子引起，而非仪器导致的人为误差**。

* 上述原因导致的二者之间的偏差，如果不进行修正，那么将显著的低估 $\Gamma^*$, 除非使用多个速率的 RACiR 来修正。

* 较高速率的 RACiR 曲线会增大其与标准曲线之间的偏差，但这个差距在无光呼吸的条件下会减小。

* 因为光呼吸和气体扩散限制与物种相关，结合以上结论，可以使用多个速率的 RACiR 来估算对 $CO_2$ 的总导度以及相对量的光呼吸速率。

一些可能的方向：

* 扩散限制影响 Cc 速率的变化，说明对具有较高总阻力与 $CO_2$ 比值的物种，例如针叶物种，C4 植物，较高的阻力导致 RACiR 与 标准 ACi 测量斜率更大的差异，或者测量的前提假设被破坏。

* RACiR 可检测到代谢中 $CO_2$ 的滞后性，各种滞后性的检测对标准 ACi 测量也具有指示性。

文中利用 R 实现了光呼吸之后模型和气体扩散限制模型，本文内容主要对文献中附录材料的源码进行解释：

### 光呼吸滞后模型 {#multi2}

为测试光呼吸的滞后性，作者使用一系列预先设定的参数，模拟了一条 ACc曲线，假定 Rubisco 激活状态为 100%，并且在整个测量过程中气孔导度是不变的。然后使用这些参数来模拟 RACiRs 曲线，并且假定光呼吸分别需要 0, 15, 30, 60, 120 或 300 s 来对变化的 $CO_2$ 进行响应，在实际效果上，这意味着 Cc 在最初的 0, 15, 30, 60, 120 或 300 内是不变的，最后我们对 $\Gamma^*$ 和 Ci 使用线性回归进行计算。

#### 构造基础数据 {#multi3}

模型第一步，则是对需要使用的参数，根据文献和实际情况进行赋值，具体内容参考代码注释。


```r
library(ggplot2)
library(plyr)
```

```
## 
## Attaching package: 'plyr'
```

```
## The following object is masked from 'package:purrr':
## 
##     compact
```

```r
library(gridExtra)

# 对图例使用自定义颜色
gg_color_hue <- function(n) {
  hues = seq(15, 375, length = n + 1)
  hcl(h = hues, l = 65, c = 100)[1:n]
}

#Maximum Rubisco carboxylation rate in umol m-2 s-1
Vcmax <- 110

#Maximum Rubisco oxygenation rate in umol m-2 s-1;
#Ratio from Bernacchi et al. 2001. PCE 24:253-259
Vomax <- 0.29 * Vcmax
#Dark respiration in umol m-2 s-1
R <- 2
#Michaelis-Menten constant for Rubisco carboxylation in umol mol-1
Kc <- 404.9
#Michaelis-Menten constant for Rubisco oxygenation in mmol mol-1
Ko <- 278.4
#oxygen concentration in mmol mol-1
O2 <- 210
Kco <- Kc * (1 + O2 / Ko)
#Boundary layer conductance in mol m-2 s-1
BLC <- 2
#stomatal conductance in mol m-2 s-1
gsw <- 0.4
#mesophyll conductance in mol m-2 s-1
gm <- 1
#Chloroplastic CO2 in umol mol-1
Cc <- as.numeric(c(25:400))
#oxygenation rate in umol m-2 s-1
vo <- Vomax * O2 / (O2 + Ko * (1 + Cc / Kc))
#carboxylation rate in umol m-2 s-1
vc <- Vcmax * (Cc) / (Cc + Kco)
#Net CO2 assimilation in umol m-2 s-1
A <- vc - 0.5 * vo - R
#Apparent CO2 assimilation rate in umol m-2 s-1
Aapparent <- vc - 0.5 * vo
#Intercellular CO2 in umol mol-1
Ci <- A / gm + Cc
#Boundary layer CO2 in umol mol-1
Cb <- A / gsw + Ci
#Reference CO2 in umol mol-1
Cr <- A / BLC + Cb

#根据Cc浓度的个数，构造向量
Counter <- as.numeric(c(1:length(Cc)))
#也就是以秒计算的cr与时间的模型
RateCrmodel <- lm(Cr ~ Counter)
#转化为分钟的cr的斜率
RateCr <- coef(RateCrmodel)[2] * 60

#转换为分钟的边界层导度斜率
RateCbmodel <- lm(Cb ~ Counter)
RateCb <- coef(RateCbmodel)[2] * 60

#转换为分钟的ci的斜率
RateCimodel <- lm(Ci ~ Counter)
RateCi <- coef(RateCimodel)[2] * 60

#转换为分钟的Cc的斜率
RateCcmodel <- lm(Cc ~ Counter)
RateCc <- coef(RateCcmodel)[2] * 60 
```

### 光呼吸滞后性代码 {#code-photoresp}

下面代码的目的是为得到 ACi 响应曲线受光呼吸延迟的影响，尤其是在临近补偿点时。

**延迟模块**


```r
#假定有15s延迟时的数据，即相比上面构造的Cc数据减少15个点
Cc15 <- as.numeric(c((min(Cc) + 15):max(Cc), rep(max(Cc), 15)))
vo15 <- Vomax * O2 / (O2 + Ko * (1 + Cc15 / Kc))
A15 <- vc - 0.5 * vo15 - R
Aapparent15 <- vc - 0.5 * vo15

#30 s 延迟数据
Cc30 <- as.numeric(c((min(Cc) + 30):max(Cc), rep(max(Cc), 30)))
vo30 <- Vomax * O2 / (O2 + Ko * (1 + Cc30 / Kc))
A30 <- vc - 0.5 * vo30 - R
Aapparent30 <- vc - 0.5 * vo30

#60s 延迟数据
Cc60 <- as.numeric(c((min(Cc) + 60):max(Cc), rep(max(Cc), 60)))
vo60 <- Vomax * O2 / (O2 + Ko * (1 + Cc60 / Kc))
A60 <- vc - 0.5 * vo60 - R
Aapparent60 <- vc - 0.5 * vo60

#120s延迟数据
Cc120 <- as.numeric(c((min(Cc) + 120):max(Cc), rep(max(Cc), 120)))
vo120 <- Vomax * O2 / (O2 + Ko * (1 + Cc120 / Kc))
A120 <- vc - 0.5 * vo120 - R
Aapparent120 <- vc - 0.5 * vo120

#300s延迟数据
Cc300 <- as.numeric(c((min(Cc) + 300):max(Cc), rep(max(Cc), 300)))
vo300 <- Vomax * O2 / (O2 + Ko * (1 + Cc300 / Kc))
A300 <- vc - 0.5 * vo120 - R
Aapparent300 <- vc - 0.5 * vo300 
```

### 数据的构造 {#multi4}

下面的代码主要是将上文最终计算的数据构造数据集，并导出。


```r
Anet <- c(A, A15, A30, A60, A120, A300)
Aapp <-
  c(Aapparent,
    Aapparent15,
    Aapparent30,
    Aapparent60,
    Aapparent120,
    Aapparent300)
Ccfull <- rep(Cc, 6)
Cifull <- rep(Ci, 6)
Delay <-
  c(
    rep("0", length(A)),
    rep("15", length(A15)),
    rep("30", length(A30)),
    rep("60", length(A60)),
    rep("120", length(A120)),
    rep("300", length(A300))
  )
PRdata <- as.data.frame(cbind(Anet, Aapp, Ccfull, Cifull, Delay))
write.csv(PRdata, "./data/PRdata.csv")
```

### 光呼吸滞后性作图 {#multi5}

下面的代码是将光呼吸的数据进行作图。


```r
data <- read.csv("./data/PRdata.csv")
data$Ccfull <- as.numeric(data$Ccfull)
data$Delay <- as.factor(data$Delay)

# 净光合速率与Cc作图
AnetCc <- ggplot(data, aes(x = Ccfull, y = Anet, colour = Delay)) +
  geom_point() +
  labs(x = expression(C[c] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[net] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Delay (s)') +
  scale_x_continuous(limits = c(25, 100),
                     breaks = c(25, 40, 55, 70, 85, 100)) +
  scale_y_continuous(limits = c(-5, 5)) +
  scale_colour_brewer(palette = 'Spectral') +
  #补偿点的参考线
  geom_hline(yintercept = 0) +
  theme_bw() +
  theme(panel.grid.major = element_blank(),
        panel.grid.minor = element_blank())
AnetCc
```

```
## Warning: Removed 1848 rows containing missing values (geom_point).
```

![(\#fig:anetcc)Anet VS. Cc](bookdown_files/figure-latex/anetcc-1.pdf) 


```r
#净光合速率与Ci作图
AnetCi <- ggplot(data, aes(x = Cifull, y = Anet, colour = Delay)) +
  geom_point() +
  labs(x = expression(C[i] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[net] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Delay (s)') +
  scale_x_continuous(limits = c(25, 100),
                     breaks = c(25, 40, 55, 70, 85, 100)) +
  scale_y_continuous(limits = c(-5, 5)) +
  scale_colour_brewer(palette = 'Spectral') +
  geom_hline(yintercept = 0) +
  theme_bw() +
  theme(panel.grid.major = element_blank(),
        panel.grid.minor = element_blank())
AnetCi
```

```
## Warning: Removed 1878 rows containing missing values (geom_point).
```

![(\#fig:anetci)Anet VS. Ci](bookdown_files/figure-latex/anetci-1.pdf) 


```r
#表观光合与Cc作图
AappCc <- ggplot(data, aes(x = Ccfull, y = Aapp, colour = Delay)) +
  geom_point() +
  labs(x = expression(C[c] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[apparent] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Delay (s)') +
  scale_x_continuous(limits = c(25, 75),
                     breaks = c(25, 35, 45, 55, 65, 75)) +
  scale_y_continuous(limits = c(-5, 5)) +
  scale_colour_brewer(palette = 'Spectral') +
  geom_hline(yintercept = 0) +
  theme_bw() +
  theme(panel.grid.major = element_blank(),
        panel.grid.minor = element_blank())
AappCc
```

```
## Warning: Removed 1959 rows containing missing values (geom_point).
```

![(\#fig:aappcc)Aapparent VS. Cc](bookdown_files/figure-latex/aappcc-1.pdf) 


```r
#表观与Ci作图
AappCi <- ggplot(data, aes(x = Cifull, y = Aapp, colour = Delay)) +
  geom_point() +
  labs(x = expression(C[i] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[apparent] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Delay (s)') +
  scale_x_continuous(limits = c(25, 75),
                     breaks = c(25, 35, 45, 55, 65, 75)) +
  scale_y_continuous(limits = c(-5, 5)) +
  scale_colour_brewer(palette = 'Spectral') +
  geom_hline(yintercept = 0) +
  theme_bw() +
  theme(panel.grid.major = element_blank(),
        panel.grid.minor = element_blank())
AappCi
```

```
## Warning: Removed 2003 rows containing missing values (geom_point).
```

![(\#fig:aappci)Aapparent VS. Ci](bookdown_files/figure-latex/aappci-1.pdf) 

### 补偿点计算 {#multi6}

计算不同的光呼吸时间延迟下的补偿点（基于Ci）：


```r
#对于基于Ci的数据，仅采用ci<100时的数据
dataCi <- data[data$Cifull < 100,]
dataCi0 <- dataCi[dataCi$Delay == "0",]
dataCi15 <- dataCi[dataCi$Delay == "15",]
dataCi30 <- dataCi[dataCi$Delay == "30",]
dataCi60 <- dataCi[dataCi$Delay == "60",]
dataCi120 <- dataCi[dataCi$Delay == "120",]
dataCi300 <- dataCi[dataCi$Delay == "300",]

#光呼吸无延迟时的计算，线性拟合
m1 <- lm(dataCi0$Anet ~ dataCi0$Cifull)
summary(m1)
```

```
## 
## Call:
## lm(formula = dataCi0$Anet ~ dataCi0$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13165 -0.04518  0.01711  0.05408  0.06699 
## 
## Coefficients:
##                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)    -7.2248105  0.0199353  -362.4   <2e-16 ***
## dataCi0$Cifull  0.1228632  0.0003089   397.8   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.06081 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.582e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
#补偿点为截距比斜率（纵坐标为零）
Gamma0 <- -m1$coefficients[1] / m1$coefficients[2]

#光呼吸延时15s
m2 <- lm(dataCi15$Anet ~ dataCi15$Cifull)
summary(m2)
```

```
## 
## Call:
## lm(formula = dataCi15$Anet ~ dataCi15$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13119 -0.04502  0.01705  0.05389  0.06676 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -7.0873326  0.0198665  -356.7   <2e-16 ***
## dataCi15$Cifull  0.1225900  0.0003078   398.3   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0606 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.586e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma15 <- -m2$coefficients[1] / m2$coefficients[2]

#光呼吸延时30s
m3 <- lm(dataCi30$Anet ~ dataCi30$Cifull)
summary(m3)
```

```
## 
## Call:
## lm(formula = dataCi30$Anet ~ dataCi30$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13076 -0.04488  0.01699  0.05372  0.06654 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -6.9553180  0.0198028  -351.2   <2e-16 ***
## dataCi30$Cifull  0.1223321  0.0003068   398.7   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0604 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.59e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma30 <- -m3$coefficients[1] / m3$coefficients[2]

#光呼吸延时60s
m4 <- lm(dataCi60$Anet ~ dataCi60$Cifull)
summary(m4)
```

```
## 
## Call:
## lm(formula = dataCi60$Anet ~ dataCi60$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13001 -0.04462  0.01690  0.05341  0.06616 
## 
## Coefficients:
##                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -6.706429   0.019689  -340.6   <2e-16 ***
## dataCi60$Cifull  0.121858   0.000305   399.5   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.06006 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.596e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma60 <- -m4$coefficients[1] / m4$coefficients[2]

#光呼吸延时120s
m5 <- lm(dataCi120$Anet ~ dataCi120$Cifull)
summary(m5)
```

```
## 
## Call:
## lm(formula = dataCi120$Anet ~ dataCi120$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.12879 -0.04421  0.01673  0.05292  0.06555 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -6.2616980  0.0195062  -321.0   <2e-16 ***
## dataCi120$Cifull  0.1210486  0.0003022   400.5   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0595 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.604e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma120 <- -m5$coefficients[1] / m5$coefficients[2]

#光呼吸延时300s
m6 <- lm(dataCi300$Anet ~ dataCi300$Cifull)
summary(m6)
```

```
## 
## Call:
## lm(formula = dataCi300$Anet ~ dataCi300$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.12879 -0.04421  0.01673  0.05292  0.06555 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -6.2616980  0.0195062  -321.0   <2e-16 ***
## dataCi300$Cifull  0.1210486  0.0003022   400.5   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0595 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.604e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma300 <- -m6$coefficients[1] / m6$coefficients[2]
```

构造数据并作图

```r
GammaCi <- c(Gamma0, Gamma15, Gamma30, Gamma60, Gamma120, Gamma300)

ints <-
  c(
    m1$coefficients[1],
    m2$coefficients[1],
    m3$coefficients[1],
    m4$coefficients[1],
    m5$coefficients[1],
    m6$coefficients[1]
  )
slps <-
  c(
    0,
    m2$coefficients[2] - m1$coefficients[2],
    m3$coefficients[2] - m1$coefficients[2],
    m4$coefficients[2] - m1$coefficients[2],
    m5$coefficients[2] - m1$coefficients[2],
    m6$coefficients[2] - m1$coefficients[2]
  )
dels <- c(0, 15, 30, 60, 120, 300)
summary(lm(ints ~ dels))
```

```
## 
## Call:
## lm(formula = ints ~ dels)
## 
## Residuals:
##   (Intercept) (Intercept).1 (Intercept).2 (Intercept).3 (Intercept).4 
##      -0.20351      -0.11262      -0.02719       0.12853       0.38691 
## (Intercept).5 
##      -0.17212 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -7.0213001  0.1343090 -52.277 8.01e-07 ***
## dels         0.0031057  0.0009959   3.119   0.0356 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.2503 on 4 degrees of freedom
## Multiple R-squared:  0.7086,	Adjusted R-squared:  0.6357 
## F-statistic: 9.725 on 1 and 4 DF,  p-value: 0.03558
```

```r
plot(ints ~ dels)
```

![(\#fig:intdelay)基于 Ci 的不同延时下的截距](bookdown_files/figure-latex/intdelay-1.pdf) 



```r
plot(slps ~ dels)
```

![(\#fig:intslps)基于 Ci 的不同延时下的斜率变化](bookdown_files/figure-latex/intslps-1.pdf) 

```r
summary(lm(slps ~ dels - 1))
```

```
## 
## Call:
## lm(formula = slps ~ dels - 1)
## 
## Residuals:
##                   dataCi15$Cifull  dataCi30$Cifull  dataCi60$Cifull 
##       -2.711e-19       -1.574e-04       -2.995e-04       -5.424e-04 
## dataCi120$Cifull dataCi300$Cifull 
##       -8.881e-04        5.015e-04 
## 
## Coefficients:
##       Estimate Std. Error t value Pr(>|t|)   
## dels -7.72e-06   1.63e-06  -4.738  0.00516 **
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0005383 on 5 degrees of freedom
## Multiple R-squared:  0.8178,	Adjusted R-squared:  0.7814 
## F-statistic: 22.44 on 1 and 5 DF,  p-value: 0.005161
```

基于 Cc 的补偿点计算结果:


```r
# 仅使用 Cc < 75的数据点拟合，过程同ci
dataCc <- data[data$Ccfull < 75, ]
dataCc0 <- dataCc[dataCc$Delay == "0", ]
dataCc15 <- dataCc[dataCc$Delay == "15", ]
dataCc30 <- dataCc[dataCc$Delay == "30", ]
dataCc60 <- dataCc[dataCc$Delay == "60", ]
dataCc120 <- dataCc[dataCc$Delay == "120", ]
dataCc300 <- dataCc[dataCc$Delay == "300", ]

# 无延迟数据
m1 <- lm(dataCc0$Anet ~ dataCc0$Ccfull)
summary(m1)
```

```
## 
## Call:
## lm(formula = dataCc0$Anet ~ dataCc0$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.075648 -0.025444  0.009857  0.031706  0.039431 
## 
## Coefficients:
##                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)    -8.4062011  0.0181852  -462.3   <2e-16 ***
## dataCc0$Ccfull  0.1438710  0.0003527   407.9   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03599 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.664e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma0 <- -m1$coefficients[1] / m1$coefficients[2]

#延时15s数据
m2 <- lm(dataCc15$Anet ~ dataCc15$Ccfull)
summary(m2)
```

```
## 
## Call:
## lm(formula = dataCc15$Anet ~ dataCc15$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.075393 -0.025359  0.009824  0.031599  0.039299 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -8.2659145  0.0181243  -456.1   <2e-16 ***
## dataCc15$Ccfull  0.1435470  0.0003515   408.4   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03587 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.668e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma15 <- -m2$coefficients[1] / m2$coefficients[2]

#延时30s数据
m3 <- lm(dataCc30$Anet ~ dataCc30$Ccfull)
summary(m3)
```

```
## 
## Call:
## lm(formula = dataCc30$Anet ~ dataCc30$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.075158 -0.025280  0.009794  0.031501  0.039177 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -8.1312578  0.0180681  -450.0   <2e-16 ***
## dataCc30$Ccfull  0.1432414  0.0003504   408.8   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03576 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.671e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma30 <- -m3$coefficients[1] / m3$coefficients[2]

#延时60s数据
m4 <- lm(dataCc60$Anet ~ dataCc60$Ccfull)
summary(m4)
```

```
## 
## Call:
## lm(formula = dataCc60$Anet ~ dataCc60$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.074737 -0.025139  0.009739  0.031325  0.038959 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -7.8775340  0.0179675  -438.4   <2e-16 ***
## dataCc60$Ccfull  0.1426797  0.0003485   409.4   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03556 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.676e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma60 <- -m4$coefficients[1] / m4$coefficients[2]

#延时120s数据
m5 <- lm(dataCc120$Anet ~ dataCc120$Ccfull)
summary(m5)
```

```
## 
## Call:
## lm(formula = dataCc120$Anet ~ dataCc120$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.074060 -0.024912  0.009651  0.031042  0.038607 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -7.4246412  0.0178052  -417.0   <2e-16 ***
## dataCc120$Ccfull  0.1417238  0.0003453   410.4   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03524 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.684e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma120 <- -m5$coefficients[1] / m5$coefficients[2]

#延时300s数据
m6 <- lm(dataCc300$Anet ~ dataCc300$Ccfull)
summary(m6)
```

```
## 
## Call:
## lm(formula = dataCc300$Anet ~ dataCc300$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.074060 -0.024912  0.009651  0.031042  0.038607 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -7.4246412  0.0178052  -417.0   <2e-16 ***
## dataCc300$Ccfull  0.1417238  0.0003453   410.4   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03524 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.684e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma300 <- -m6$coefficients[1] / m6$coefficients[2]
```


```r
GammaCc <- c(Gamma0, Gamma15, Gamma30, Gamma60, Gamma120, Gamma300)

ints <-
  c(
    m1$coefficients[1],
    m2$coefficients[1],
    m3$coefficients[1],
    m4$coefficients[1],
    m5$coefficients[1],
    m6$coefficients[1]
  )
dels <- c(0, 15, 30, 60, 120, 300)
plot(ints ~ dels)
```

![(\#fig:intdelaycc)基于 Cc 的不同延时下的时间](bookdown_files/figure-latex/intdelaycc-1.pdf) 

```r
summary(lm(ints ~ dels))
```

```
## 
## Call:
## lm(formula = ints ~ dels)
## 
## Residuals:
##   (Intercept) (Intercept).1 (Intercept).2 (Intercept).3 (Intercept).4 
##      -0.20760      -0.11478      -0.02759       0.13119       0.39421 
## (Intercept).5 
##      -0.17542 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -8.198602   0.136891 -59.891 4.65e-07 ***
## dels         0.003165   0.001015   3.118   0.0356 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.2552 on 4 degrees of freedom
## Multiple R-squared:  0.7085,	Adjusted R-squared:  0.6356 
## F-statistic:  9.72 on 1 and 4 DF,  p-value: 0.03561
```


```r
#GammaStar
# For Ci-based estimates, only use Ci < 100
dataCi <- data[data$Cifull < 100,]
dataCi0 <- dataCi[dataCi$Delay == "0",]
dataCi15 <- dataCi[dataCi$Delay == "15",]
dataCi30 <- dataCi[dataCi$Delay == "30",]
dataCi60 <- dataCi[dataCi$Delay == "60",]
dataCi120 <- dataCi[dataCi$Delay == "120",]
dataCi300 <- dataCi[dataCi$Delay == "300",]
m1 <- lm(dataCi0$Aapp ~ dataCi0$Cifull)
summary(m1)
```

```
## 
## Call:
## lm(formula = dataCi0$Aapp ~ dataCi0$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13165 -0.04518  0.01711  0.05408  0.06699 
## 
## Coefficients:
##                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)    -5.2248105  0.0199353  -262.1   <2e-16 ***
## dataCi0$Cifull  0.1228632  0.0003089   397.8   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.06081 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.582e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma0 <- -m1$coefficients[1] / m1$coefficients[2]
m2 <- lm(dataCi15$Aapp ~ dataCi15$Cifull)
summary(m2)
```

```
## 
## Call:
## lm(formula = dataCi15$Aapp ~ dataCi15$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13119 -0.04502  0.01705  0.05389  0.06676 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -5.0873326  0.0198665  -256.1   <2e-16 ***
## dataCi15$Cifull  0.1225900  0.0003078   398.3   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0606 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.586e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma15 <- -m2$coefficients[1] / m2$coefficients[2]
m3 <- lm(dataCi30$Aapp ~ dataCi30$Cifull)
summary(m3)
```

```
## 
## Call:
## lm(formula = dataCi30$Aapp ~ dataCi30$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13076 -0.04488  0.01699  0.05372  0.06654 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -4.9553180  0.0198028  -250.2   <2e-16 ***
## dataCi30$Cifull  0.1223321  0.0003068   398.7   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0604 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.59e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma30 <- -m3$coefficients[1] / m3$coefficients[2]
m4 <- lm(dataCi60$Aapp ~ dataCi60$Cifull)
summary(m4)
```

```
## 
## Call:
## lm(formula = dataCi60$Aapp ~ dataCi60$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13001 -0.04462  0.01690  0.05341  0.06616 
## 
## Coefficients:
##                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -4.706429   0.019689  -239.0   <2e-16 ***
## dataCi60$Cifull  0.121858   0.000305   399.5   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.06006 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.596e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma60 <- -m4$coefficients[1] / m4$coefficients[2]
m5 <- lm(dataCi120$Aapp ~ dataCi120$Cifull)
summary(m5)
```

```
## 
## Call:
## lm(formula = dataCi120$Aapp ~ dataCi120$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.12879 -0.04421  0.01673  0.05292  0.06555 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -4.2616980  0.0195062  -218.5   <2e-16 ***
## dataCi120$Cifull  0.1210486  0.0003022   400.5   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0595 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.604e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma120 <- -m5$coefficients[1] / m5$coefficients[2]
m6 <- lm(dataCi300$Aapp ~ dataCi300$Cifull)
summary(m6)
```

```
## 
## Call:
## lm(formula = dataCi300$Aapp ~ dataCi300$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.12662 -0.04346  0.01645  0.05202  0.06444 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -3.2402030  0.0191773  -169.0   <2e-16 ***
## dataCi300$Cifull  0.1193852  0.0002971   401.8   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.05849 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.615e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma300 <- -m6$coefficients[1] / m6$coefficients[2]

GammastarCi <-
  c(Gamma0, Gamma15, Gamma30, Gamma60, Gamma120, Gamma300)

# For Cc-based estimates, only use Cc < 75
dataCc <- data[data$Ccfull < 75,]
dataCc0 <- dataCc[dataCc$Delay == "0",]
dataCc15 <- dataCc[dataCc$Delay == "15",]
dataCc30 <- dataCc[dataCc$Delay == "30",]
dataCc60 <- dataCc[dataCc$Delay == "60",]
dataCc120 <- dataCc[dataCc$Delay == "120",]
dataCc300 <- dataCc[dataCc$Delay == "300",]
m1 <- lm(dataCc0$Aapp ~ dataCc0$Ccfull)
summary(m1)
```

```
## 
## Call:
## lm(formula = dataCc0$Aapp ~ dataCc0$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.075648 -0.025444  0.009857  0.031706  0.039431 
## 
## Coefficients:
##                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)    -6.4062011  0.0181852  -352.3   <2e-16 ***
## dataCc0$Ccfull  0.1438710  0.0003527   407.9   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03599 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.664e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma0 <- -m1$coefficients[1] / m1$coefficients[2]
m2 <- lm(dataCc15$Aapp ~ dataCc15$Ccfull)
summary(m2)
```

```
## 
## Call:
## lm(formula = dataCc15$Aapp ~ dataCc15$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.075393 -0.025359  0.009824  0.031599  0.039299 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -6.2659145  0.0181243  -345.7   <2e-16 ***
## dataCc15$Ccfull  0.1435470  0.0003515   408.4   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03587 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.668e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma15 <- -m2$coefficients[1] / m2$coefficients[2]
m3 <- lm(dataCc30$Aapp ~ dataCc30$Ccfull)
summary(m3)
```

```
## 
## Call:
## lm(formula = dataCc30$Aapp ~ dataCc30$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.075158 -0.025280  0.009794  0.031501  0.039177 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -6.1312578  0.0180681  -339.3   <2e-16 ***
## dataCc30$Ccfull  0.1432414  0.0003504   408.8   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03576 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.671e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma30 <- -m3$coefficients[1] / m3$coefficients[2]
m4 <- lm(dataCc60$Aapp ~ dataCc60$Ccfull)
summary(m4)
```

```
## 
## Call:
## lm(formula = dataCc60$Aapp ~ dataCc60$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.074737 -0.025139  0.009739  0.031325  0.038959 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     -5.8775340  0.0179675  -327.1   <2e-16 ***
## dataCc60$Ccfull  0.1426797  0.0003485   409.4   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03556 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.676e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma60 <- -m4$coefficients[1] / m4$coefficients[2]
m5 <- lm(dataCc120$Aapp ~ dataCc120$Ccfull)
summary(m5)
```

```
## 
## Call:
## lm(formula = dataCc120$Aapp ~ dataCc120$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.074060 -0.024912  0.009651  0.031042  0.038607 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -5.4246412  0.0178052  -304.7   <2e-16 ***
## dataCc120$Ccfull  0.1417238  0.0003453   410.4   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03524 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.684e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma120 <- -m5$coefficients[1] / m5$coefficients[2]
m6 <- lm(dataCc300$Aapp ~ dataCc300$Ccfull)
summary(m6)
```

```
## 
## Call:
## lm(formula = dataCc300$Aapp ~ dataCc300$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.072835 -0.024500  0.009492  0.030529  0.037969 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -4.3867121  0.0175110  -250.5   <2e-16 ***
## dataCc300$Ccfull  0.1397661  0.0003396   411.5   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03466 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.694e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gamma300 <- -m6$coefficients[1] / m6$coefficients[2]

GammastarCc <-
  c(Gamma0, Gamma15, Gamma30, Gamma60, Gamma120, Gamma300)

Delay2 <- c("0", "15", "30", "60", "120", "300")
PRcomps <-
  as.data.frame(cbind(Delay2, GammaCc, GammaCi, GammastarCc, GammastarCi))
write.csv(PRcomps, "./data/PRcomps.csv")

ints <-
  c(
    m1$coefficients[1],
    m2$coefficients[1],
    m3$coefficients[1],
    m4$coefficients[1],
    m5$coefficients[1],
    m6$coefficients[1]
  )
dels <- c(0, 15, 30, 60, 120, 300)
summary(lm(ints ~ dels))
```

```
## 
## Call:
## lm(formula = ints ~ dels)
## 
## Residuals:
##   (Intercept) (Intercept).1 (Intercept).2 (Intercept).3 (Intercept).4 
##    -0.0751633    -0.0347043     0.0001247     0.0541933     0.1077758 
## (Intercept).5 
##    -0.0522262 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -6.331038   0.041673 -151.92 1.13e-08 ***
## dels         0.006655   0.000309   21.54 2.75e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.07768 on 4 degrees of freedom
## Multiple R-squared:  0.9915,	Adjusted R-squared:  0.9893 
## F-statistic: 463.9 on 1 and 4 DF,  p-value: 2.749e-05
```

```r
plot(ints ~ dels)
```

![(\#fig:slpdelaycc)基于 Cc 的不同延时下的截距](bookdown_files/figure-latex/slpdelaycc-1.pdf) 

### 无光呼吸酶失活模块 {#multi7}

该部分内容是在测量 ACi 曲线时检测 Rubisco 失活的影响 -- 从激活状态的变化导致了多少的偏移？

#### 数据构造  {#multi8}

基于文献，假定 $CO_2$ 从 400 ppm 降低至 5 ppm 时，激活率从 100% 降低至 80%。


```r
#Assume that Rubisco activation state drops from 100% at 400 ppm to 80% at 5 ppm Cr (line 273 is Cr of 400, Cc of 297; 5ppm Cr is 25 ppm Cc) roughly from Salvucci et al 1986, arabidopsis; assume linear response

#不同的cr对应了不同的cc浓度，
#此为cc的变化范围（cr从400降低至5）
ccslope <- c(25, 297)
#酶的激活率变化
raslope <- c(0.80, 1.00)
#得到cc变化对应rubisco激活率变化的关系
ram1 <- lm(raslope ~ ccslope)
raslope <- coef(ram1)[2]
raint <- coef(ram1)[1]

#根据公式计算酶部分失活后各个参数
vora1 <- (raslope * Cc + raint) * Vomax * O2 / (O2 + Ko * (1 + Cc / Kc))
vcra1 <- (raslope * Cc + raint) * Vcmax * (Cc) / (Cc + Kco)
Ara1 <- vcra1 - 0.5 * vora1 - R
Aapparentra1 <- vcra1 - 0.5 * vora1
Cira1 <- Ara1 / gm + Cc
Cbra1 <- Ara1 / gsw + Cira1
Crra1 <- Ara1 / BLC + Cbra1

#失活后换算为分钟的变化斜率
Counter <- as.numeric(c(1:length(Cc)))
RateCr1model <- lm(Crra1 ~ Counter)
RateCr1 <- coef(RateCr1model)[2] * 60
RateCb1model <- lm(Cbra1 ~ Counter)
RateCb1 <- coef(RateCb1model)[2] * 60
RateCi1model <- lm(Cira1 ~ Counter)
RateCi1 <- coef(RateCi1model)[2] * 60
RateCcmodel <- lm(Cc ~ Counter)
RateCc <- coef(RateCcmodel)[2] * 60

#假定在5ppm时下降为40%
ccslope2 <- c(25, 297)
raslope2 <- c(0.40, 1.00)
ram2 <- lm(raslope2 ~ ccslope2)
raslope2 <- coef(ram2)[2]
raint2 <- coef(ram2)[1]

vora2 <-
  (raslope2 * Cc + raint2) * Vomax * O2 / (O2 + Ko * (1 + Cc / Kc)) 
vcra2 <- (raslope2 * Cc + raint2) * Vcmax * (Cc) / (Cc + Kco) 
Ara2 <- vcra2 - 0.5 * vora2 - R 
Aapparentra2 <- vcra2 - 0.5 * vora2 
Cira2 <- Ara2 / gm + Cc #umol mol-1
Cbra2 <- Ara2 / gsw + Cira2 #umol mol-1
Crra2 <- Ara2 / BLC + Cbra2 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCr2model <- lm(Crra2 ~ Counter)
RateCr2 <- coef(RateCr2model)[2] * 60 #umol mol-1 min-1
RateCb2model <- lm(Cbra2 ~ Counter)
RateCb2 <- coef(RateCb2model)[2] * 60 #umol mol-1 min-1
RateCi2model <- lm(Cira2 ~ Counter)
RateCi2 <- coef(RateCi2model)[2] * 60 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc <- coef(RateCcmodel)[2] * 60 #umol mol-1 min-1

#假定在5ppm时下降为20%
ccslope3 <- c(25, 297)
raslope3 <- c(0.20, 1.00)
ram3 <- lm(raslope3 ~ ccslope3)
raslope3 <- coef(ram3)[2]
raint3 <- coef(ram3)[1]

vora3 <-
  (raslope3 * Cc + raint3) * Vomax * O2 / (O2 + Ko * (1 + Cc / Kc)) 
vcra3 <- (raslope3 * Cc + raint3) * Vcmax * (Cc) / (Cc + Kco) 
Ara3 <- vcra3 - 0.5 * vora3 - R 
Aapparentra3 <- vcra3 - 0.5 * vora3 
Cira3 <- Ara3 / gm + Cc 
Cbra3 <- Ara3 / gsw + Cira3 
Crra3 <- Ara3 / BLC + Cbra3 
Counter <- as.numeric(c(1:length(Cc)))
RateCr3model <- lm(Crra3 ~ Counter)
RateCr3 <- coef(RateCr3model)[2] * 60 
RateCb3model <- lm(Cbra3 ~ Counter)
RateCb3 <- coef(RateCb3model)[2] * 60 
RateCi3model <- lm(Cira3 ~ Counter)
RateCi3 <- coef(RateCi3model)[2] * 60 
RateCcmodel <- lm(Cc ~ Counter)
RateCc <- coef(RateCcmodel)[2] * 60 

Anet <- c(A, Ara1, Ara2, Ara3)
Aapp <- c(Aapparent, Aapparentra1, Aapparentra2, Aapparentra3)
Ccfull <- rep(Cc, 4)
Cifull <- c(Ci, Cira1, Cira2, Cira3)
Deactivation <-
  c(
    rep("None", length(A)),
    rep("Low", length(Ara1)),
    rep("Medium", length(Ara2)),
    rep("High", length(Ara3))
  )
RASdata <-
  as.data.frame(cbind(Anet, Aapp, Ccfull, Cifull, Deactivation))
write.csv(RASdata, "./data/RASdata.csv")
```

### 酶失活作图 {#multi9}


```r
data <- read.csv("./data/RASdata.csv")
data$Ccfull <- as.numeric(data$Ccfull)
data$Deactivation <- as.factor(data$Deactivation)

AnetCc <-
  ggplot(data, aes(x = Ccfull, y = Anet, colour = Deactivation)) +
  geom_point() +
  labs(x = expression(C[c] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[net] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Deactivation') +
  scale_x_continuous(limits = c(25, 100),
                     breaks = c(25, 40, 55, 70, 85, 100)) +
  scale_y_continuous(limits = c(-5, 5)) +
  scale_colour_brewer(palette = 'Spectral') +
  geom_hline(yintercept = 0) +
  theme_bw() +
  theme(panel.grid.major = element_blank(),
        panel.grid.minor = element_blank())
AnetCc
```

```
## Warning: Removed 1205 rows containing missing values (geom_point).
```

![(\#fig:anetdeccc)Rubisco 不同失活程度时 Anet VS Cc](bookdown_files/figure-latex/anetdeccc-1.pdf) 



```r
AnetCi <-
  ggplot(data, aes(x = Cifull, y = Anet, colour = Deactivation)) +
  geom_point() +
  labs(x = expression(C[i] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[net] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Deactivation') +
  scale_x_continuous(limits = c(25, 100),
                     breaks = c(25, 40, 55, 70, 85, 100)) +
  scale_y_continuous(limits = c(-5, 5)) +
  scale_colour_brewer(palette = 'Spectral') +
  geom_hline(yintercept = 0) +
  theme_bw() +
  theme(panel.grid.major = element_blank(),
        panel.grid.minor = element_blank())
AnetCi
```

```
## Warning: Removed 1230 rows containing missing values (geom_point).
```

![(\#fig:anetdecci)Rubisco 不同失活程度时 Anet VS Ci](bookdown_files/figure-latex/anetdecci-1.pdf) 



```r
AappCc <-
  ggplot(data, aes(x = Ccfull, y = Aapp, colour = Deactivation)) +
  geom_point() +
  labs(x = expression(C[c] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[apparent] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Deactivation') +
  scale_x_continuous(limits = c(25, 75),
                     breaks = c(25, 35, 45, 55, 65, 75)) +
  scale_y_continuous(limits = c(-5, 5)) +
  scale_colour_brewer(palette = 'Spectral') +
  geom_hline(yintercept = 0) +
  theme_bw() +
  theme(panel.grid.major = element_blank(),
        panel.grid.minor = element_blank())
AappCc
```

```
## Warning: Removed 1300 rows containing missing values (geom_point).
```

![(\#fig:aappdeccc)Rubisco 不同失活程度时 Aapp VS Cc](bookdown_files/figure-latex/aappdeccc-1.pdf) 



```r
AappCi <-
  ggplot(data, aes(x = Cifull, y = Aapp, colour = Deactivation)) +
  geom_point() +
  labs(x = expression(C[i] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[apparent] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Deactivation') +
  scale_x_continuous(limits = c(25, 75),
                     breaks = c(25, 35, 45, 55, 65, 75)) +
  scale_y_continuous(limits = c(-5, 5)) +
  scale_colour_brewer(palette = 'Spectral') +
  geom_hline(yintercept = 0) +
  theme_bw() +
  theme(panel.grid.major = element_blank(),
        panel.grid.minor = element_blank())
AappCi
```

```
## Warning: Removed 1321 rows containing missing values (geom_point).
```

![(\#fig:aappdecci)Rubisco 不同失活程度时 Aapp VS Ci](bookdown_files/figure-latex/aappdecci-1.pdf) 

### 不同失活程度下补偿点计算{#multi10}

此部分内容同未失活状态相似，不在额外介绍，可参考 \@ref(gammastar) 内容。


```r
#Gamma
# For Ci-based estimates, only use Ci < 100
dataCi <- data[data$Cifull < 100, ]
dataCinone <- dataCi[dataCi$Deactivation == "None", ]
dataCilow <- dataCi[dataCi$Deactivation == "Low", ]
dataCimedium <- dataCi[dataCi$Deactivation == "Medium", ]
dataCihigh <- dataCi[dataCi$Deactivation == "High", ]
m1 <- lm(dataCinone$Anet ~ dataCinone$Cifull)
summary(m1)
```

```
## 
## Call:
## lm(formula = dataCinone$Anet ~ dataCinone$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13165 -0.04518  0.01711  0.05408  0.06699 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)       -7.2248105  0.0199353  -362.4   <2e-16 ***
## dataCinone$Cifull  0.1228632  0.0003089   397.8   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.06081 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.582e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gammanone <- -m1$coefficients[1] / m1$coefficients[2]
m2 <- lm(dataCilow$Anet ~ dataCilow$Cifull)
summary(m2)
```

```
## 
## Call:
## lm(formula = dataCilow$Anet ~ dataCilow$Cifull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.034896 -0.011957  0.004499  0.014311  0.017726 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -6.444e+00  5.343e-03   -1206   <2e-16 ***
## dataCilow$Cifull  1.049e-01  8.341e-05    1258   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.01609 on 69 degrees of freedom
## Multiple R-squared:      1,	Adjusted R-squared:      1 
## F-statistic: 1.582e+06 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gammalow <- -m2$coefficients[1] / m2$coefficients[2]
m3 <- lm(dataCimedium$Anet ~ dataCimedium$Cifull)
summary(m3)
```

```
## 
## Call:
## lm(formula = dataCimedium$Anet ~ dataCimedium$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.09168 -0.07338 -0.02281  0.05893  0.18150 
## 
## Coefficients:
##                       Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         -4.8015337  0.0277371  -173.1   <2e-16 ***
## dataCimedium$Cifull  0.0671064  0.0004311   155.7   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0832 on 71 degrees of freedom
## Multiple R-squared:  0.9971,	Adjusted R-squared:  0.997 
## F-statistic: 2.423e+04 on 1 and 71 DF,  p-value: < 2.2e-16
```

```r
Gammamedium <- -m3$coefficients[1] / m3$coefficients[2]
m4 <- lm(dataCihigh$Anet ~ dataCihigh$Cifull)
summary(m4)
```

```
## 
## Call:
## lm(formula = dataCihigh$Anet ~ dataCihigh$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.15430 -0.12508 -0.03863  0.10156  0.30621 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)       -3.9450658  0.0467537  -84.38   <2e-16 ***
## dataCihigh$Cifull  0.0473551  0.0007255   65.27   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.1399 on 72 degrees of freedom
## Multiple R-squared:  0.9834,	Adjusted R-squared:  0.9831 
## F-statistic:  4260 on 1 and 72 DF,  p-value: < 2.2e-16
```

```r
Gammahigh <- -m4$coefficients[1] / m4$coefficients[2]

GammaCi <- c(Gammanone, Gammalow, Gammamedium, Gammahigh)

# For Cc-based estimates, only use Cc < 75
dataCc <- data[data$Ccfull < 75, ]
dataCcnone <- dataCc[dataCc$Deactivation == "None", ]
dataCclow <- dataCc[dataCc$Deactivation == "Low", ]
dataCcmedium <- dataCc[dataCc$Deactivation == "Medium", ]
dataCchigh <- dataCc[dataCc$Deactivation == "High", ]
m1 <- lm(dataCcnone$Anet ~ dataCcnone$Ccfull)
summary(m1)
```

```
## 
## Call:
## lm(formula = dataCcnone$Anet ~ dataCcnone$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.075648 -0.025444  0.009857  0.031706  0.039431 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)       -8.4062011  0.0181852  -462.3   <2e-16 ***
## dataCcnone$Ccfull  0.1438710  0.0003527   407.9   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03599 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.664e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gammanone <- -m1$coefficients[1] / m1$coefficients[2]
m2 <- lm(dataCclow$Anet ~ dataCclow$Ccfull)
summary(m2)
```

```
## 
## Call:
## lm(formula = dataCclow$Anet ~ dataCclow$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.019617 -0.006598  0.002556  0.008222  0.010225 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -7.243e+00  4.716e-03   -1536   <2e-16 ***
## dataCclow$Ccfull  1.182e-01  9.146e-05    1292   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.009333 on 48 degrees of freedom
## Multiple R-squared:      1,	Adjusted R-squared:      1 
## F-statistic: 1.67e+06 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gammalow <- -m2$coefficients[1] / m2$coefficients[2]
m3 <- lm(dataCcmedium$Anet ~ dataCcmedium$Ccfull)
summary(m3)
```

```
## 
## Call:
## lm(formula = dataCcmedium$Anet ~ dataCcmedium$Ccfull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.04819 -0.03875 -0.01205  0.03109  0.09244 
## 
## Coefficients:
##                      Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         -4.917283   0.022223  -221.3   <2e-16 ***
## dataCcmedium$Ccfull  0.066832   0.000431   155.1   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.04398 on 48 degrees of freedom
## Multiple R-squared:  0.998,	Adjusted R-squared:  0.998 
## F-statistic: 2.404e+04 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gammamedium <- -m3$coefficients[1] / m3$coefficients[2]
m4 <- lm(dataCchigh$Anet ~ dataCchigh$Ccfull)
summary(m4)
```

```
## 
## Call:
## lm(formula = dataCchigh$Anet ~ dataCchigh$Ccfull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.07739 -0.06223 -0.01935  0.04994  0.14847 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)       -3.7543099  0.0356922 -105.19   <2e-16 ***
## dataCchigh$Ccfull  0.0411528  0.0006922   59.45   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.07064 on 48 degrees of freedom
## Multiple R-squared:  0.9866,	Adjusted R-squared:  0.9863 
## F-statistic:  3534 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gammahigh <- -m4$coefficients[1] / m4$coefficients[2]

GammaCc <- c(Gammanone, Gammalow, Gammamedium, Gammahigh)

#GammaStar
# For Ci-based estimates, only use Ci < 100
dataCi <- data[data$Cifull < 100, ]
dataCinone <- dataCi[dataCi$Deactivation == "None", ]
dataCilow <- dataCi[dataCi$Deactivation == "Low", ]
dataCimedium <- dataCi[dataCi$Deactivation == "Medium", ]
dataCihigh <- dataCi[dataCi$Deactivation == "High", ]
m1 <- lm(dataCinone$Aapp ~ dataCinone$Cifull)
summary(m1)
```

```
## 
## Call:
## lm(formula = dataCinone$Aapp ~ dataCinone$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13165 -0.04518  0.01711  0.05408  0.06699 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)       -5.2248105  0.0199353  -262.1   <2e-16 ***
## dataCinone$Cifull  0.1228632  0.0003089   397.8   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.06081 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.582e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gammastarnone <- -m1$coefficients[1] / m1$coefficients[2]
m2 <- lm(dataCilow$Aapp ~ dataCilow$Cifull)
summary(m2)
```

```
## 
## Call:
## lm(formula = dataCilow$Aapp ~ dataCilow$Cifull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.034896 -0.011957  0.004499  0.014311  0.017726 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -4.444e+00  5.343e-03  -831.7   <2e-16 ***
## dataCilow$Cifull  1.049e-01  8.341e-05  1257.8   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.01609 on 69 degrees of freedom
## Multiple R-squared:      1,	Adjusted R-squared:      1 
## F-statistic: 1.582e+06 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gammastarlow <- -m2$coefficients[1] / m2$coefficients[2]
m3 <- lm(dataCimedium$Aapp ~ dataCimedium$Cifull)
summary(m3)
```

```
## 
## Call:
## lm(formula = dataCimedium$Aapp ~ dataCimedium$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.09168 -0.07338 -0.02281  0.05893  0.18150 
## 
## Coefficients:
##                       Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         -2.8015337  0.0277371  -101.0   <2e-16 ***
## dataCimedium$Cifull  0.0671064  0.0004311   155.7   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0832 on 71 degrees of freedom
## Multiple R-squared:  0.9971,	Adjusted R-squared:  0.997 
## F-statistic: 2.423e+04 on 1 and 71 DF,  p-value: < 2.2e-16
```

```r
Gammastarmedium <- -m3$coefficients[1] / m3$coefficients[2]
m4 <- lm(dataCihigh$Aapp ~ dataCihigh$Cifull)
summary(m4)
```

```
## 
## Call:
## lm(formula = dataCihigh$Aapp ~ dataCihigh$Cifull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.15430 -0.12508 -0.03863  0.10156  0.30621 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)       -1.9450658  0.0467537  -41.60   <2e-16 ***
## dataCihigh$Cifull  0.0473551  0.0007255   65.27   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.1399 on 72 degrees of freedom
## Multiple R-squared:  0.9834,	Adjusted R-squared:  0.9831 
## F-statistic:  4260 on 1 and 72 DF,  p-value: < 2.2e-16
```

```r
Gammastarhigh <- -m4$coefficients[1] / m4$coefficients[2]

GammastarCi <-
  c(Gammastarnone, Gammastarlow, Gammastarmedium, Gammastarhigh)

# For Cc-based estimates, only use Cc < 75
dataCc <- data[data$Ccfull < 75, ]
dataCcnone <- dataCc[dataCc$Deactivation == "None", ]
dataCclow <- dataCc[dataCc$Deactivation == "Low", ]
dataCcmedium <- dataCc[dataCc$Deactivation == "Medium", ]
dataCchigh <- dataCc[dataCc$Deactivation == "High", ]
m1 <- lm(dataCcnone$Aapp ~ dataCcnone$Ccfull)
summary(m1)
```

```
## 
## Call:
## lm(formula = dataCcnone$Aapp ~ dataCcnone$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.075648 -0.025444  0.009857  0.031706  0.039431 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)       -6.4062011  0.0181852  -352.3   <2e-16 ***
## dataCcnone$Ccfull  0.1438710  0.0003527   407.9   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03599 on 48 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 1.664e+05 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gammastarnone <- -m1$coefficients[1] / m1$coefficients[2]
m2 <- lm(dataCclow$Aapp ~ dataCclow$Ccfull)
summary(m2)
```

```
## 
## Call:
## lm(formula = dataCclow$Aapp ~ dataCclow$Ccfull)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.019617 -0.006598  0.002556  0.008222  0.010225 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      -5.243e+00  4.716e-03   -1112   <2e-16 ***
## dataCclow$Ccfull  1.182e-01  9.146e-05    1292   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.009333 on 48 degrees of freedom
## Multiple R-squared:      1,	Adjusted R-squared:      1 
## F-statistic: 1.67e+06 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gammastarlow <- -m2$coefficients[1] / m2$coefficients[2]
m3 <- lm(dataCcmedium$Aapp ~ dataCcmedium$Ccfull)
summary(m3)
```

```
## 
## Call:
## lm(formula = dataCcmedium$Aapp ~ dataCcmedium$Ccfull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.04819 -0.03875 -0.01205  0.03109  0.09244 
## 
## Coefficients:
##                      Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         -2.917283   0.022223  -131.3   <2e-16 ***
## dataCcmedium$Ccfull  0.066832   0.000431   155.1   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.04398 on 48 degrees of freedom
## Multiple R-squared:  0.998,	Adjusted R-squared:  0.998 
## F-statistic: 2.404e+04 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gammastarmedium <- -m3$coefficients[1] / m3$coefficients[2]
m4 <- lm(dataCchigh$Aapp ~ dataCchigh$Ccfull)
summary(m4)
```

```
## 
## Call:
## lm(formula = dataCchigh$Aapp ~ dataCchigh$Ccfull)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.07739 -0.06223 -0.01935  0.04994  0.14847 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)       -1.7543099  0.0356922  -49.15   <2e-16 ***
## dataCchigh$Ccfull  0.0411528  0.0006922   59.45   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.07064 on 48 degrees of freedom
## Multiple R-squared:  0.9866,	Adjusted R-squared:  0.9863 
## F-statistic:  3534 on 1 and 48 DF,  p-value: < 2.2e-16
```

```r
Gammastarhigh <- -m4$coefficients[1] / m4$coefficients[2]

GammastarCc <-
  c(Gammastarnone, Gammastarlow, Gammastarmedium, Gammastarhigh)

Deactivation2 <- c("None", "Low", "Medium", "High")
RAScomps <-
  as.data.frame(cbind(Deactivation2, GammaCc, GammaCi, GammastarCc, GammastarCi))
write.csv(RAScomps, "./data/RAScomps.csv")
```


## 时间延迟的扩散限制 {#multi11}

对于扩散限制，下面的内容比较了多速率 RACiR 和标准 ACi 曲线的差别，比较实在有光呼吸和没有光呼吸的两种情况。对于没有扩散限制的表观光合速，采用了已知质量的碱石灰药品，放置于 1.7 ml 的微量离心管内，然后将其置于荧光叶室内部模拟叶片，此时叶室环境控制与其他实验不同，此时不再控制 H2OR。RACiR 测试从 500 到 0 的变化，不同样品的测量是随机的。

下面内容采用了一定的假设，来计算扩散的时间。


```r
#Equations from Campbell & Norman, 1998
#We are taking a simple approach to calculating diffusion times.
#Here we make the simplifying assumption that diffusion is pure, planar
#Diffusion, such that:
#gtot = phat * D / deltaZ
#where gtot is total conductance, phat is molar density of air in mol /m^3,
#D is diffusion coefficient in m2/s, deltaZ is pathlength in m
#Since PV = NRT, N/V = P/RT
#T in K, R in J K-1 mol-1, P in Pa

#phat = Patm/(RT)
phat = 100000 / (8.314 * 298.15)

#We also assume a linear pathlength
#Note, if diffusion is nonlinear or nonplanar, it will affect the value determined
#for D from this equation.

#D = gtot * deltaZ / phat

#Diffusion time, t, varies with D and deltaZ such that:

#t = (deltaZ)^2 / D

#So

#t = (deltaZ)^2 / (gtot * deltaZ / phat)

#If we assume mean diffusion pathlength of 1/2 lamina thickness,
#then Onoda et al. 2011 lamina thicknesses of: median 0.22 mm (0.11 to 0.74 for 95% CI)
#becomes 0.11 mm (0.055 to 0.37) for estimated deltaZ

#Convert pathlength to m
dZlow <- 0.055 / 1000
dZmedian <- 0.11 / 1000
dZhigh <- 0.37 / 1000
```

下面的内容是对边界层导度和气孔导度等赋值，由此而计算出其他所需要的参数：

```r
#Mesophyll Conductance
BLC <- 2 #mol m-2 s-1
gsw <- 0.4 #mol m-2 s-1

# 无限制的叶肉导度，并以此计算ci等
gm1 <- 1 #mol m-2 s-1
Ci1 <- A / gm1 + Cc #umol mol-1
Cim1 <- Ci1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cr1 <- A / BLC + Cb1 #umol mol-1
#根据斜率计算达到 100 ppm min-1 时记录数据的个数
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2] 
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
#计算ci，Cc等达到100ppm min-1 时数据的个数
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc1 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
#总的阻力
res1 <- 1 / gm1 + 1 / BLC + 1 / gsw

#不同的叶肉导度计算其他参数
gm2 <- 2 #mol m-2 s-1
Ci2 <- A / gm2 + Cc #umol mol-1
Cim2 <- Ci2
Cb2 <- A / gsw + Ci2 #umol mol-1
Cr2 <- A / BLC + Cb2 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr2 ~ Counter)
x2 <- 100 / coef(RateCrmodel)[2] 
RateCr2 <- coef(RateCrmodel)[2] * x2 #umol mol-1 min-1
RateCbmodel <- lm(Cb2 ~ Counter)
RateCb2 <- coef(RateCbmodel)[2] * x2 #umol mol-1 min-1
RateCimodel <- lm(Ci2 ~ Counter)
RateCi2 <- coef(RateCimodel)[2] * x2 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc2 <- coef(RateCcmodel)[2] * x2 #umol mol-1 min-1
res2 <- 1 / gm2 + 1 / BLC + 1 / gsw

#再次计算不同导度下的数值
gm4 <- 4 #mol m-2 s-1
Ci4 <- A / gm4 + Cc #umol mol-1
Cim4 <- Ci4
Cb4 <- A / gsw + Ci4 #umol mol-1
Cr4 <- A / BLC + Cb4 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr4 ~ Counter)
x4 <- 100 / coef(RateCrmodel)[2]
RateCr4 <- coef(RateCrmodel)[2] * x4 #umol mol-1 min-1
RateCbmodel <- lm(Cb4 ~ Counter)
RateCb4 <- coef(RateCbmodel)[2] * x4 #umol mol-1 min-1
RateCimodel <- lm(Ci4 ~ Counter)
RateCi4 <- coef(RateCimodel)[2] * x4 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc4 <- coef(RateCcmodel)[2] * x4 #umol mol-1 min-1
res4 <- 1 / gm4 + 1 / BLC + 1 / gsw

#再次计算不同导度下的数值
gm05 <- 0.5 #mol m-2 s-1
Ci05 <- A / gm05 + Cc #umol mol-1
Cim05 <- Ci05
Cb05 <- A / gsw + Ci05 #umol mol-1
Cr05 <- A / BLC + Cb05 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr05 ~ Counter)
x05 <- 100 / coef(RateCrmodel)[2]
RateCr05 <- coef(RateCrmodel)[2] * x05 #umol mol-1 min-1
RateCbmodel <- lm(Cb05 ~ Counter)
RateCb05 <- coef(RateCbmodel)[2] * x05 #umol mol-1 min-1
RateCimodel <- lm(Ci05 ~ Counter)
RateCi05 <- coef(RateCimodel)[2] * x05 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc05 <- coef(RateCcmodel)[2] * x05 #umol mol-1 min-1
res05 <- 1 / gm05 + 1 / BLC + 1 / gsw

#正常的叶肉导度数据计算其他参数
gm025 <- 0.25 #mol m-2 s-1
Ci025 <- A / gm025 + Cc #umol mol-1
Cim025 <- Ci025
Cb025 <- A / gsw + Ci025 #umol mol-1
Cr025 <- A / BLC + Cb025 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr025 ~ Counter)
x025 <- 100 / coef(RateCrmodel)[2]
RateCr025 <- coef(RateCrmodel)[2] * x025 #umol mol-1 min-1
RateCbmodel <- lm(Cb025 ~ Counter)
RateCb025 <- coef(RateCbmodel)[2] * x025 #umol mol-1 min-1
RateCimodel <- lm(Ci025 ~ Counter)
RateCi025 <- coef(RateCimodel)[2] * x025 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc025 <- coef(RateCcmodel)[2] * x025 #umol mol-1 min-1
res025 <- 1 / gm025 + 1 / BLC + 1 / gsw

#另一个正常的叶肉导度
gm0125 <- 0.125 #mol m-2 s-1
Ci0125 <- A / gm0125 + Cc #umol mol-1
Cim0125 <- Ci0125
Cb0125 <- A / gsw + Ci0125 #umol mol-1
Cr0125 <- A / BLC + Cb0125 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr0125 ~ Counter)
x0125 <- 100 / coef(RateCrmodel)[2]
RateCr0125 <- coef(RateCrmodel)[2] * x0125 #umol mol-1 min-1
RateCbmodel <- lm(Cb0125 ~ Counter)
RateCb0125 <- coef(RateCbmodel)[2] * x0125 #umol mol-1 min-1
RateCimodel <- lm(Ci0125 ~ Counter)
RateCi0125 <- coef(RateCimodel)[2] * x0125 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc0125 <- coef(RateCcmodel)[2] * x0125 #umol mol-1 min-1
res0125 <- 1 / gm0125 + 1 / BLC + 1 / gsw

#利用不同叶肉导度的数据计算结果构造数据
Ratesgm <-
  c(RateCc0125, RateCc025, RateCc05, RateCc1, RateCc2, RateCc4)
gmval <- c(0.125, 0.25, 0.5, 1, 2, 4)
totalresgm <- c(res0125, res025, res05, res1, res2, res4)
resistance <-
  c(
    rep(res0125, 376),
    rep(res025, 376),
    rep(res05, 376),
    rep(res1, 376),
    rep(res2, 376),
    rep(res4, 376)
  )

#其余部分与上面类似
#此时采用不同的气孔导度构建数据
BLC <- 2 #mol m-2 s-1
gm <- 1 #mol m-2 s-1

gsw <- 0.4 #mol m-2 s-1
Ci1 <- A / gm + Cc #umol mol-1
Cis04 <- Ci1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2] 
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc04 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res04 <- 1 / gm + 1 / BLC + 1 / gsw

gsw <- 0.2 #mol m-2 s-1
Ci1 <- A / gm + Cc #umol mol-1
Cis02 <- Ci1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2]
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc02 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res02 <- 1 / gm + 1 / BLC + 1 / gsw

gsw <- 0.1 #mol m-2 s-1
Ci1 <- A / gm + Cc #umol mol-1
Cis01 <- Ci1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2]
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc01 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res01 <- 1 / gm + 1 / BLC + 1 / gsw

gsw <- 0.05 #mol m-2 s-1
Ci1 <- A / gm + Cc #umol mol-1
Cis05 <- Ci1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2]
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc005 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res005 <- 1 / gm + 1 / BLC + 1 / gsw

gsw <- 0.025 #mol m-2 s-1
Ci1 <- A / gm + Cc #umol mol-1
Cis0025 <- Ci1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2]
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc0025 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res0025 <- 1 / gm + 1 / BLC + 1 / gsw

Ratesgsw <- c(RateCc0025, RateCc005, RateCc01, RateCc02, RateCc04)
gswvals <- c(0.025, 0.05, 0.1, 0.2, 0.4)
totalresgsw <- c(res0025, res005, res01, res02, res04)

# 下面的代码是采用不同的边界层导度
# 含义与上面代码相似
gm <- 1 #mol m-2 s-1
gsw <- 0.4 #mol m-2 s-1

BLC <- 2 #mol m-2 s-1

Ci1 <- A / gm + Cc #umol mol-1
Cib2 <- Ci1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2]
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc2 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res2 <- 1 / gm + 1 / BLC + 1 / gsw

BLC <- 4 #mol m-2 s-1

Ci1 <- A / gm + Cc #umol mol-1
Cib4 <- Ci1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2]
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc4 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res4 <- 1 / gm + 1 / BLC + 1 / gsw

BLC <- 1 #mol m-2 s-1

Ci1 <- A / gm + Cc #umol mol-1
Cib1 <- Ci1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2]
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc1 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res1 <- 1 / gm + 1 / BLC + 1 / gsw

BLC <- 0.5 #mol m-2 s-1

Ci1 <- A / gm + Cc #umol mol-1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cib05 <- Ci1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2]
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc05 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res05 <- 1 / gm + 1 / BLC + 1 / gsw

BLC <- 0.25 #mol m-2 s-1

Ci1 <- A / gm + Cc #umol mol-1
Cb1 <- A / gsw + Ci1 #umol mol-1
Cib025 <- Ci1
Cr1 <- A / BLC + Cb1 #umol mol-1
Counter <- as.numeric(c(1:length(Cc)))
RateCrmodel <- lm(Cr1 ~ Counter)
x1 <- 100 / coef(RateCrmodel)[2]
RateCr1 <- coef(RateCrmodel)[2] * x1 #umol mol-1 min-1
RateCbmodel <- lm(Cb1 ~ Counter)
RateCb1 <- coef(RateCbmodel)[2] * x1 #umol mol-1 min-1
RateCimodel <- lm(Ci1 ~ Counter)
RateCi1 <- coef(RateCimodel)[2] * x1 #umol mol-1 min-1
RateCcmodel <- lm(Cc ~ Counter)
RateCc025 <- coef(RateCcmodel)[2] * x1 #umol mol-1 min-1
res025 <- 1 / gm + 1 / BLC + 1 / gsw

BLCRates <- c(RateCc025, RateCc05, RateCc1, RateCc2, RateCc4)
BLCvals <- c(0.25, 0.5, 1, 2, 4)
totalresBLC <- c(res025, res05, res1, res2, res4)

Scenario <-
  c(
    rep("Boundary Layer Conductance", 5),
    rep("Stomatal Conductance", 5),
    rep("Mesophyll Conductance", 6)
  )
Rates <- c(BLCRates, Ratesgsw, Ratesgm)
Conductances <- c(BLCvals, gswvals, gmval)
TotalRes <- c(totalresBLC, totalresgsw, totalresgm)

Cidiffusion <- c(Cim0125, Cim025, Cim05, Cim1, Cim2, Cim4)
Adiffusion <- rep(A, 6)
Aappdiffusion <- rep(Aapparent, 6)
variable <- c(rep("Mesophyll Conductance", 6 * length(Cim1)))
conductance <-
  c(rep(0.125, 376),
    rep(0.25, 376),
    rep(0.5, 376),
    rep(1, 376),
    rep(2, 376),
    rep(4, 376))


Diffusionplot <-
  as.data.frame(cbind(
    Cidiffusion,
    Adiffusion,
    Aappdiffusion,
    variable,
    conductance,
    resistance
  ))
write.csv(Diffusionplot, "./data/DiffusionLimitsACI.csv")

Diffusion <-
  as.data.frame(cbind(Scenario, Rates, Conductances, TotalRes))
write.csv(Diffusion, "./data/DiffusionLimits.csv")
```


```r
knitr::kable(head(Diffusion))
```


\begin{tabular}{l|l|l|l|l}
\hline
  & Scenario & Rates & Conductances & TotalRes\\
\hline
Counter & Boundary Layer Conductance & 57.1493939574751 & 0.25 & 7.5\\
\hline
Counter.1 & Boundary Layer Conductance & 64.522239461912 & 0.5 & 5.5\\
\hline
Counter.2 & Boundary Layer Conductance & 68.9712299728077 & 1 & 4.5\\
\hline
Counter.3 & Boundary Layer Conductance & 71.4340185663793 & 2 & 4\\
\hline
Counter.4 & Boundary Layer Conductance & 72.7325667866589 & 4 & 3.75\\
\hline
Counter.5 & Stomatal Conductance & 19.4216526694884 & 0.025 & 41.5\\
\hline
\end{tabular}


### 扩散限制滞后性 {#multi12}

下面的代码，是根据上面代码的计算结果，结合最初的扩散时间的公式，来计算出各个参数的最大最小值，中间值，构造数据：


```r
gtot1 <- 1 / res1
t1low = (dZlow) ^ 2 / (gtot1 * dZlow / phat)
t1median = (dZmedian) ^ 2 / (gtot1 * dZmedian / phat)
t1high = (dZhigh) ^ 2 / (gtot1 * dZhigh / phat)
Cc1low <- c(Cc + (t1low * RateCc1))
Cc1median <- c(Cc + (t1median * RateCc1))
Cc1high <- c(Cc + (t1high * RateCc1))
vo1low <- Vomax * O2 / (O2 + Ko * (1 + Cc1low / Kc)) 
A1low <- vc - 0.5 * vo1low - R 
vo1median <- Vomax * O2 / (O2 + Ko * (1 + Cc1median / Kc)) 
A1median <- vc - 0.5 * vo1median - R 
vo1high <- Vomax * O2 / (O2 + Ko * (1 + Cc1high / Kc)) 
A1high <- vc - 0.5 * vo1high - R 

gtot0125 <- 1 / res0125
t0125low = (dZlow) ^ 2 / (gtot0125 * dZlow / phat)
t0125median = (dZmedian) ^ 2 / (gtot0125 * dZmedian / phat)
t0125high = (dZhigh) ^ 2 / (gtot0125 * dZhigh / phat)
Cc0125low <- c(Cc + (t0125low * RateCc0125))
Cc0125median <- c(Cc + (t0125median * RateCc0125))
Cc0125high <- c(Cc + (t0125high * RateCc0125))
vo0125low <- Vomax * O2 / (O2 + Ko * (1 + Cc0125low / Kc)) 
A0125low <- vc - 0.5 * vo0125low - R 
vo0125median <- Vomax * O2 / (O2 + Ko * (1 + Cc0125median / Kc)) 
A0125median <- vc - 0.5 * vo0125median - R 
vo0125high <- Vomax * O2 / (O2 + Ko * (1 + Cc0125high / Kc)) 
A0125high <- vc - 0.5 * vo0125high - R 

gtot025 <- 1 / res025
t025low = (dZlow) ^ 2 / (gtot025 * dZlow / phat)
t025median = (dZmedian) ^ 2 / (gtot025 * dZmedian / phat)
t025high = (dZhigh) ^ 2 / (gtot025 * dZhigh / phat)
Cc025low <- c(Cc + (t025low * RateCc025))
Cc025median <- c(Cc + (t025median * RateCc025))
Cc025high <- c(Cc + (t025high * RateCc025))
vo025low <- Vomax * O2 / (O2 + Ko * (1 + Cc025low / Kc)) 
A025low <- vc - 0.5 * vo025low - R 
vo025median <- Vomax * O2 / (O2 + Ko * (1 + Cc025median / Kc)) 
A025median <- vc - 0.5 * vo025median - R 
vo025high <- Vomax * O2 / (O2 + Ko * (1 + Cc025high / Kc)) 
A025high <- vc - 0.5 * vo025high - R 

gtot05 <- 1 / res05
t05low = (dZlow) ^ 2 / (gtot05 * dZlow / phat)
t05median = (dZmedian) ^ 2 / (gtot05 * dZmedian / phat)
t05high = (dZhigh) ^ 2 / (gtot05 * dZhigh / phat)
Cc05low <- c(Cc + (t05low * RateCc05))
Cc05median <- c(Cc + (t05median * RateCc05))
Cc05high <- c(Cc + (t05high * RateCc05))
vo05low <- Vomax * O2 / (O2 + Ko * (1 + Cc05low / Kc)) 
A05low <- vc - 0.5 * vo05low - R 
vo05median <- Vomax * O2 / (O2 + Ko * (1 + Cc05median / Kc)) 
A05median <- vc - 0.5 * vo05median - R 
vo05high <- Vomax * O2 / (O2 + Ko * (1 + Cc05high / Kc)) 
A05high <- vc - 0.5 * vo05high - R 

gtot2 <- 1 / res2
t2low = (dZlow) ^ 2 / (gtot2 * dZlow / phat)
t2median = (dZmedian) ^ 2 / (gtot2 * dZmedian / phat)
t2high = (dZhigh) ^ 2 / (gtot2 * dZhigh / phat)
Cc2low <- c(Cc + (t2low * RateCc2))
Cc2median <- c(Cc + (t2median * RateCc2))
Cc2high <- c(Cc + (t2high * RateCc2))
vo2low <- Vomax * O2 / (O2 + Ko * (1 + Cc2low / Kc)) 
A2low <- vc - 0.5 * vo2low - R 
vo2median <- Vomax * O2 / (O2 + Ko * (1 + Cc2median / Kc)) 
A2median <- vc - 0.5 * vo2median - R 
vo2high <- Vomax * O2 / (O2 + Ko * (1 + Cc2high / Kc)) 
A2high <- vc - 0.5 * vo2high - R 

gtot4 <- 1 / res4
t4low = (dZlow) ^ 2 / (gtot4 * dZlow / phat)
t4median = (dZmedian) ^ 2 / (gtot4 * dZmedian / phat)
t4high = (dZhigh) ^ 2 / (gtot4 * dZhigh / phat)
Cc4low <- c(Cc + (t4low * RateCc4))
Cc4median <- c(Cc + (t4median * RateCc4))
Cc4high <- c(Cc + (t4high * RateCc4))
vo4low <- Vomax * O2 / (O2 + Ko * (1 + Cc4low / Kc)) 
A4low <- vc - 0.5 * vo4low - R 
vo4median <- Vomax * O2 / (O2 + Ko * (1 + Cc4median / Kc)) 
A4median <- vc - 0.5 * vo4median - R 
vo4high <- Vomax * O2 / (O2 + Ko * (1 + Cc4high / Kc)) 
A4high <- vc - 0.5 * vo4high - R 

Cc0125high2 <- c(Cc + (t0125high * RateCc0125 * 2))
Cc025high2 <- c(Cc + (t025high * RateCc025 * 2))
Cc05high2 <- c(Cc + (t05high * RateCc05 * 2))
Cc1high2 <- c(Cc + (t1high * RateCc1 * 2))
Cc2high2 <- c(Cc + (t2high * RateCc2 * 2))
Cc4high2 <- c(Cc + (t4high * RateCc4 * 2))

vo0125high2 <- Vomax * O2 / (O2 + Ko * (1 + Cc0125high2 / Kc)) 
A0125high2 <- vc - 0.5 * vo0125high2 - R 
vo025high2 <- Vomax * O2 / (O2 + Ko * (1 + Cc025high2 / Kc)) 
A025high2 <- vc - 0.5 * vo025high2 - R 
vo05high2 <- Vomax * O2 / (O2 + Ko * (1 + Cc05high2 / Kc)) 
A05high2 <- vc - 0.5 * vo05high2 - R 
vo1high2 <- Vomax * O2 / (O2 + Ko * (1 + Cc1high2 / Kc)) 
A1high2 <- vc - 0.5 * vo1high2 - R 
vo2high2 <- Vomax * O2 / (O2 + Ko * (1 + Cc2high2 / Kc)) 
A2high2 <- vc - 0.5 * vo2high2 - R 
vo4high2 <- Vomax * O2 / (O2 + Ko * (1 + Cc4high2 / Kc)) 
A4high2 <- vc - 0.5 * vo4high2 - R 

Ahigh2 <-
  c(A0125high2, A025high2, A05high2, A1high2, A2high2, A4high2)

Cc0125high3 <- c(Cc + (t0125high * RateCc0125 * 3))
Cc025high3 <- c(Cc + (t025high * RateCc025 * 3))
Cc05high3 <- c(Cc + (t05high * RateCc05 * 3))
Cc1high3 <- c(Cc + (t1high * RateCc1 * 3))
Cc2high3 <- c(Cc + (t2high * RateCc2 * 3))
Cc4high3 <- c(Cc + (t4high * RateCc4 * 3))

vo0125high3 <- Vomax * O2 / (O2 + Ko * (1 + Cc0125high3 / Kc)) 
A0125high3 <- vc - 0.5 * vo0125high3 - R 
vo025high3 <- Vomax * O2 / (O2 + Ko * (1 + Cc025high3 / Kc)) 
A025high3 <- vc - 0.5 * vo025high3 - R 
vo05high3 <- Vomax * O2 / (O2 + Ko * (1 + Cc05high3 / Kc)) 
A05high3 <- vc - 0.5 * vo05high3 - R 
vo1high3 <- Vomax * O2 / (O2 + Ko * (1 + Cc1high3 / Kc)) 
A1high3 <- vc - 0.5 * vo1high3 - R 
vo2high3 <- Vomax * O2 / (O2 + Ko * (1 + Cc2high3 / Kc)) 
A2high3 <- vc - 0.5 * vo2high3 - R 
vo4high3 <- Vomax * O2 / (O2 + Ko * (1 + Cc4high3 / Kc)) 
A4high3 <- vc - 0.5 * vo4high3 - R 

Ahigh3 <-
  c(A0125high3, A025high3, A05high3, A1high3, A2high3, A4high3)

Cidiffusion <- c(Cim0125, Cim025, Cim05, Cim1, Cim2, Cim4)
Alow <- c(A0125low, A025low, A05low, A1low, A2low, A4low)
Amedian <-
  c(A0125median,
    A025median,
    A05median,
    A1median,
    A2median,
    A4median)
Ahigh <- c(A0125high, A025high, A05high, A1high, A2high, A4high)

variable <- c(rep("Mesophyll Conductance", 6 * length(Cim1)))
conductance <-
  c(rep(0.125, 376),
    rep(0.25, 376),
    rep(0.5, 376),
    rep(1, 376),
    rep(2, 376),
    rep(4, 376))

Diffusionplot2 <-
  as.data.frame(
    cbind(
      Cidiffusion,
      Alow,
      Amedian,
      Ahigh,
      Ahigh2,
      Ahigh3,
      variable,
      conductance,
      resistance
    )
  )
write.csv(Diffusionplot2, "./data/DiffusionLimitsACI2.csv")
```

最终够到的不同导度下的扩散数据如下：


```r
knitr::kable(head(Diffusionplot2))
```


\begin{tabular}{l|l|l|l|l|l|l|l|l}
\hline
Cidiffusion & Alow & Amedian & Ahigh & Ahigh2 & Ahigh3 & variable & conductance & resistance\\
\hline
-14.0805894452511 & -4.87461753940542 & -4.86419435199041 & -4.81536251060599 & -4.74710314038274 & -4.68025068475633 & Mesophyll Conductance & 0.125 & 11\\
\hline
-11.8541114570766 & -4.72133615031493 & -4.71094118846005 & -4.66224099010836 & -4.59416400241068 & -4.52748832337504 & Mesophyll Conductance & 0.125 & 11\\
\hline
-9.63096032171488 & -4.56847050260645 & -4.55810365181624 & -4.50953456487788 & -4.44163922971142 & -4.37513962701612 & Mesophyll Conductance & 0.125 & 11\\
\hline
-7.41112252124025 & -4.41601890716257 & -4.40568005356001 & -4.35724154927421 & -4.28952714053524 & -4.22320291762348 & Mesophyll Conductance & 0.125 & 11\\
\hline
-5.19458461086401 & -4.26397968400382 & -4.25366871432613 & -4.20536026677158 & -4.13782606222113 & -4.07167652620537 & Mesophyll Conductance & 0.125 & 11\\
\hline
-2.98133321844053 & -4.11235116222692 & -4.10206796382144 & -4.05388904989726 & -3.98653433113539 & -3.9205587927733 & Mesophyll Conductance & 0.125 & 11\\
\hline
\end{tabular}


## 扩散限制作图 {#multi13}


```r
data <- read.csv("./data/DiffusionLimitsACI2.csv")
data$resistance <- as.factor(data$resistance)
graph <- ggplot(data, aes(x = Cidiffusion, y = Ahigh3, colour = resistance)) +
  geom_abline(slope=0,intercept=0,size=1.5)+
  geom_point()+
  labs(colour = 'Total Resistance')+
  scale_colour_brewer(palette = 'Spectral') +
  theme_bw() +
  scale_x_continuous(limits=c(0,100))+
  scale_y_continuous(limits=c(-5,10))+
  theme(
    axis.title = element_text(size = 18),
    axis.text = element_text(size = 15),
    legend.position = 'right',
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank()
  )
graph
```

```
## Warning: Removed 1863 rows containing missing values (geom_point).
```

![(\#fig:difuci)不同 Ci 扩散限制下的差异](bookdown_files/figure-latex/difuci-1.pdf) 



```r
datahigh <- data[data$resistance == "11",]
graph <- ggplot(datahigh, aes(x = Cidiffusion, y = Ahigh3)) +
  geom_abline(slope=0,intercept=0,size=1.5)+
  geom_point(colour = "red")+
  geom_point(aes(x = Cidiffusion, y = Ahigh2), colour = "blue")+
  geom_point(aes(x = Cidiffusion, y = Ahigh), colour = "green")+
  labs(colour = 'Total Resistance')+
  scale_colour_brewer(palette = 'Spectral') +
  theme_bw() +
  scale_x_continuous(limits=c(0,100))+
  scale_y_continuous(limits=c(-2.5,2.5))+
  theme(
    axis.title = element_text(size = 18),
    axis.text = element_text(size = 15),
    legend.position = 'right',
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank()
  )
graph
```

```
## Warning: Removed 340 rows containing missing values (geom_point).

## Warning: Removed 340 rows containing missing values (geom_point).

## Warning: Removed 340 rows containing missing values (geom_point).
```

![(\#fig:difuci11)不同 Ci 扩散限制下的差异（resistance = 11）](bookdown_files/figure-latex/difuci11-1.pdf) 



```r
data2 <- read.csv("./data/DiffusionLimits.csv")
graph <- ggplot(data2, aes(x = TotalRes, y = Rates, colour = Scenario)) +
  geom_point()+
  scale_colour_brewer(palette = 'Spectral') +
  theme_bw() +
  theme(
    axis.title = element_text(size = 18),
    axis.text = element_text(size = 12),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank()
  )
graph
```

![(\#fig:tolresis)不同总导度下的各个导度的速率变化](bookdown_files/figure-latex/tolresis-1.pdf) 



```r
m1 <- lm(Rates ~ I(1/TotalRes)+I((1/TotalRes)^2),data=data2)
summary(m1)
```

```
## 
## Call:
## lm(formula = Rates ~ I(1/TotalRes) + I((1/TotalRes)^2), data = data2)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.3590 -0.9844 -0.1236  0.7706  2.4510 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         13.042      1.684   7.745 3.18e-06 ***
## I(1/TotalRes)      421.928     22.487  18.763 8.49e-11 ***
## I((1/TotalRes)^2) -737.690     65.500 -11.262 4.46e-08 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.705 on 13 degrees of freedom
## Multiple R-squared:  0.9904,	Adjusted R-squared:  0.9889 
## F-statistic: 668.8 on 2 and 13 DF,  p-value: 7.799e-14
```

```r
ab <- predict(m1)
red <- 1/data2$TotalRes
plot(ab~red)
```

![(\#fig:tolre)不同阻力下的各个导度的速率变化预测值](bookdown_files/figure-latex/tolre-1.pdf) 

### 补偿点的计算 {#multi14}

计算补偿点，代码同前文类似，只是采用了不同导度下的数值：


```r
data <- read.csv("./data/DiffusionLimitsACI2.csv")
#Gamma
# For Ci-based estimates, only use Ci < 100
dataCi <- data[data$Cidiffusion < 100, ]
dataCi0125 <- dataCi[dataCi$conductance == "0.125", ]
dataCi025 <- dataCi[dataCi$conductance == "0.25", ]
dataCi05 <- dataCi[dataCi$conductance == "0.5", ]
dataCi1 <- dataCi[dataCi$conductance == "1", ]
dataCi2 <- dataCi[dataCi$conductance == "2", ]
dataCi4 <- dataCi[dataCi$conductance == "4", ]
m1 <- lm(dataCi0125$Ahigh ~ dataCi0125$Cidiffusion)
summary(m1)
```

```
## 
## Call:
## lm(formula = dataCi0125$Ahigh ~ dataCi0125$Cidiffusion)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.040649 -0.014495  0.005307  0.017054  0.021232 
## 
## Coefficients:
##                          Estimate Std. Error t value Pr(>|t|)    
## (Intercept)            -3.836e+00  4.318e-03  -888.5   <2e-16 ***
## dataCi0125$Cidiffusion  6.665e-02  7.873e-05   846.6   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.01934 on 52 degrees of freedom
## Multiple R-squared:  0.9999,	Adjusted R-squared:  0.9999 
## F-statistic: 7.167e+05 on 1 and 52 DF,  p-value: < 2.2e-16
```

```r
Gamma0125 <- -m1$coefficients[1] / m1$coefficients[2]
m2 <- lm(dataCi025$Ahigh ~ dataCi025$Cidiffusion)
summary(m2)
```

```
## 
## Call:
## lm(formula = dataCi025$Ahigh ~ dataCi025$Cidiffusion)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.070876 -0.025427  0.009071  0.029765  0.036593 
## 
## Coefficients:
##                         Estimate Std. Error t value Pr(>|t|)    
## (Intercept)           -5.2507346  0.0092187  -569.6   <2e-16 ***
## dataCi025$Cidiffusion  0.0904227  0.0001544   585.5   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.03329 on 59 degrees of freedom
## Multiple R-squared:  0.9998,	Adjusted R-squared:  0.9998 
## F-statistic: 3.428e+05 on 1 and 59 DF,  p-value: < 2.2e-16
```

```r
Gamma025 <- -m2$coefficients[1] / m2$coefficients[2]
m3 <- lm(dataCi05$Ahigh ~ dataCi05$Cidiffusion)
summary(m3)
```

```
## 
## Call:
## lm(formula = dataCi05$Ahigh ~ dataCi05$Cidiffusion)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.10435 -0.03583  0.01358  0.04311  0.05340 
## 
## Coefficients:
##                        Estimate Std. Error t value Pr(>|t|)    
## (Intercept)          -6.4051036  0.0150363    -426   <2e-16 ***
## dataCi05$Cidiffusion  0.1097686  0.0002391     459   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.04851 on 65 degrees of freedom
## Multiple R-squared:  0.9997,	Adjusted R-squared:  0.9997 
## F-statistic: 2.107e+05 on 1 and 65 DF,  p-value: < 2.2e-16
```

```r
Gamma05 <- -m3$coefficients[1] / m3$coefficients[2]
m4 <- lm(dataCi1$Ahigh ~ dataCi1$Cidiffusion)
summary(m4)
```

```
## 
## Call:
## lm(formula = dataCi1$Ahigh ~ dataCi1$Cidiffusion)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.13150 -0.04513  0.01709  0.05402  0.06692 
## 
## Coefficients:
##                       Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         -7.1817472  0.0199135  -360.6   <2e-16 ***
## dataCi1$Cidiffusion  0.1227771  0.0003085   398.0   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.06074 on 69 degrees of freedom
## Multiple R-squared:  0.9996,	Adjusted R-squared:  0.9996 
## F-statistic: 1.584e+05 on 1 and 69 DF,  p-value: < 2.2e-16
```

```r
Gamma1 <- -m4$coefficients[1] / m4$coefficients[2]
m5 <- lm(dataCi2$Ahigh ~ dataCi2$Cidiffusion)
summary(m5)
```

```
## 
## Call:
## lm(formula = dataCi2$Ahigh ~ dataCi2$Cidiffusion)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.14803 -0.04874  0.01837  0.05995  0.07511 
## 
## Coefficients:
##                       Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         -7.6427727  0.0230595  -331.4   <2e-16 ***
## dataCi2$Cidiffusion  0.1305083  0.0003538   368.9   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.06815 on 71 degrees of freedom
## Multiple R-squared:  0.9995,	Adjusted R-squared:  0.9995 
## F-statistic: 1.361e+05 on 1 and 71 DF,  p-value: < 2.2e-16
```

```r
Gamma2 <- -m5$coefficients[1] / m5$coefficients[2]
m6 <- lm(dataCi4$Ahigh ~ dataCi4$Cidiffusion)
summary(m6)
```

```
## 
## Call:
## lm(formula = dataCi4$Ahigh ~ dataCi4$Cidiffusion)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.15721 -0.05258  0.01994  0.06470  0.07968 
## 
## Coefficients:
##                       Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         -7.8955772  0.0248634  -317.6   <2e-16 ***
## dataCi4$Cidiffusion  0.1347503  0.0003799   354.7   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.07225 on 72 degrees of freedom
## Multiple R-squared:  0.9994,	Adjusted R-squared:  0.9994 
## F-statistic: 1.258e+05 on 1 and 72 DF,  p-value: < 2.2e-16
```

```r
Gamma4 <- -m6$coefficients[1] / m6$coefficients[2]

GammaCi <- c(Gamma0125, Gamma025, Gamma05, Gamma1, Gamma2, Gamma4)
```

### 所有图形代码 {#multi15}



```r
data <- read.csv("./data/PRdata.csv")
data$Ccfull <- as.numeric(data$Ccfull)
data$Delay <- as.factor(data$Delay)
cols <- gg_color_hue(6)
Panel_1A <-
  ggplot(data, aes(
    x = Cifull,
    y = Anet,
    colour = Delay,
    linetype = Delay
  )) +
  # 零水平参考线
  geom_abline(slope = 0,
              intercept = 0,
              size = 1.5) +
  #ci与A loess 方法的拟合曲线
  geom_smooth(method = "loess", se = FALSE, size = 2) +
  scale_linetype_manual(
    name = "Delay (s)",
    labels = c("0", "15", "30", "60", "120", "300"),
    values = c("solid", "longdash", "twodash", "dotdash", "dashed", "dotted")
  ) +
  ggtitle(expression(paste(
    bold("(a)"), " Modelled Photorespiratory Effect"
  ))) +
  labs(x = expression(C[i] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[net] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Delay (s)') +
  scale_x_continuous(limits = c(0, 250)) +
  scale_y_continuous(limits = c(-5, 20)) +
  scale_colour_manual(
    values = cols,
    name = "Delay (s)",
    labels = c("0", "15", "30", "60", "120", "300")
  ) +
  theme_bw() +
  theme(
    axis.title.x = element_text(size = 20),
    axis.title.y = element_text(size = 20),
    axis.line = element_line(size = 1),
    axis.ticks = element_line(size = 1.5),
    axis.ticks.length = unit(1.5, "mm"),
    rect = element_rect(size = 2),
    axis.text.x = element_text(size = 14, color = 'black'),
    axis.text.y =
      element_text(
        size = 14,
        color = 'black',
        hjust = (1)
      ),
    legend.position = c(0.15, 0.75),
    axis.title = element_text(size = 18),
    axis.text = element_text(size = 12, color = "black"),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    legend.text = element_text(size = 14),
    legend.title = element_text(size = 14)
  )
Panel_1A
```

```
## Warning: Removed 1029 rows containing non-finite values (stat_smooth).
```

![(\#fig:prdataplot)不同时间滞后性的 Anet VS Ci](bookdown_files/figure-latex/prdataplot-1.pdf) 



```r
Panel_1A_inset <-
  ggplot(data, aes(
    x = Cifull,
    y = Anet,
    colour = Delay,
    linetype = Delay
  )) +
  geom_abline(slope = 0,
              intercept = 0,
              size = 1.5) +
  geom_smooth(method = "loess", se = FALSE, size = 2) +
  scale_linetype_manual(
    name = "Delay (s)",
    labels = c("0", "15", "30", "60", "120", "300"),
    values = c("solid", "longdash", "twodash", "dotdash", "dashed", "dotted")
  ) +
  labs(x = expression(C[i] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[net] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Delay (s)') +
  scale_x_continuous(limits = c(45, 65)) +
  scale_y_continuous(limits = c(-1, 1), breaks = c(-1, 0, 1)) +
  scale_colour_manual(values = cols) +
  theme_bw() +
  theme(
    axis.title.x = element_blank(),
    axis.title.y = element_blank(),
    axis.line = element_line(size = 1),
    axis.ticks = element_line(size = 1.5),
    axis.ticks.length = unit(1.5, "mm"),
    rect = element_rect(size = 2),
    axis.text.x = element_text(size = 14, color = 'black'),
    axis.text.y =
      element_text(
        size = 14,
        color = 'black',
        hjust = (1)
      ),
    axis.title = element_blank(),
    axis.text = element_text(size = 12, color = "black"),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    legend.position = 'none',
    plot.background = element_blank()
  )



cols <- gg_color_hue(6)

data <- read.csv("./data/DiffusionLimitsACI2.csv")
data$resistance <- as.factor(data$resistance)
Panel_1Db <-
  ggplot(data,
         aes(
           x = Cidiffusion,
           y = Ahigh,
           colour = resistance,
           linetype = resistance
         )) +
  geom_abline(slope = 0,
              intercept = 0,
              size = 1.5) +
  geom_smooth(method = "loess", se = FALSE, size = 2) +
  scale_linetype_manual(
    name = "Total Resistance",
    labels = c("3.25", "3.5", "4", "5", "7", "11"),
    values = c("solid", "longdash", "twodash", "dotdash", "dashed", "dotted")
  ) +
  ggtitle(expression(paste(bold("(d)"), " Modelled Resistance Effect"))) +
  scale_color_manual(
    values = cols,
    name = "Total Resistance",
    labels = c("3.25", "3.5", "4", "5", "7", "11")
  ) +
  theme_bw() +
  labs(x = expression(paste(C[i] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")")), y = expression(paste(A[net] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")"))) +
  scale_x_continuous(limits = c(0, 250)) +
  scale_y_continuous(limits = c(-5, 20)) +
  theme(
    axis.title.x = element_text(size = 20),
    axis.title.y = element_text(size = 20),
    axis.line = element_line(size = 1),
    axis.ticks = element_line(size = 1.5),
    axis.ticks.length = unit(1.5, "mm"),
    rect = element_rect(size = 2),
    axis.text.x = element_text(size = 14, color = 'black'),
    axis.text.y =
      element_text(
        size = 14,
        color = 'black',
        hjust = (1)
      ),
    legend.position = c(0.22, 0.75),
    axis.title = element_text(size = 18),
    axis.text = element_text(size = 12, color = "black"),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    legend.text = element_text(size = 14),
    legend.title = element_text(size = 14)
  )
Panel_1Db
```

```
## Warning: Removed 1159 rows containing non-finite values (stat_smooth).
```

![(\#fig:resistplot)不同总阻力下的 Anet VS Ci](bookdown_files/figure-latex/resistplot-1.pdf) 



```r
data <- read.csv("./data/DiffusionLimitsACI2.csv")
data$resistance <- as.factor(data$resistance)
data <- data[data$resistance == "11",]
resistance <- rep(data$resistance, 3)
Cidiffusion <- rep(data$Cidffusion, 3)
A <- c(data$Ahigh, data$Ahigh2, data$Ahigh3)
Rate <-
  c(rep(100, length(data$Ahigh)), rep(200, length(data$Ahigh2)), rep(300, length(data$Ahigh3)))
data3 <- rbind(data, data, data)
data3$A <- c(data$Ahigh, data$Ahigh2, data$Ahigh3)
data3$Rate <-
  as.factor(c(rep(100, length(data$Ahigh)), rep(200, length(data$Ahigh2)), rep(300, length(data$Ahigh3))))
Panel_1Dbinset <-
  ggplot(data3, aes(
    x = Cidiffusion,
    y = A,
    colour = Rate,
    linetype = Rate
  )) +
  geom_abline(slope = 0,
              intercept = 0,
              size = 1.5) +
  geom_smooth(method = "loess", se = FALSE, size = 2) +
  scale_linetype_manual(
    name = "Rate",
    labels = c("100", "200", "300"),
    values = c("solid", "longdash", "twodash")
  ) +
  scale_color_manual(
    values = cols,
    name = "Rate",
    labels = c("100", "200", "300")
  ) +
  theme_bw() +
  labs(x = expression(paste(C[i] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")")), y = expression(paste(A[net] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")"))) +
  scale_x_continuous(limits = c(45, 65)) +
  scale_y_continuous(limits = c(-1, 1), breaks = c(-1, 0, 1)) +
  theme(
    axis.title.x = element_blank(),
    axis.title.y = element_blank(),
    axis.line = element_line(size = 1),
    axis.ticks = element_line(size = 1.5),
    axis.ticks.length = unit(1.5, "mm"),
    rect = element_rect(size = 2),
    axis.text.x = element_text(size = 14, color = 'black'),
    axis.text.y =
      element_text(
        size = 14,
        color = 'black',
        hjust = (1)
      ),
    legend.position = c(0.22, 0.75),
    axis.title = element_text(size = 18),
    axis.text = element_text(size = 12, color = "black"),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    plot.background = element_blank(),
    legend.text = element_text(size = 12),
    legend.key = element_blank(),
    legend.title = element_blank(),
    legend.background = element_blank()
  )
Panel_1Dbinset
```

```
## Warning: Removed 1101 rows containing non-finite values (stat_smooth).
```

![(\#fig:high3tplot)不同导度下的 A VS Ci](bookdown_files/figure-latex/high3tplot-1.pdf) 



```r
data2 <- read.csv("./data/DiffusionLimits.csv")
data2$Conductance <-
  revalue(
    data2$Scenario,
    c(
      "Boundary Layer Conductance" = "Boundary Layer",
      "Mesophyll Conductance" = "Mesophyll",
      "Stomatal Conductance" = "Stomatal"
    )
  )
cols <- gg_color_hue(3)

Panel_1E <-
  ggplot(data2, aes(x = TotalRes, y = Rates, colour = Conductance)) +
  geom_point(size = 4) +
  ggtitle(expression(paste(bold("(e)"), " Modelled Resistance Effect"))) +
  labs(x = expression(paste(r[total] * " (s" ~ m ^ {
    2
  } ~ mol ^ {
    -1
  } * ")")), 
  y = expression(paste(C[c] * " Ramp Rate (" * mu * mol ~ mol ^
                                    {
                                      -1
                                    } ~ min ^ {
                                      -1
                                    } * ")"))) +
  scale_x_continuous(limits = c(0, 50)) +
  scale_y_continuous(limits = c(0, 80)) +
  scale_color_manual(
    values = cols,
    name = "Manipulated Resistance",
    labels = c("Boundary Layer", "Mesophyll", "Stomatal")
  ) +
  theme_bw() +
  theme(
    axis.title.x = element_text(size = 20),
    axis.title.y = element_text(size = 20),
    axis.line = element_line(size = 1),
    axis.ticks = element_line(size = 1.5),
    axis.ticks.length = unit(1.5, "mm"),
    rect = element_rect(size = 2),
    axis.text.x = element_text(size = 14, color = 'black'),
    axis.text.y =
      element_text(
        size = 14,
        color = 'black',
        hjust = (1)
      ),
    legend.position = c(0.70, 0.84),
    axis.title = element_text(size = 18),
    axis.text = element_text(size = 12, color = "black"),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    legend.text = element_text(size = 14),
    legend.title = element_text(size = 14)
  )
Panel_1E
```

![(\#fig:toresratetplot)不同总导度下的各个导度的变化](bookdown_files/figure-latex/toresratetplot-1.pdf) 



```r
data <- read.csv("./data/RASdata.csv")
data$Ccfull <- as.numeric(data$Ccfull)
data$Deactivation <- as.factor(data$Deactivation)
print(levels(data$Deactivation))
```

```
## [1] "High"   "Low"    "Medium" "None"
```

```r
data$Deactivation <-
  factor(data$Deactivation, levels(data$Deactivation)[c(4, 2, 3, 1)])
FigS1 <-
  ggplot(data,
         aes(
           x = Cifull,
           y = Anet,
           colour = Deactivation,
           linetype = Deactivation
         )) +
  geom_smooth(method = "loess", se = FALSE, size = 2) +
  scale_linetype_manual(
    name = "Deactivation",
    labels = c("None", "Low", "Medium", "High"),
    values = c("solid", "longdash", "dashed", "dotted")
  ) +
  labs(x = expression(C[i] ~ "(" * mu * mol ~ mol ^ {
    -1
  } * ")"),
  y = expression(A[net] ~ "(" * mu * mol ~ m ^ {
    -2
  } ~ s ^ {
    -1
  } * ")")) +
  labs(colour = 'Deactivation') +
  scale_x_continuous(limits = c(25, 100),
                     breaks = c(25, 40, 55, 70, 85, 100)) +
  scale_y_continuous(limits = c(-5, 5)) +
  geom_hline(yintercept = 0, size = 1.5) +
  theme_bw() +
  theme(
    axis.title.x = element_text(size = 20),
    axis.title.y = element_text(size = 20),
    axis.line = element_line(size = 1),
    axis.ticks = element_line(size = 1.5),
    axis.ticks.length = unit(1.5, "mm"),
    rect = element_rect(size = 2),
    axis.text.x = element_text(size = 14, color = 'black'),
    axis.text.y =
      element_text(
        size = 14,
        color = 'black',
        hjust = (1)
      ),
    legend.position = c(0.8, 0.2),
    legend.text = element_text(size = 16),
    legend.title = element_text(size = 16),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank()
  )
FigS1
```

```
## Warning: Removed 1230 rows containing non-finite values (stat_smooth).
```

![(\#fig:deasratetplot)Rubisco不同失活程度下的下 A VS Ci](bookdown_files/figure-latex/deasratetplot-1.pdf) 


\cleardoublepage

<!--chapter:end:12-5-multiracir.Rmd-->

## LI-6800 荧光数据分析 {#fluro68}

LI-6800 能够直接提供基本的叶绿素荧光参数，其他参数均可通过这些基本的参数进行计算，计算也较简单，在此不赘述，需要注意的是快相荧光部分的数据，因为分析 ojip 数据的模型有很多，很多都需要复杂的计算，在此我们先将其中较为简单的 jip test 数据分析进行介绍。

### jip test 的实现 {#jiptest}

LI-6800 增加了 ojip 曲线测量功能，本功能主要是针对测量数据的 jip test 的实现。

### `jiptest` 软件包安装 {#jiptest_pack}

请在 github 安装，有可能需要安装 `remotes`


```r
install.packages("remotes")
```

然后安装：


```r
install_github("zhujiedong/jiptest")
```

如果因为网络原因失败，可以转战 gitee:


```r
install_git('https://gitee.com/zhu_jie_dong/jiptest')
```


### `read_files` 及 `read_dcfiles` 函数 {#readfluor}

`read_files` 用于批量读取所有调制光测量数据，方便用于其他的数据分析。**函数要求所有数据必须是 `xlsx` 格式，并且所有测量数据都保存在同一文件夹内。**，如有其他文件或测量数据则会报错。
`read_dcfiles` 用于批量读取所有连续光测量数据，其他与 `read_dcfiles` 相同。例如我放在了 data文件夹下的 ojip 文件夹内，有五个数据文件。

函数仅有一个参数，即保存数据文件夹的路径，使用如下：


```r
library(jiptest)
jip_data<- read_files("data/ojip")
```


```r
jip_dcdata <- read_dcfiles("./data/ojip")
```

调制光的信号前 10 行数据：

\begin{table}

\caption{(\#tab:unnamed-chunk-69)jiptest 批量导入数据后的样式}
\centering
\begin{tabular}[t]{rrrl}
\toprule
SECS & FLUOR & NORM\_FLUOR & SOURCE\\
\midrule
6.10e-06 & 621 & 0.0005027 & INDUCTION-4188-20201116-10\_53\_39\\
1.01e-05 & 620 & 0.0000000 & INDUCTION-4188-20201116-10\_53\_39\\
1.42e-05 & 649 & 0.0145781 & INDUCTION-4188-20201116-10\_53\_39\\
1.80e-05 & 629 & 0.0045242 & INDUCTION-4188-20201116-10\_53\_39\\
2.21e-05 & 674 & 0.0271455 & INDUCTION-4188-20201116-10\_53\_39\\
\addlinespace
2.61e-05 & 736 & 0.0583126 & INDUCTION-4188-20201116-10\_53\_39\\
\bottomrule
\end{tabular}
\end{table}

连续光的信号前 10 行数据：

\begin{table}

\caption{(\#tab:unnamed-chunk-70)jiptest DC 数据批量导入数据后的样式}
\centering
\begin{tabular}[t]{rrrl}
\toprule
SECS & FLUOR & NORM\_FLUOR & SOURCE\\
\midrule
6.10e-06 & 98161 & 0.0000000 & INDUCTION-4188-20201116-10\_53\_39\\
1.01e-05 & 100331 & 0.0071347 & INDUCTION-4188-20201116-10\_53\_39\\
1.42e-05 & 102577 & 0.0145193 & INDUCTION-4188-20201116-10\_53\_39\\
1.80e-05 & 104849 & 0.0219894 & INDUCTION-4188-20201116-10\_53\_39\\
2.21e-05 & 107214 & 0.0297652 & INDUCTION-4188-20201116-10\_53\_39\\
\addlinespace
2.61e-05 & 109624 & 0.0376890 & INDUCTION-4188-20201116-10\_53\_39\\
\bottomrule
\end{tabular}
\end{table}

> 注：NORM_FLUOR 是标准化后的荧光信号数据，其中标准化采用的方式是 @stirbetChlorophyllFluorescenceInduction2012 所采用的：$F = \frac{F_t - F_o}{F_m - F_o}$，而曲线上部的面积则采用 @stirbetRelationKautskyEffect2011 的方式进行标准化，以方便不同叶片之间的比较，具体为 $area = \frac{area}{F_m - F_o}$

### `jip_test` 及 `jip_dctest` 函数 {#testfluor}

 `jiptest` 是 jiptest 的核心函数，用于所有数据的 `jiptest` 分析，函数仅包一个参数，测量数据的保存文件夹路径。
 `jip_dctest` 与 `jip_test` 相似，用于连续光测量数据的分析。
 

```r
actest <- jip_test("./data/ojip")
```


```r
dctest <- jip_dctest("./data/ojip")
```

可以看出，对于标准化之后的参数，二者基本一致，原始信号则无法一致，因为连续光信号强度更高（所以数据的比较要在相同的测量光内进行）：

调制光信号的计算参数：


\begin{longtable}[t]{lrrrrr}
\caption{(\#tab:jipres)jiptest 输出的计算参数}\\
\toprule
parameters & data\_file1 & data\_file2 & data\_file3 & data\_file4 & data\_file5\\
\midrule
Fo & 620.000 & 887.000 & 849.000 & 981.000 & 914.000\\
Fm & 2609.280 & 4649.520 & 3714.270 & 4225.240 & 3754.270\\
F300 & 1593.200 & 2181.400 & 1798.600 & 2067.800 & 2537.200\\
FJ & 1722.900 & 2594.300 & 2104.600 & 2456.800 & 2569.000\\
FI & 2268.950 & 4443.110 & 3547.110 & 4009.220 & 3556.530\\
\addlinespace
Tfmax & 159.998 & 256.016 & 316.016 & 272.016 & 200.016\\
\bottomrule
\end{longtable}

连续光信号计算参数：


\begin{longtable}[t]{lrrrrr}
\caption{(\#tab:dcjipres)jiptest DC 数据输出的计算参数}\\
\toprule
parameters & data\_file1 & data\_file2 & data\_file3 & data\_file4 & data\_file5\\
\midrule
Fo & 98161.000 & 154000.000 & 133191.000 & 156664.000 & 153589.000\\
Fm & 402308.000 & 713839.000 & 560610.000 & 647660.000 & 584839.000\\
F300 & 246753.000 & 338091.000 & 275476.800 & 322859.200 & 388110.400\\
FJ & 267827.000 & 400744.000 & 320245.000 & 376885.000 & 406671.000\\
FI & 349985.000 & 681505.000 & 534314.000 & 614590.000 & 552253.000\\
\addlinespace
Tfmax & 171.998 & 276.016 & 300.016 & 288.016 & 212.016\\
\bottomrule
\end{longtable}

计算参数见表 \@ref(tab:jipres) 及 \@ref(tab:dcjipres)，考虑到排版，仅显示部分内容。若需要将数据导出，可以使用相关命令，如：


```r
# export the results of jiptest to a csv file
write.csv(actest, "d:/data/jip_resluts.csv")
write.csv(dctest, "d:/data/dcjip_resluts.csv")
```


### 图像查看函数 {#plotfluor}

之前的预览方式已经彻底从代码中删除，不在提供。如果仅仅是简单查看结果，那么我们直接用默认参数就好，作图的数据是之前导入的数据文件：

默认使用标准化的荧光信号（也就是除以最大值，标准化到 0~1 之间，LI-6800 在 1.4 之后的版本也提供了仪器上查看的界面）。

#### 默认图形的方式 {#default-plot-jiptest}

1. 默认调制光图形 


```r
plot(jip_data)
```

![(\#fig:default-moulation-plot-ac)默认调制光图形查看](bookdown_files/figure-latex/default-moulation-plot-ac-1.pdf) 

2. 默认连续光图形 


```r
plot(jip_dcdata)
```

![(\#fig:default-moulation-plot-dc)默认连续光图形查看](bookdown_files/figure-latex/default-moulation-plot-dc-1.pdf) 

默认图形，仅仅查看结果是可以的，当然有些情况下需要进行调整才方便做展示用。

#### 定制化图形的方式 {#customized-plot-jiptest}

这个是这次改动的重点之一，例如都使用原始信号分别做上面两幅图：

1. 定制调制光图形

这里使用原始荧光信号，然后更改图例颜色等，主要是 `add_leg = FALSE` 不增加默认的图例，以及 `normalized = FALSE` 不使用信号标准化。其他请参考帮助 `?plot.jip`。 我简单写了一下帮助文档，大家应该基本能看明白，我实在没时间。


```r
cls <- palette.colors(n = 5,  "set 2", alpha = 0.8)
plot(jip_data,
     ylab = 'Normalized fluorescence signals',
     add_leg = FALSE,
     def_pch = 14:18,
     col = cls,
     main = "Demodulated signals", normalized = FALSE)

legend(
  "topleft",
  unique(jip_data$SOURCE),
  col = cls,
  pch = 14:18,
  cex = 0.6,
  pt.cex = 1.2,
  bty = "n")
```

![(\#fig:cus-ac-plot-leg-cls)定制调制光图形示例](bookdown_files/figure-latex/cus-ac-plot-leg-cls-1.pdf) 

2. 定制连续光图形

连续光图形，只改了两个地方，图例的位置以及不显示标准化的数据。


```r
plot(jip_dcdata, legend_pos = "bottomright", normalized = FALSE)
```

![(\#fig:cus-dc-plot-leg-cls)定制连续光图形示例](bookdown_files/figure-latex/cus-dc-plot-leg-cls-1.pdf) 

**关于标准化和非标准化图形差异大的问题，这个比较简单，原始信号比较低的那条线，是不同物种的不同叶片，所以不具备比较价值，我只是数据测试。**

---

**建议在开始分析数据前，使用如上方式作图查看数据质量，若使用调制光数据测量的荧光信号太弱，数据点太散，则可以使用连续光测量信号进行分析，对于归一化的荧光参数，二者几乎无差别，当然避免陷入被动的最好方式还是最开始测量时注意检查数据质量**

\cleardoublepage

<!--chapter:end:12-6-jiptest.Rmd-->

# 大叶模型 {#big-leaf}

题目中所提到的大叶模型，并非指用涡动数据来进行大尺度的直接测量后的大叶模型 [@knauerBigleafPackageCalculation2018]，是指 @mejdovaPhotosyntheticParametersSedgegrass2021 使用 LI-6400 和 LI-3000C 的方式进行的个体叶片尺度的光合测量，外推到整个群落尺度的大叶模型，这也是一个比较新的测量方法，发表在 Scientific Reports。这里对其方法进行概述，因为所用方法是之前早都提过的基本方式。

## 叶片尺度测量 {#leaf-scale-meas}

文章中所使用的是 LI-6400，但毫无疑问，LI-6800 可以更好的胜任该工作，具体测量方法为：

1. 从 4 月中旬到 10 月，除 6 月因为洪水有中断外，每周进行一次光响应曲线的测量。
2. 为最小化几个优势种测量时样本之间的变异性，考虑了枝条的差异、枝条不同叶片之间的年龄、以及环境的随机效应的影响。测量选取的是一系列相邻的枝条或草丛。在特定的日期，每个植株选择两个枝条上的，在完全展开的成熟叶片中选择最 2-4 最年轻的叶片进行测量（2片或4片，由物种而定）。
3. 测量的不为距离叶片顶端约 3/4，选择的都是冠层顶部的叶片。
4. 测量过程是标准的光响应曲线的测量流程，不同的是在光强设置为 0 时，额外等待 4 min，用于测量暗呼吸速率。
5. 该实验测量的时间是欧洲中部时间的 7-11 点。

## 数据的处理 {#big-leaf-data}

### 单个测量数据的处理 {#single-data-big-leaf}

1. 单个测量的数据使用的是非线性拟合，选取的为非直角双曲线模型，使用了 `nls2` 作为非线性拟合的工具。
2. 暗呼吸速率的值是拟合模型曲线与纵坐标的交点。
3.不同物种、不同测量日期和不同生育期的参数各自拟合。

### 大叶模型的数据处理 {#big-leaf-data-MODEL}

1. 在不同季节，使用 LI-3100C 测量了不同物种的叶面积指数。
2. 计算单个植株的 LAI 占不同时期以及整个生长季 LAI 的比例，以此作为该植株拟合参数的权重。而整个大叶模型的参数则是对这些权重数据进行求和。然后将这些参数带入所用的非直角双曲线模型。

\begin{figure}

{\centering \includegraphics[width=15.68in]{./images/weighted-parameter} 

}

\caption{根据 LAI 权重求和来计算大叶模型的参数}(\#fig:lai-big-leaf)
\end{figure}

光响应曲线的拟合可以参考 \@ref(nonrec-mod)。部分实验结果如下：

\begin{figure}

{\centering \includegraphics[width=8.94in]{./images/big-leaf-model} 

}

\caption{假定不同物种组成下的模型结果}(\#fig:lai-big-leaf-model)
\end{figure}

<!--chapter:end:13-1-big-leaf.Rmd-->

# 大话 PCA {#pca-anylysis}

PCA 作为一个基础的方法，应用领域覆盖很广，涵盖的统计学相关的内容有均值、标准差、协方差， 线性代数中的特征向量及特征值。当然在与光合仪相关的应用方面多数是测量的光合速率、气孔导度等配合植物的其他性状来进行研究，其适用范围，用现在流行的统计学习术语来讲，应属于非监督性学习，即我们并非预测某变量同其预测值的关系，如同我们获得了一系列包含植物光合性状在内的性状，但我们并非将其预测某些结果，如同响应曲线那般，而是用这些形状来分类或者看齐对某目的的重要程度。本章的标题之所以叫大话，是因为这是我结合我自己的理解写的内容，如有错误请谅解，请指出。

另，推荐两个我认为很好的解释 PCA 的链接：

[BioTuring's Blog](https://blog.bioturing.com/2018/06/14/principal-component-analysis-explained-simply/)

[Principal Component Analysis](http://setosa.io/ev/principal-component-analysis/)

## 几何解释 {#geom-pca}

PCA 通常能够反映我们复杂数据集的一些分布特征，例如典型的 `iris` 数据集，我们看到了三个鸢尾种的四个性状：花萼的长宽、花瓣的长宽，如果给我们这么一个数据，我们肉眼很难看出他们的特征来，如果作图也很难表达，因为这是一个四维的数据，假设我们只对前两组也就是花萼的长宽作图，先看一下二维的数据情况：

![](bookdown_files/figure-latex/2dim-1.pdf)<!-- --> 

看上去一言难尽，此时的缺点非常明显，图中的每个点均代表了某个植株的两个性状，这个性状类似于我们说的电子云，杂乱而无规律。这还只是二维数据，如果我们将所有四个轴的数据都显示也就是四维的数据，那么对于我等肉眼凡胎来讲，还是不看为妙。

我在看到上面留的两个网址之前，对主成份分析的疑惑一直没断过，什么是主成份，一直说是主轴旋转，到底是怎么旋转的，我们看一下主成份的 PC1 是怎么来做的:

\begin{figure}
\includegraphics[width=1\linewidth]{images/pc1} \caption{数据的中心化}(\#fig:pccenter)
\end{figure}

如图 \@ref(fig:pccenter)，假设有三维的数据，可以理解为 iris 数据中前三列的观测值，每个点分别代表了一个植株的前三个性状，PCA 第一步便是中心化，将坐标轴由左边转移到右边的中心点位置作为原点，这就是所谓的平均值中心化的过程，这样就消除了测量时的偏差 ^[https://www.mathsisfun.com/definitions/bias.html]，同时一般还要对数据进行缩放，以消除不同单位的影响 ^[https://en.wikipedia.org/wiki/Scaling_(geometry)]，
例如有光合速率，有叶片面积的数据，这样能够消除不同单位的影响，通常的缩放例如对数化。

\begin{figure}
\includegraphics[width=1\linewidth]{images/pc2} \caption{PC1 的诞生}(\#fig:pc2)
\end{figure}

移动坐标系后，因为进行了缩放，所有的数据具有相同的比例。我们这时做一条最佳的拟合线(图 \@ref(fig:pc2))，此时发生了两件重要的事情：

* 所有的点投影到这条拟合线上，投影后的点之间的距离是最大的，该方向代表了所有变异最大，也就是最大化的区分所有的数据点，因为我们的目的就是要区分数据点之间的差异。如果在读的各位有跟我一样空间想象力有限的，可以根据图 \@ref(fig:maxvar) 的极端情况来理解，蓝色线为最佳拟合线，红色的数据点投影到其上的距离之和当然大于投影到蓝色的拟合线上。

![(\#fig:maxvar)变异最大的极端情况](bookdown_files/figure-latex/maxvar-1.pdf) 

* 所有点和他们相对应的点之间的距离是最小的（也就是残差最小，最佳拟合当然是残差最小），这保证了这些投影的点和原来的数据点尽可能接近，也就是变化后损失的信息最少。

以上变化的最终结果为将变异放到最大而误差降低到最小，我们研究的最终目的无非就是这样。这个最佳拟合的线我们将其称之为 PC1，主成份1。

\begin{figure}
\includegraphics[width=1\linewidth]{images/pc3} \caption{PC2 的诞生}(\#fig:pc3)
\end{figure}
如果我们做一条通过中心点并与 PC1 垂直的线，并不断旋转这条线使其满足：所有投影于其上的点，他们的距离最大，也就是变异最大，这样就做出了第二个主成份，PC2，如图 \@ref(fig:pc3)。**就这样不断的变换，一般情况下，我们可以使用 2~3 个主成份来解释绝大部分数据所展示的信息（所有数据都尽可能少的损失信息投影于其上）**。

以上为基本变换，如果我们使用 R 计算 PCA (这里推荐两个包 `factoextra` 和 `FactoMineR`)，查看其结果：

```r
library("FactoMineR")
library("factoextra")
```

```
## Welcome! Want to learn more? See two factoextra-related books at https://goo.gl/ve3WBa
```

```r
ir <- iris[, 1:4]
ir_pca <- PCA(ir, graph = FALSE)

fviz_eig(ir_pca, addlabels = TRUE)
```

![(\#fig:scree)iris 碎石图](bookdown_files/figure-latex/scree-1.pdf) 

我们可以看到，前两个成分可以解释数据 95% 以上的变异，所以我们可以只使用主成份 1 和主成份 2 对我们的结果进行解释。

既然我们使用两个主成份解释整个数据，那么我们怎么把四个性状都放在这个图上呢？这就需要载荷图来展示了，如图 \@ref(fig:loadingpca)


```r
fviz_pca_var(ir_pca)
```

![(\#fig:loadingpca)iris 载荷图](bookdown_files/figure-latex/loadingpca-1.pdf) 

怎么解释呢，因为原始的数据点相当于在坐标轴上，这几个性状分布的产生类似于原始的数据点在拉拽某个性状，影响强的偏向某一方向，换句话说，**这些性状决定主成份时有不同的权重，也就是对主成份影响的权重大小。同时他们是有方向的，如果两个性状方向接近，也就是夹角非常小，说明他们有较强的正相关性，例如图中的花瓣的长宽，如果他们互相垂直，那说明他们无相关性，例如图中花萼的长宽，如果夹角非常大，例如他们几乎在一条直线上，说明具有显著的负相关**。其本质还是所有的单位向量（本例有四个性状，各个性状中心化后形成的轴，也就是向量）在现在的这个二维平面的位置。

而如果我想通过这些性状来对鸢尾分类，怎么看呢？那就需要得分图来展示了，如图 \@ref(fig:scorepca) ，得分图本质是各个品种的每个观测值在各个轴上的长度，也就是得分，所以其值都是每个物种性状的加权，每个点代表了每个物种的观测值。


```r
fviz_pca_ind(ir_pca, repel = TRUE, col.ind= iris$Species)
```

```
## Warning: ggrepel: 85 unlabeled data points (too many overlaps). Consider
## increasing max.overlaps
```

![(\#fig:scorepca)iris 得分图](bookdown_files/figure-latex/scorepca-1.pdf) 

我们明显看出来，PC1 方向，三个种的鸢尾分成了两大部分，其中 Setosa 延 PC1 同其他两个明显有区别，另外两个在 PC1 上也有较明显的区别，但二者有一定的重合，延 PC2 方向上，三者差别不大。或者反过来讲也可以，品种的差别对 PC1 影响比较显著，这也和我们的碎石图相匹配。

## 线性代数解释 {#alge-pca}

### 特征向量与特征值 {#egi-pca}

特征向量和特征值的数学描述为：

\begin{equation}
A \vec{x} = \lambda \vec{x}
(\#eq:eign)
\end{equation}

其中 A 为 n $\times$ n 的矩阵，$\vec{x}$ 为 非零  n $\times$ 1 列向量，$\lambda$ 是标量，那么 $\vec{x}$ 为 A 的特征向量，$\lambda$ 为 A 的特征值。

如果仅从数学描述上看，是十分不适合我这样没基础的人来理解的，但我们可以这么理解：

A 为我们观测值（也即我们生态学上测量的数据）组成的 $n \times n$ 矩阵，每行的数据代表了一个观测值，例如光合速率是其中的一行，我们有n 个， 那么 $\vec{x}$ 和 $\lambda$ 意味着什么呢？再看一眼上面的 \@ref(eq:eign)，这是一个等式，而 $\vec{x}$ 是 n $\times$ 1 维度，原来的数据是 n $\times$ n 维度，这就是主轴分析是降维分析的意义了，一个一维的特征向量和特征值与我们多维的观测值之间有一个相等的关系。当然，也有很多人在描述时喜欢用坐标轴旋转或这投影来形容的，如果你觉得这样好理解也没问题，不过我脑袋不擅长想象这种多维的空间。这样描述并不十分准确，只是我觉得便于理解，更正确描述应为我们下面所述的例子 (@Smith2002) ，但降维的原理即来自此处：

\begin{equation}
finaldata = rowfeaturevector \times rowdataajust
(\#eq:despca)
\end{equation}

finaldata 很好理解，我们用于 PCA 的最终数据，rowfeature vector 则是我们根据协相关矩阵求得的特征向量的转置，最大的特征向量再最上面， rowdataadjust 则为**原始数据减去标准值**。

因为我手头没有相关的数据，想来想去，我们在 R 里见到最多的与生态学相关的数据也就是 iris，一个关于不同 iris 品种的叶片性状相关的数据，非常生态，我们还是继续使用：


\begin{tabular}{r|r|r|r|l}
\hline
Sepal.Length & Sepal.Width & Petal.Length & Petal.Width & Species\\
\hline
5.1 & 3.5 & 1.4 & 0.2 & setosa\\
\hline
4.9 & 3.0 & 1.4 & 0.2 & setosa\\
\hline
4.7 & 3.2 & 1.3 & 0.2 & setosa\\
\hline
4.6 & 3.1 & 1.5 & 0.2 & setosa\\
\hline
5.0 & 3.6 & 1.4 & 0.2 & setosa\\
\hline
5.4 & 3.9 & 1.7 & 0.4 & setosa\\
\hline
\end{tabular}

数据并不复杂，但是想要得出一些规律性的东西确不那么容易，因为数据量太大了。R 语言里面很多函数可以直接实现 PCA，例如 \@ref(geom-pca) 内容。这里按照最原始的方法实现一下，加深对原理的理解：

### 手动实现过程 {#man_pca}

#### 均值的计算 {#av_val}


```r
iris_mean <- apply(iris[, 1:4], 2, function(x) x - mean(x))
```

我只使用了前四列的性状数据，仅仅是方便结果的展示。

#### 计算协方差矩阵 {#cov_val}


```r
iris_cov <- cov(iris_mean)
```

#### 计算特征值和特征向量 {#eig_val}


```r
iris_eigen <- eigen(iris_cov)
```

这里就可以看到之前提到的，特征值和特征向量是根据协方差矩阵计算的。我们选取特征值最大的两个，他们的特征值之和已经占所有的特征值之和相当大的比例：


```r
sum(iris_eigen$values[1:2])/sum(iris_eigen$values)
```

```
## [1] 0.9776852
```

也就是说特征值对应的前两项是我们最终降维所需要的，即最终的两个主轴为 $150 \times 2$ 维矩阵，即我们原来的 $150 \times 4$ 的矩阵乘以我们选取的前两个特征值对应的 $4 \times 2$ 组成的矩阵。

实际应用中我们不需要这么做了，因为太浪费时间了，我们直接用函数来得到结果即可。

### `prcomp` 的实现 {#prcom}

我们用 `prcomp` 来简化实现过程，注意，根据 @kemp2003modern，我们把 iris 数据对数化一下，并提取所有物种名字：


```r
data("iris")
iris_pca <- log(iris[, 1:4])
iris_species <- iris$Species
```
需要注意，我们处理时使用特征中心化。即每一维的数据都减去该维的均值。这里的“维”指的就是一个特征（或属性），变换之后每一维的均值都变成了0 (参考 \@ref(eq:despca) )。


```r
value_pca <- prcomp(iris_pca, center = TRUE, scale. = TRUE)
## 查看结果
summary(value_pca)
```

```
## Importance of components:
##                           PC1    PC2     PC3     PC4
## Standard deviation     1.7125 0.9524 0.36470 0.16568
## Proportion of Variance 0.7331 0.2268 0.03325 0.00686
## Cumulative Proportion  0.7331 0.9599 0.99314 1.00000
```

可以看到结果同我们最开始的计算相似，不同的是我们进行了对数化，而且根据 @kemp2003modern， 这个结果更合适。

# 环境与配置 {#sessioninfo}

本文的内容所完成的 sessioninfo 如下：


```r
sessionInfo()
```

```
## R version 4.1.0 (2021-05-18)
## Platform: x86_64-w64-mingw32/x64 (64-bit)
## Running under: Windows 10 x64 (build 19042)
## 
## Matrix products: default
## 
## locale:
## [1] LC_COLLATE=Chinese (Simplified)_China.936 
## [2] LC_CTYPE=Chinese (Simplified)_China.936   
## [3] LC_MONETARY=Chinese (Simplified)_China.936
## [4] LC_NUMERIC=C                              
## [5] LC_TIME=Chinese (Simplified)_China.936    
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
##  [1] factoextra_1.0.7   FactoMineR_2.4     jiptest_0.1.3      gridExtra_2.3     
##  [5] plyr_1.8.6         nls2_0.2           proto_1.0.0        purrr_0.3.4       
##  [9] ggplot2_3.3.5      minpack.lm_1.2-1   plantecophys_1.4-6 readphoto_0.0.1   
## 
## loaded via a namespace (and not attached):
##  [1] nlme_3.1-152         RColorBrewer_1.1-2   tools_4.1.0         
##  [4] backports_1.2.1      utf8_1.2.1           R6_2.5.0            
##  [7] DT_0.18              DBI_1.1.1            mgcv_1.8-36         
## [10] colorspace_2.0-2     MESS_0.5.7           withr_2.4.2         
## [13] tidyselect_1.1.1     rematch_1.0.1        curl_4.3.2          
## [16] compiler_4.1.0       flashClust_1.01-2    labeling_0.4.2      
## [19] bookdown_0.22        mosaicCore_0.9.0     scales_1.1.1        
## [22] readr_1.4.0          ggridges_0.5.3       stringr_1.4.0       
## [25] digest_0.6.27        foreign_0.8-81       ggformula_0.10.1    
## [28] rmarkdown_2.9.7      rio_0.5.27           pkgconfig_2.0.3     
## [31] htmltools_0.5.1.1    geeM_0.10.1          labelled_2.8.0      
## [34] highr_0.9            htmlwidgets_1.5.3    rlang_0.4.11        
## [37] readxl_1.3.1         rstudioapi_0.13      farver_2.1.0        
## [40] generics_0.1.0       zip_2.2.0            car_3.0-11          
## [43] dplyr_1.0.7          plantecophys2_0.1.0  magrittr_2.0.1      
## [46] leaps_3.1            Matrix_1.3-4         Rcpp_1.0.6          
## [49] munsell_0.5.0        fansi_0.5.0          abind_1.4-5         
## [52] geepack_1.3-2        lifecycle_1.0.0      scatterplot3d_0.3-41
## [55] stringi_1.6.2        yaml_2.2.1           carData_3.0-4       
## [58] MASS_7.3-54          ggstance_0.3.5       grid_4.1.0          
## [61] ggrepel_0.9.1        forcats_0.5.1        crayon_1.4.1        
## [64] lattice_0.20-44      haven_2.4.1          splines_4.1.0       
## [67] hms_1.1.0            knitr_1.33           pillar_1.6.1        
## [70] ggpubr_0.4.0         ggsignif_0.6.2       glue_1.4.2          
## [73] evaluate_0.14        data.table_1.14.0    png_0.1-7           
## [76] vctrs_0.3.8          tweenr_1.0.2         cellranger_1.1.0    
## [79] gtable_0.3.0         polyclip_1.10-0      tidyr_1.1.3         
## [82] assertthat_0.2.1     openxlsx_4.2.4       xfun_0.24           
## [85] ggforce_0.3.3        broom_0.7.8          rstatix_0.7.0       
## [88] tibble_3.1.2         cluster_2.1.2        ellipsis_0.3.2
```

\cleardoublepage

<!--chapter:end:13-2-pca.Rmd-->




<!--chapter:end:14-reference.Rmd-->

