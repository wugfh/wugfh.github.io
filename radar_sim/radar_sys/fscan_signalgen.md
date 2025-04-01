---
layout: default
title: F-SCAN 信号模型的建立
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

## 模型建立

### 模型建立
<center>  垂直航迹切面

![alt text](/assets/Fscan/cross_track.png)
</center>

卫星与地面的几何系统如上图所示。 $\beta$ 为天线法线与卫星和地心连线的角，$\phi$ 为 目标俯仰角。F-SCAN 天线结构类似于DBF-SCORE的接收端，在发送与接收两端均使用真时延相控阵雷达（True-Time-Delay Phased-Array。天线的每个通道间存在一个固定时延 $\tau$。设第一个通道为参考通道，则第 $n$ 个通道的时延为 $n \cdot \tau$ 。

<center>  天线单元构成 

![alt text](/assets/Fscan/发射单元构成.png)
</center>


每个单元均发送类似信号。假设参考通道发送线性调频信号。设 $f_c$ 为载频频率， $B$ 为信号带宽，而 $K = B/T_p$ 为信号的调频率, $c$ 为光速。则发送信号为

$$s_{ref}(t) = rect[t/T_p] \cdot e^{j(2 \pi f_c t + \pi K(t-T_p/2)^2)}$$   

设每个天线单元的相位中心间距为 $\phi$, 则第 $n$ 个 通道的发送信号为

$$s_n(t) = s_{ref}(t) \otimes \delta(t-n\tau+\frac{d \sin(\phi-\beta)}{c})$$

$\otimes$ 表示对两个信号进行卷积。对于地面上的某个点目标，其接收到的信号为所有通道延时后的发送信号之和。设雷达到目标的距离为 $R$，可以得到

$$ s(t) = \sum_{n=1}^{N}s_n(t) \otimes \delta(t-\frac{R}{c})$$

求和可以得到[1]

$$s(t) = [s_{ref}(t) \cdot h(f, \phi)] \otimes \delta(t - \frac{R}{c})$$

$$h(f, \phi)=\sin[N \cdot \pi f (\tau - \frac{d \sin (\phi-\beta)}{c})] / \sin[\pi f (\tau - \frac{d \sin (\phi-\beta)}{c})] \enspace f \in [f_c - B/2, f_c + B/2]$$

$ f \in [f_c - B/2, f_c + B/2]$ 为发射信号的瞬时频率。同样，接收到的回波信号为点目标反射信号，经过传播后到达各个通道的信号之和。接收端也使用真时延相控阵，通道间的时延与发送端相同。

$$s_r(t) = \sum_{n=1}^{N} s(t) \otimes \delta(t-\frac{R}{c}) \otimes \delta(t-n\tau+\frac{d \sin(\phi-\beta)}{c})$$

由于点目标的俯仰角确定，其所对应的频率也确定， $h(f, \phi)$ 与 时间无关，在时间域上进行卷积时可以视为常数，所以求和可以改写为
$$s_r(t) =\delta(t-\frac{2R}{c}) \otimes \sum_{n=1}^{N} [s_{ref}(t) \otimes \delta(t-n\tau+\frac{d \sin(\phi-\beta)}{c})] \cdot h(f, \phi) $$

完成求和，得到接收回波的解析式为

$$s_r(t) = [s_{ref}(t) \cdot h(f, \phi)^2] \otimes \delta(t-\frac{2R}{c}) $$

需要注意的是，虽然 $h(f, \phi)$ 与时间无关，但如果使用频率与时间相关的信号，如线性调频信号等，则可以在不同时间得到不同的 $f$。$f$ 的变化使得 $h(f, \phi)$ 的主瓣对准不同的 $\phi$ ，从而完成对成像区域的扫描。 $ h(f,\phi)^2$ 归一化响应如下图所示


<center>  天线方向图

![alt text](/assets/Fscan/ant_direction.png)

其中黑线为矩形天线单元的天线方向图，蓝线为 $ h(f,\phi)^2$ 的响应。天线参数: $d = 0.05 \enspace m$ , $N = 10$ , $\tau = 0.235 \enspace ns$。天线指向 $\beta = 25^{\circ}$。载波频率 $f = 30Ghz$
</center>

如图，$ h(f,\phi)$ 在 $d > c/f$ 时会出现栅瓣，但栅瓣出现的角度位于单一天线单元的响应之外，可以忽略。如果想抑制栅瓣，可以考虑减小天线单元的相位间距。

### 系统性能分析

如上所述，$h(f,\phi)$ 的特性决定了频率扫描模式的系统特征。给定其他参数，可以得到 $h(f,\phi)$ 的主瓣俯仰角与频率的关系。当分子，分母都等于 $0$ 时， $h(f,\phi)$ 到达最大值。所以有

$$ f \cdot (\tau - \frac{d \sin (\phi-\beta)}{c}) = k, \enspace k \in \mathbb{Z}$$

解得

$$ \phi = \arcsin(\frac{\tau \cdot c -kc/f}{d}) + \beta$$

为了在频率扫描过程中，扫描波束能够连续扫描整个成像带，需要使得 $k$ 在扫描过程中维持恒定。因此成像带应该保证

$$ \phi_{max} < \arcsin[\frac{\tau \cdot c -kc/(f_c+\frac{B}{2})}{d}] + \beta$$
$$ \phi_{min} > \arcsin[\frac{\tau \cdot c -kc/(f_c-\frac{B}{2})}{d}] + \beta$$

<center>  频率随俯仰角的变化

![alt text](/assets/Fscan/频率随俯仰角变化.png)

