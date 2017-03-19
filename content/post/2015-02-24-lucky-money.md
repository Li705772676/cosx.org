---
title: 嘿，朋友，抢红包了吗？
date: '2015-02-24T08:28:29+00:00'
author: 邱怡轩
categories:
  - 优化与模拟
  - 统计计算
tags:
  - Dirichlet分布
  - 基尼系数
  - 新年
  - 模拟
  - 红包
slug: lucky-money
---

如果你有一台智能手机，如果你装了一个名叫微信的软件，那么你今年的春节很可能是在下面这样的场景中度过的（图片来自微信群）：

[<img class="aligncenter size-full wp-image-10669" src="https://cos.name/wp-content/uploads/2015/02/lucky_money_3.gif" alt="lucky_money_3" width="240" height="320" />](https://cos.name/wp-content/uploads/2015/02/lucky_money_3.gif) [<img class="aligncenter size-full wp-image-10668" src="https://cos.name/wp-content/uploads/2015/02/lucky_money_2.gif" alt="lucky_money_2" width="300" height="225" />](https://cos.name/wp-content/uploads/2015/02/lucky_money_2.gif) [<img class="aligncenter size-full wp-image-10667" src="https://cos.name/wp-content/uploads/2015/02/lucky_money_1.gif" alt="lucky_money_1" width="280" height="208" />](https://cos.name/wp-content/uploads/2015/02/lucky_money_1.gif)

这也使得众多的网络大V发出了下面的感慨：

[<img class="aligncenter wp-image-10671 size-full" src="https://cos.name/wp-content/uploads/2015/02/lucky_money_4.png" alt="lucky_money_4" width="824" height="207" srcset="https://cos.name/wp-content/uploads/2015/02/lucky_money_4.png 824w, https://cos.name/wp-content/uploads/2015/02/lucky_money_4-300x75.png 300w, https://cos.name/wp-content/uploads/2015/02/lucky_money_4-500x126.png 500w" sizes="(max-width: 824px) 100vw, 824px" />](https://cos.name/wp-content/uploads/2015/02/lucky_money_4.png)

而最近几天不少微信群里面又流行起来一种“红包接力”的玩法，大概的规则是：群里面先由一人发一个红包，然后大家开始抢，其中金额最大的那个人继续发新一轮的红包，之后不断往复循环。

这时候大家或许就会问了，一直这么玩下去会有什么结果呢？是“闷声赚大钱”了，还是“错过几个亿”了？是最终实现“共同富裕”了，还是变成“寡头垄断”了？要回答这些问题，我们不妨用统计模拟的方法来做一些随机实验，得到的结果或许会让你大跌眼镜呢。

<!--more-->

### 模拟红包发放

要进行模拟实验，就需要设定一个红包金额的分配机制。但由于微信红包的算法并没有公开，所以在这里我们使用了一个简化的模型，即假设抢到红包的人分得的金额占总金额的比例（每个人对应一个比例，所以这是一个向量，且其总和为1）服从一个 Dirichlet 分布，参数为 $\vec{\alpha}=(\alpha, \alpha, \ldots, \alpha)$，即 $\vec{\alpha}$ 是一个各分量都相等的向量，由一个参数 $\alpha$ 决定。对于不熟悉 Dirichlet 分布的读者，可以参考 <a href="https://cos.name/2013/01/lda-math-beta-dirichlet/" target="_blank">rickjin 大侠的文章</a>以及 <a href="http://en.wikipedia.org/wiki/Dirichlet_distribution" target="_blank">Dirichlet 分布的维基页面</a>。

在这个简化的模型里，$\alpha$ 决定了红包发放的“公平”程度。$\alpha$ 越大，每人分得的金额比例就越倾向于平均，反之则波动性越大。下面展示了两组 Dirichlet 分布的随机数，我们假定红包金额分成5份，分别考察 $\alpha=1$ 和 $\alpha=10$ 时的金额比例，其中每一行是一次模拟。本文的最后附上了在R中生成 Dirichlet 分布随机数的程序。

<pre class="r"><code class="r">&lt;span class="identifier">rdir&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="number">3&lt;/span>, &lt;span class="identifier">rep&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="number">1&lt;/span>, &lt;span class="number">5&lt;/span>&lt;span class="paren">)&lt;/span>&lt;span class="paren">)  ## alpha = 1&lt;/span></code></pre>

    ##            [,1]        [,2]       [,3]       [,4]       [,5]
    ## [1,] 0.02669467 0.248426309 0.23745777 0.35864430 0.12877695
    ## [2,] 0.32398351 0.472000506 0.11704852 0.08291485 0.00405262
    ## [3,] 0.29309424 0.080879260 0.09338722 0.03765236 0.49498692
    

