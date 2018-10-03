
# Presentation 1 Name Disambiguation

本项目是用来对db中的论文信息进行处理，因为同一个名字可能指代很多个人，我们需要正确的把 ta们甄别出来。数据集从dblp中下载，选取下面链接中的0001-0010 “Jun Zhang” 数据集作为你方法的测试。

## 社交网络图pic

![原始.png](https://upload-images.jianshu.io/upload_images/5786775-79b18680f33a360a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Setup and Run

dblp的基本信息有：

```python
{
    "bibsource": "dblp computer science bibliography, https://dblp.org",
    "biburl": "https://dblp.org/rec/bib/journals/cma/Dai0W16", 
    "timestamp": "Sun, 28 May 2017 01:00:00 +0200", 
    "doi": "10.1016/j.camwa.2015.12.007", 
    "url": "https://doi.org/10.1016/j.camwa.2015.12.007", 
    "year": "2016",
    "pages": "431--442", 
    "number": "1",
    "volume": "71", 
    "journal": "Computers {\\&} Mathematics with Applications", 
    "title": "Higher order {ADI} method with completed Richardson extrapolation\nfor solving unsteady convection-diffusion equations",
    "author": "Ruxin Dai and\nJun Zhang and\nYin Wang", 
    "ENTRYTYPE": "article",
    "ID": "DBLP:journals/cma/Dai0W16"
}

```


```python

    pip3 install bibtexparser
    pip3 install nltk
    
    
    python3 main.py

```

----

# Louvain Community Detection

To use from the command line:

```bash

community <filename>

```


filename is a binary file as generated by the convert utility distributed with the C implementation

To use as a python library:


Louvain文档
https://github.com/taynaud/python-louvain
```python

import community



```

### 2018/10/01 

求出了每个单词在全部文章中的频率之后，剔除了 'using', 'problem', 'approach', 'method'这几个没有意义的单词后，
对单词频率进行排序后，取前100个单词作为特征向量。

这100个单词的提取方法在getFeature.py

提取后的单词存放在data/vocabularyFeature.txt

数据聚类：

### 2018/10/03

使用基于密度的DBSCAN，采用欧式距离算相似度求聚类。
参数的选取：


采用枚举寻找最小的噪声比，得到(15, 0, 0.9510204081632653, 1.0)这个较为优的结果

代码如下:

```python

EpsArray = np.linspace(0.1, 14).tolist()
MinNumArray = np.linspace(1 , 15).tolist()

def CountNoisyRate(labels):
    NoisyLen = len(list(filter(lambda labelType: labelType == -1, labels)))
    return NoisyLen

ResArray = []
for Eps in EpsArray:
    for MinNum in MinNumArray:
        tempClusternum, templabels = CountDBSCAN(Eps, MinNum, X)
        NoisyLen = CountNoisyRate(templabels)
        ResArray.append((tempClusternum, NoisyLen, Eps, MinNum))

ResArray = sorted(ResArray, key=lambda item: item[1])
print(ResArray)

```

但是，打印出来的结果发现多样性不足，所以再次调参结果是：

EPS: 0.81, MinNum: 1

```python
[  0   1   2   3   0   0   0   0   0   4   0   0   0   0   0   5   0   0
   0   6   7   7   0   0   1   8   0   4   3   3   0   0   9   4   4   0
   0   0   0  10   0  10   0   7   0   3   0   7   0   7   0   0   0  11
   0   0   7   3   0   0   0   0   0   7   0  12   7  13   0  14  15   0
   0  16   0   0  17   0   0   0   0   0   0   0   0   0   0   0   0   0
   3  16   0   0  18  18  19   0   0   0   0   0   0   5  20  21   5  22
  23   0  21   0   0  24   0   0  25  26  27   0  26   0   0  28  26   5
  29   0   0   0  21   0  21  26   0   3   0  21   0   0  30   0   0   0
   0   0  21   0   0  31   0   0   0  32  33   0  34  21   0   0  21  35
   7   0   0  21   0  36   0   0   0   0   0   0   0  21  21   0  21   0
   0   0  21  21   0  35   0  37  38  39   5   0   0  21   0   0  26  40
   0   0   0   0   0  21   0  41  21   0   0   0   0   0   0  39   0   0
  21  42  21   0  21  21  21  43  44   0   0   0   0  45   0   0   0   0
   0  21  21   0  21  39   0   0  46   0   0   0   0  47   0  46   0   0
  21   0  48  49  46   0   0   0   0   0   0   0   0   0   0   0   0   0
   0   0   0   0   0   0  50  26   0  51   0   0   0   0  52   0   0   0
   0   0   0   0   0   0  53   0   0   0   0   0   0   0   0   0   0  54
   0   0  55  56   0  57   0  54   0   0  58   1  59   4   1  60   1  61
  59  62  63   0   1  64  59  59   1  59  59   4   1   0  64   1   1  60
   1  64   1  65  59  58   1   1   1   1  66   1   1  66  67  59  59   1
  68   1   1  60  59   1   4   1   1   1   1   1   1   1  66  59   1  59
   1  59   1  59   1   1   0  69  60   0   1   0   1   1  70   1  60   4
  66   1   1   1   0   0   1   1   1  60  65   1  60  66  60   1  60   1
  67   0   0   1   1   1  60   0   1   1   1   1   1  71   1   1   1  60
  67  66  66   1   1   1   1   1   1  72   1   0   1   3   0   0   3   3
  72   1  72   1  72   0   5  62   1  72   1  62  58   0  73   0  73   0
  59   7  74   7   0  74   0   0  75   7  16  58   0   0   0   0  16   3
   3   0   3   0   3   3  76   3   0   3  77   7  78  79  79   3   7  80
  81   3   3   3   3   3  75   3   3   3   3   7   3   3   3   3   3  10
   3   3   3   7   3   3   3   3   7  82  82  54  83   3   3  84  85  86
  82  87  88  83   3  83  82  83  73  85   5   3  89   3   0  82   0  90
   1   3   0  54  91   5   1  75   1   0  58  92   0   3  93  94  95   0
   7   0  96   7   7   0   7  16   0   7   0   0   0   0   0  16  97   0
   0  98  98  99   0 100   4   3 101   4  59   0   0 102   3   0   0   0
   0  54  54  54 103 104  16 105  54   0   0   0   7 106   0   7  54 106
   3   0  54   0   0   0   0   0 107   0   0   3   0   9   0   0   0 108
  31 109   0   0   0   0   3   3   0  58   0   0   0   3   0   3   7   3
   3   3   3   0  75   7   0]
clusternum:  110
CountNoisyRate:  0
```