频率随俯仰角的变化。天线参数: $d = 0.05 \enspace m$ , $N = 10$ , $\tau = 0.235 \enspace ns$。天线指向 $\beta = 25^{\circ}$。载波频率 $f = 30Ghz$
</center>

如图，主瓣俯仰角与频率存在正相关关系。可以观察到频带分布在整个成像带中，也就是对于一个单一点目标，其处于相控阵所形成的窄波束中的时间远小于整个脉宽。因此点目标所能反射的信号带宽要小于整个信号带宽。定义点目标经过 $h(f,\phi)$ 主瓣最近的零点时，目标开始和完成扫描。

$$N \cdot f (\tau - \frac{d \sin (\phi-\beta)}{c}) = k \pm 1$$

可以得到点目标反射信号的带宽 $B_r$ 为

$$ B_r(\phi) = \frac{2}{N[\tau - d \sin(\phi-\beta)/c]} < B$$

点目标反射信号的带宽决定了SAR系统的距离向分辨率，其为

$$\rho_r(\phi) = \frac{c}{2B_r} = N (\tau \cdot c - d\sin(\phi-\beta))$$

可以发现，距离向分辨率与俯仰角存在负相关关系，这在条带模式，DBF-SCORE模式中是不存在的。但这也给设计人员一定便利，可以通过调整系统的 $\tau$ , $N$ , $d$ ，可以让不同俯仰角的目标分辨率不同。从而在更加关注的地方分辨率更高，不关注的地方分辨率大大降低，让整个系统更加灵活[2]。
而点目标反射信号脉宽 $T_p(\phi)$ 为

$$T_p([phi]) = \frac{2}{N K [\tau - d \sin(\phi-\beta)/c]}$$

对于SNR，SAR系统一般使用等效噪声系数（NESZ）进行评估。NESZ越低，SNR越好。其NESZ的表达式如下

$$NESZ = \frac{256 \pi^3 K_n T_n v L_n R(\phi)^3 B_r(\phi)^3 \sin(\eta)}{P \lambda^3 c f_p G^2 N^2 T_p(\phi)}$$

其中 $K_n$ 为玻尔兹曼常数， $T_n$ 等效系统噪声温度。 $L_n$ 为系统损耗如大气层影响等。$R(\phi)$ 为俯仰角为 $\phi$ 时，目标到雷达的距离，即斜距。$\eta$ 为入射角，当雷达正侧视时，入射角与俯仰角相等。当存在斜视时，入射角要大于俯仰角。$P$ 为每个天线单元的发射功率。$\lambda$ 为波长。 $f_p$ 为脉冲重复频率（PRF）。$G$ 为单一天线单元的增益，设 $A$ 为天线单元的有效面积，则 $G$ 满足

$$G = \frac{4\pi A}{\lambda^2} sinc[\frac{d \sin(\phi-\beta)}{\lambda}]^2$$

相较于 DBF-SCORE 只在接收端使用天线阵列，而条带模式完全不使用天线阵列，频率扫描模式在发射端，接收端均使用真时延相控阵，其NESZ要远小于DBF-SCORE模式与条带模式。

<center>  

![alt text](/assets/Fscan/fcan_dbf_strip_nesz.png)

F-SCAN，DBF-SCORE，条带模式的NESZ对比。参数: $d = 0.05 \enspace m$ , $N = 10$ , $\tau = 0.235 \enspace ns$。天线指向 $\beta = 25^{\circ}$。载波频率 $f = 30Ghz$, $T_n = 300K$, $L_n = 0.4$, 轨高 $H = 519 \enspace km$。$f_p = 1670 Hz$
</center>

天线的旁瓣会引起距离模糊。在模糊目标与主目标的斜距差 $\Delta R$ 满足

$$\Delta R = \frac{m \cdot c}{2 f_p} , \enspace m \in \mathbb{Z}$$

模糊目标所反射的信号会对前脉冲间隔或者后脉冲间隔的接收回波产生影响。对其的评估为 RASR。对于F-SCAN模式，RASR为

$$RASR = \frac{\sum_{m \in {\mathbb{Z}, m \neq 0}} h(f_0, \phi_m)^4 G^2/(R(\phi_m)^3 \sin(\eta_m))}{h(f_0, \phi_0)^4 G^2/(R(\phi_0)^3 \sin(\eta_0))}$$

其中 $f_0$ 为波束扫描到主目标时，信号频率。 $\phi_0$ 为主目标俯仰角。 $\phi_m$ 为模糊目标俯仰角， $\eta_m$ 为模糊目标的斜视角。
相较于DBF-SCORE的单发多收，条带模式单发单收，F-SCAN的多发多收特性使其RASR要好于另外两种模式。

<center>  

![alt text](/assets/Fscan/fscan_dbf_strip_rasr.png)


F-SCAN，DBF-SCORE，条带模式的RASR对比。参数同NESZ
</center>




## 载频波长影响


### 载频波长对频带利用率的影响


### 载波波长对接收窗的影响

### RASR 变化

### NESZ 变化

## 参数仿真

## Ka 波段超大带宽实现与设计



## 参考文献
>[1] L. Nan, G. Gai, T. Shiyang and Z. Linrang, "Signal Modeling and Analysis for Elevation Frequency Scanning HRWS SAR," in IEEE Transactions on Geoscience and Remote Sensing, vol. 58, no. 9, pp. 6434-6450, Sept. 2020
>
>B. Li, D. Liang, Y. Nan, J. Li, P. Lu and R. Wang, "A Novel Nonlinear Frequency Scanning SAR Imaging Mode," in IEEE Transactions on Geoscience and Remote Sensing, vol. 62, pp. 1-24, 2024