<pre class="r"><code class="r">&lt;span class="identifier">rdir&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="number">3&lt;/span>, &lt;span class="identifier">rep&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="number">10&lt;/span>, &lt;span class="number">5&lt;/span>&lt;span class="paren">)&lt;/span>&lt;span class="paren">)  ## &lt;/span>&lt;span class="identifier">alpha = 10&lt;/span></code></pre>

    ##           [,1]      [,2]      [,3]      [,4]      [,5]
    ## [1,] 0.2459250 0.2722147 0.1717301 0.1398133 0.1703169
    ## [2,] 0.1956686 0.2058377 0.1540937 0.3322662 0.1121338
    ## [3,] 0.1729287 0.1807517 0.1597919 0.2075967 0.2789310
    

可以看出，当 $\alpha=1$ 时，金额分配的变动性非常大，比如对于第一组随机数，如果红包总金额是100，那么领得最多的人可以得到35.86元，而最少的只有2.67元。而在 $\alpha=10$的情形下，金额的分配就平均多了。

### 模拟接力游戏

下面我们就来模拟红包接力的游戏。首先假设我们有一个50人的群，每人初始手头上的可用金额为50元（这里是为了产生“破产”现象而故意放低的，土豪们请忽略此设定），根据规则，每次红包的总金额是20元，发放给10个人，其中抢得最大红包金额的人将发出下一轮的红包。如果某人发完红包后余额变成了负值，就不能再继续抢红包（请原谅这个丧心病狂的设定……）。

本文最后给出了模拟红包接力的R语言代码，其中我们依然做了很多简化，比如假设抢到红包的人是在参与游戏的人中间均匀分布的（排除了资产为负的人）。在实际情况中，大家可能会根据自己余额的多少来决定是否继续参加，但在此我们忽略了这种可能。

我们让红包接力100次，最后大家的余额如下：

<pre id="rstudio_console_output" class="GEWYW5YBFEB" tabindex="0"><code class="r">&lt;span class="identifier">set.seed(123)
&lt;/span></code><span class="GEWYW5YBMDB ace_keyword">hb_experiment(param)$last_balance</span></pre>

    ##  [1]  31.2447  82.6933  18.0721  44.5619  62.8697
    ##  [6]  33.3992  47.0031  45.5506  77.1086  70.4357
    ## [11]  54.2828  26.9817  54.7438  80.3029  28.3159
    ## [16]  43.9803  48.8019  82.6942  82.9376 -10.9989
    ## [21]  34.3014  80.6406  60.6780  47.3356  40.1266
    ## [26]  52.5458  23.3861  62.6662  92.2020  72.4268
    ## [31]  41.5504  40.1163  50.5079  81.2957  51.1659
    ## [36]  43.3606  34.9284  64.3786  42.7006  -8.9036
    ## [41]   9.1015  78.6058  46.3494  64.1757  61.9002
    ## [46]  13.6061  50.0068  68.5091  41.2131  54.1413

可以看出，有两位朋友不幸破产了，而最后资产最多的有92.20元，几乎翻了一倍。一个很明显的事实是，破产的玩家都是因为“中头奖”中得太多了，导致入不敷出。相反，最终收得92.20元的这位玩家属于“闷声发大财”。经统计，“它”获得第二名3次，第三名2次，第四名2次，第五名4次，等等。

当然，概率面前人人平等，没有谁能预知自己抽中红包后会是最大的还是最小的，所以从对称性的角度考虑，个人选择的结果是完全随机的。但是，从整个群的角度来看，有一个指标却在悄悄发生变化，那就是这个群的“贫富差距”。

### 平均还是独大？

我们注意到，在游戏最开始的时候，大家的资金都是一样的（50元），而在100次接力之后，几家欢喜几家愁，贫富差距被拉大了。于是我们有两个很自然的问题：1. 如何量化这种贫富差距？2. 随着游戏的进程，贫富差距会有怎样的变化？

