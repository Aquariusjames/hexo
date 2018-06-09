---
title: '[转]ROC曲线-阈值评价标准'
tags: [ROC, 机器学习, 分类]
categories: [IT, 机器学习]
date: 2016-04-11 12:47:51
---

<span style="font-size:14px; line-height:24px; font-family:arial,宋体,sans-serif">ROC曲线指受试者工作特征曲线 / 接收器操作特性曲线(receiver operating characteristic curve), 是反映敏感性和特异性连续变量的综合指标,是用构图法揭示敏感性和特异性的相互关系，它通过将连续变量设定出多个不同的临界&#20540;，从而计算出一系列敏感性和特异性，再以敏感性为纵坐标、（1-特异性）为横坐标绘制成曲线，曲线下面积越大，诊断准确性越高。在ROC曲线上，最靠近坐标图左上方的点为敏感性和特异性均较高的临界&#20540;。</span><span style="font-family:Arial; font-size:14px; line-height:26px"></span>

<span style="font-family:arial,宋体,sans-serif"><span style="line-height:24px">

</span></span>

<span style="font-family:arial,宋体,sans-serif"><span style="line-height:24px"></span></span>

##
<span class="headline-content">ROC曲线的例子</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">　　考虑一个二分问题，即将实例分成正类（positive）或负类（negative）。对一个二分问题来说，会出现四种情况。如果一个实例是正类并且也被 预测成正类，即为真正类（True positive）,如果实例是负类被预测成正类，称之为假正类（False positive）。相应地，如果实例是负类被预测成负类，称之为真负类（True negative）,正类被预测成负类则为假负类（false
 negative）。</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif; background-color:rgb(255,255,153)">TP：正确肯定的数目；</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif; background-color:rgb(255,255,153)">FN：漏报，没有正确找到的匹配的数目；</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif; background-color:rgb(255,255,153)">FP：误报，给出的匹配是不正确的；</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif; background-color:rgb(255,255,153)">TN：正确拒绝的非匹配对数；</span>