对于第一个问题，我们可以借用经济学中的一个概念来予以回答，那就是所谓的“基尼系数”（Gini Coefficient）。基尼系数通常被用来衡量一个国家居民收入的公平性，其取值在0到1之间，越大表示贫富差距越大，即少部分的人掌握了这个经济体大部分的收入。基尼系数的计算公式可以在它的<a href="http://en.wikipedia.org/wiki/Gini_coefficient" target="_blank">维基页面</a>中找到，本文最后也给出了相应的R代码。对于之前的模拟游戏结果，计算出的基尼系数是0.2551。

这个结果的绝对数值可能并没有太大的意义，因此我们在每一轮接力之后都计算出当时这个群的基尼系数，然后观察它的变化。结果如下：

[<img class="aligncenter size-large wp-image-10677" src="https://cos.name/wp-content/uploads/2015/02/lucky_money_5-500x375.png" alt="lucky_money_5" width="500" height="375" srcset="https://cos.name/wp-content/uploads/2015/02/lucky_money_5-500x375.png 500w, https://cos.name/wp-content/uploads/2015/02/lucky_money_5-300x225.png 300w, https://cos.name/wp-content/uploads/2015/02/lucky_money_5.png 800w" sizes="(max-width: 500px) 100vw, 500px" />](https://cos.name/wp-content/uploads/2015/02/lucky_money_5.png)在这里我们将接力次数延长到了500次。可以看出，随着接力的进行，基尼系数的整体趋势是在不断变大的，意味着贫富差距会随着游戏的进行变得越来越大。这其实很好理解：总是会有人因为拿了太多头奖而破产，这样财富会在越来越少的人中间进行分配，所以相应地贫富差距就拉大了。

为了更直观地展示50个玩家的余额变化，我们可以看一下下面的这个动画，其中每幅图代表一个玩家的资金余额。



### 红包越“公平”，贫富差越大

前面提到，在我们的模型中有一个参数 $\alpha$ 用来控制红包金额分配的“公平”程度（或者更准确地说，是“平均”的程度，因为就机会而言，每个人分得金额的可能性都是相同的，但就每一次实际分得的金额而言，$\alpha$ 越大，这种分配越倾向于平均，即结果的波动性越小）。下图展示了一组随机模拟实验的结果，其中我们模拟了20次红包接力的游戏，10次取 $\alpha=2$， 另外10次取 $\alpha=20$。每次游戏中，红包都接力了500次。

[<img class="aligncenter size-large wp-image-10679" src="https://cos.name/wp-content/uploads/2015/02/lucky_money_6-500x375.png" alt="lucky_money_6" width="500" height="375" srcset="https://cos.name/wp-content/uploads/2015/02/lucky_money_6-500x375.png 500w, https://cos.name/wp-content/uploads/2015/02/lucky_money_6-300x225.png 300w, https://cos.name/wp-content/uploads/2015/02/lucky_money_6.png 800w" sizes="(max-width: 500px) 100vw, 500px" />](https://cos.name/wp-content/uploads/2015/02/lucky_money_6.png)可以看出，红线和蓝线虽然有所重叠，但总体来看蓝线的取值要比红线更大，也就是说，**红包金额越“公平”，贫富差距反而会越大**。

这个结论看起来可能有些反直觉，但其实也合情合理：如果红包的分配是绝对公平的，那么第一名得到的金额就将是2元，而下一轮又必须送出20元，所以总共亏损18元；如果红包金额的波动性很大，就会有一部分人得到的金额小于2元，而第一名就会得到更多，也就更不容易破产。所以说，一个规则是否真的“公平”，不能只看其表面。

### 红包金额越滚越大

我们最后再看一个这样的规则：第一个红包金额为20元，下一个为21元，再下一个为22元……到了30元后，又逐渐递减至20元，以此反复。我们对代码稍作修改后，同样画出基尼系数的变化图，对比这两种规则产生的结果：

[<img class="aligncenter size-large wp-image-10682" src="https://cos.name/wp-content/uploads/2015/02/lucky_money_7-500x375.png" alt="lucky_money_7" width="500" height="375" srcset="https://cos.name/wp-content/uploads/2015/02/lucky_money_7-500x375.png 500w, https://cos.name/wp-content/uploads/2015/02/lucky_money_7-300x225.png 300w, https://cos.name/wp-content/uploads/2015/02/lucky_money_7.png 800w" sizes="(max-width: 500px) 100vw, 500px" />](https://cos.name/wp-content/uploads/2015/02/lucky_money_7.png)很明显，当红包的金额发生这种变动后，贫富差距又进一步拉大了。

### 更多

除了本文考察的这些可能影响金额分配的因素之外，感兴趣的读者还可以利用文中的代码（可能需要稍作修改）继续考察其他因素对贫富差距的影响，比如红包人数，初始金额等等。最后提醒大家的是，红包主要还是在过年的时候图个喜庆，游戏有风险，抢包需谨慎。:D

<img class="aligncenter" src="http://www.gpxz.com/uploads/allimg/140111/0155295X8_0.gif" alt="" width="197" height="213" />

**附：文本用到的R预言代码**

<pre id="rstudio_console_output" class="GEWYW5YBFEB" tabindex="0"><code class="r">&lt;span class="identifier">rdir&lt;/span> &lt;span class="operator">=&lt;/span> &lt;span class="keyword">function&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="identifier">n&lt;/span>, &lt;span class="identifier">shape&lt;/span>&lt;span class="paren">)&lt;/span>
&lt;span class="paren">{&lt;/span>
    &lt;span class="identifier">len&lt;/span> &lt;span class="operator">=&lt;/span> &lt;span class="identifier">length&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="identifier">shape&lt;/span>&lt;span class="paren">)&lt;/span>
    &lt;span class="identifier">mat&lt;/span> &lt;span class="operator">=&lt;/span> &lt;span class="identifier">matrix&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="identifier">rgamma&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="identifier">len&lt;/span> &lt;span class="operator">*&lt;/span> &lt;span class="identifier">n&lt;/span>, &lt;span class="identifier">shape&lt;/span>&lt;span class="paren">)&lt;/span>, &lt;span class="identifier">len&lt;/span>&lt;span class="paren">)&lt;/span>
    &lt;span class="identifier">sums&lt;/span> &lt;span class="operator">=&lt;/span> &lt;span class="identifier">colSums&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="identifier">mat&lt;/span>&lt;span class="paren">)&lt;/span>
    &lt;span class="identifier">t&lt;/span>&lt;span class="paren">(&lt;/span>&lt;span class="identifier">mat&lt;/span>&lt;span class="paren">)&lt;/span> &lt;span class="operator">/&lt;/span> &lt;span class="identifier">sums&lt;/span>