<div class="spctrl" style="font-size:14px; height:14px; line-height:14px; overflow:hidden; font-family:arial,宋体,sans-serif">
</div>
<span style="font-size:14px; line-height:24px; font-family:arial,宋体,sans-serif">　　列联表如下表所示，1代表正类，0代表负类。</span><span style="font-family:Arial; font-size:14px; line-height:26px"></span><span style="font-family:Arial; font-size:14px; line-height:26px"></span>
<div class="spctrl" style="font-size:14px; height:14px; line-height:14px; overflow:hidden; font-family:arial,宋体,sans-serif">
</div>
<span style="font-size:14px; line-height:24px; font-family:arial,宋体,sans-serif">　　</span><span style="font-family:Arial; font-size:14px; line-height:26px"></span><span style="font-family:Arial; font-size:14px; line-height:26px"></span>
<table class="table-view log-set-param       " style="color:rgb(0,0,0); font-family:Arial; font-size:14px; line-height:26px">
<tbody>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
&nbsp;</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
&nbsp;</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
预测</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
&nbsp;</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
&nbsp;</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
&nbsp;</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
1</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
0</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
合计</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
实际</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
1</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
True Positive（TP）</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
False Negative（FN）</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
Actual Positive(TP&#43;FN)</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
&nbsp;</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
0</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
False Positive（FP)</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
True Negative(TN)</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
Actual Negative(FP&#43;TN)</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
合计</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
&nbsp;</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
Predicted Positive(TP&#43;FP)</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
Predicted Negative(FN&#43;TN)</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
TP&#43;FP&#43;FN&#43;TN</td>
</tr>
</tbody>
</table>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">从列联表引入两个新名词。其一是真正类率(true positive rate ,<span style="color:rgb(255,0,0)">TPR</span>), 计算公式为</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">TPR=TP</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">/
 (</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">TP</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">&#43;&nbsp;</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">FN</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">)，刻画的是分类器所识别出的
 正实例占所有正实例的比例。另外一个是负正类率(false positive rate,&nbsp;<span style="color:rgb(255,0,0)">FPR</span>),计算公式为</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">FPR= FP / (FP &#43; TN)，</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">计算的是分类器错认为正类的负实例占所有负实例的比例。还有一个真负类率（True
 Negative Rate，TNR），也称为specificity,计算公式为TNR=</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">TN</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">/ (</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">FP</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">&#43;&nbsp;</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">TN</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">)
 = 1-</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">FPR</span><span style="line-height:24px; font-family:arial,宋体,sans-serif">。</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">![](http://my.csdn.net/uploads/201206/20/1340175403_3476.jpg)

</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">其中，两列True matches和True non-match分别代表<span style="background-color:rgb(204,255,255)">应该</span>匹配上和<span style="background-color:rgb(204,255,255)">不应该</span>匹配上的</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">两行Pred matches和Pred non-match分别代表<span style="background-color:rgb(204,255,255)">预测</span><span style="line-height:23px">匹配上和预测</span><span style="line-height:23px; background-color:rgb(204,255,255)">不</span><span style="line-height:23px">匹配上的</span></span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif"><span style="line-height:23px">![](http://my.csdn.net/uploads/201206/20/1340175807_2760.jpg)

</span></span>

<div class="spctrl" style="font-size:14px; height:14px; line-height:14px; overflow:hidden; font-family:arial,宋体,sans-serif">
</div>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">　　在一个二分类模型中，对于所得到的连续结果，假设已确定一个阀&#20540;，比如说 0.6，大于这个&#20540;的实例划归为正类，小于这个&#20540;则划到负类中。如果减小阀&#20540;，减到0.5，固然能识别出更多的正类，也就是提高了识别出的正例占所有正例 的比类，即TPR,但同时也将更多的负实例当作了正实例，即提高了FPR。为了形象化这一变化，在此引入ROC，<span style="background-color:rgb(255,255,153)">ROC曲线可以用于评价一个分类器</span>。</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">![](http://my.csdn.net/uploads/201206/20/1340176083_5411.jpg)

</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">ROC曲线和它相关的比率</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">(a)理想情况下，TPR应该接近1，FPR应该接近0。</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">ROC曲线上的每一个点对应于一个threshold，<span style="background-color:rgb(255,255,153)">对于一个分类器，每个threshold下会有一个TPR和FPR</span>。</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">比如Threshold最大时，TP=FP=0，对应于原点；Threshold最小时，TN=FN=0，对应于右上角的点(1,1)</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">(b)P和N得分不作为特征间距离d的一个函数，随着阈&#20540;theta增加，TP和FP都增加</span>

<span style="font-family:arial,宋体,sans-serif"><span style="line-height:23px">

</span></span>

<div class="spctrl" style="font-size:14px; height:14px; line-height:14px; overflow:hidden; font-family:arial,宋体,sans-serif">
</div>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">　　Receiver Operating Characteristic,翻译为&quot;接受者操作特性曲线&quot;，够拗口的。曲线由两个变量1-specificity 和 Sensitivity绘制. 1-specificity=FPR，即负正类率。Sensitivity即是真正类率，TPR(True positive rate),反映了正类覆盖程度。这个组合以1-specificity对sensitivity,即是以代价(costs)对收益(benefits)。</span>

<span style="line-height:24px; font-family:arial,宋体,sans-serif">&nbsp; &nbsp; &nbsp; &nbsp;此外，ROC曲线还可以用来计算“均&#20540;平均精度”（<span style="background-color:rgb(255,255,153)">mean average precision</span>），这是当你通过改变阈&#20540;来选择最好的结果时所得到的平均精度（PPV）.</span>

<div class="spctrl" style="font-size:14px; height:14px; line-height:14px; overflow:hidden; font-family:arial,宋体,sans-serif">
</div>
<span style="font-size:14px; line-height:24px; font-family:arial,宋体,sans-serif">　　下表是一个逻辑回归得到的结果。将得到的实数&#20540;按大到小划分成10个个数 相同的部分。</span><span style="font-family:Arial; font-size:14px; line-height:26px"></span><span style="font-family:Arial; font-size:14px; line-height:26px"></span>
<div class="spctrl" style="font-size:14px; height:14px; line-height:14px; overflow:hidden; font-family:arial,宋体,sans-serif">
</div>
<span style="font-size:14px; line-height:24px; font-family:arial,宋体,sans-serif">　　</span><span style="font-family:Arial; font-size:14px; line-height:26px"></span><span style="font-family:Arial; font-size:14px; line-height:26px"></span>
<table class="table-view log-set-param       " style="color:rgb(0,0,0); font-family:Arial; font-size:14px; line-height:26px">
<tbody>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
Percentile</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
实例数</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
正例数</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
1-特异度(%)</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
敏感度(%)</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
10</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6180</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
4879</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
2.73</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
34.64</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
20</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6180</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
2804</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
9.80</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
54.55</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
30</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6180</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
2165</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
18.22</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
69.92</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
40</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6180</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
1506</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
28.01</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
80.62</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
50</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6180</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
987</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
38.90</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
87.62</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
60</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6180</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
529</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
50.74</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
91.38</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
70</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6180</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
365</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
62.93</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
93.97</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
80</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6180</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
294</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
75.26</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
96.06</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
90</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6180</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
297</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
87.59</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
98.17</td>
</tr>
<tr>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
100</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
6177</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
258</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
100.00</td>
<td style="margin:0px; padding:0px 10px; border:1px solid rgb(221,221,221); line-height:22px">
100.00</td>
</tr>
</tbody>
</table>
<span style="font-size:14px; line-height:24px; font-family:arial,宋体,sans-serif">其正例数为此部分里实际的正类数。也就是说，将逻辑回归得到的结 果按从大到小排列，倘若以前10%的数&#20540;作为阀&#20540;，即将前10%的实例都划归为正类，6180个。其中，正确的个数为4879个，占所有正类的 4879/14084*100%=34.64%，即敏感度；另外，有6180-4879=1301个负实例被错划为正类，占所有负类的1301 /47713*100%=2.73%,即1-特异度。以这两组&#20540;分别作为x&#20540;和y&#20540;，在excel中作散点图。</span>