&lt;span class="paren">}
&lt;/span></code>
gini = function(x)
{
    x = sort(x)
    n = length(x)
    2 * sum(seq_along(x) * x) / n / sum(x) - (n + 1) / n
}<code class="r">&lt;span class="paren">

param = list(group_size = 50,    ## 群大小
             init_balance = 50,  ## 每人初始金额
             hb_amount = 20，    ## 每次红包的总金额
             hb_size = 10,       ## 红包发给多少人
             niter = 100，       ## 红包接力次数
             alpha = 2           ## 红包金额分配参数
             )

hb_experiment = function(p)
{
    id = 1:p$group_size  ## 群成员 ID 编号
    balance = rep(p$init_balance, p$group_size)  ## 当前每人资产
    
    bal = matrix(0, p$niter, p$group_size)
    for(i in 1:p$niter)
    {
        ## 破产的就别再玩啦
        players = id[balance &gt; 0]
        if(i == 1)
        {
            ## 第一轮随机挑一个人发红包
            host = sample(players, 1)
        } else {
            ## 后面就找抢得最多的
            host = winners[which.max(winner_amount)]
        }
        ## 红包主掏钱
        balance[host] = balance[host] - p$hb_amount
        ## 手快有，手慢无
        winners = sample(players, p$hb_size)
        ## 每人领取的红包金额
        winner_amount = p$hb_amount * c(rdir(1, rep(p$alpha, p$hb_size)))
        balance[winners] = balance[winners] + winner_amount
        bal[i, ] = balance
    }
    return(list(balance = bal, last_balance = balance))
}
&lt;/span>&lt;span class="identifier">
set.seed(123)
b = &lt;/span></code><span class="GEWYW5YBMDB ace_keyword">hb_experiment(param)$last_balance
gini(b)
</span></pre>

&nbsp;