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

卫星与地面的几何系统如上图所示。 $\beta$ 为天线法线与卫星和地心连线的角, $\phi$ 为目标俯仰角。F-SCAN 天线结构类似于DBF-SCORE的接收端，在发送与接收两端均使用真时延相控阵雷达（True-Time-Delay Phased-Array。天线的每个通道间存在一个固定时延 $\tau$ 。设第一个通道为参考通道，则第 $n$ 个通道的时延为 $n \cdot \tau$ 。

<center>  天线单元构成 

![alt text](/assets/Fscan/发射单元构成.png)
</center>


每个单元均发送类似信号。假设参考通道发送线性调频信号。设 $f_c$ 为载频频率， $B$ 为信号带宽，而 $K = B/T_p$ 为信号的调频率, $c$ 为光速。则发送信号为

$$s_{ref}(t) = rect[t/T_p] \cdot e^{j(2 \pi f_c t + \pi K(t-T_p/2)^2)}$$   

设每个天线单元的相位中心间距为 $\phi$ , 则第 $n$ 个 通道的发送信号为

$$s_n(t) = s_{ref}(t) \otimes \delta(t-n\tau+\frac{d \sin(\phi-\beta)}{c})$$

$\otimes$ 表示对两个信号进行卷积运算。对于地面上的某个点目标，其接收到的信号为所有通道延时后的发送信号之和。设雷达到目标的距离为 $R$ ，可以得到

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

需要注意的是，虽然 $h(f, \phi)$ 与时间无关，但如果使用频率与时间相关的信号，如线性调频信号等，则可以在不同时间得到不同的 $f$ 。 $f$ 的变化使得 $h(f, \phi)$ 的主瓣对准不同的 $\phi$ ，从而完成对成像区域的扫描。 $ h(f,\phi)^2$ 归一化响应如下图所示


<center>  天线方向图

![alt text](/assets/Fscan/ant_direction.png)

其中黑线为矩形天线单元的天线方向图，蓝线为 $ h(f,\phi)^2$ 的响应。天线参数: $d = 0.05 \enspace m$ , $N = 10$ , $\tau = 0.235 \enspace ns$ 。天线指向 $\beta = 25^{\circ}$ 。载波频率 $f = 30Ghz$
</center>

如图,  $h(f,\phi)$ 在 $d > c/f$ 时会出现栅瓣，但栅瓣出现的角度位于单一天线单元的响应之外，可以忽略。如果想抑制栅瓣，可以考虑减小天线单元的相位间距。

### 系统性能分析

如上所述, $h(f,\phi)$ 的特性决定了频率扫描模式的系统特征。给定其他参数，可以得到 $h(f,\phi)$ 的主瓣俯仰角与频率的关系。当分子，分母都等于 $0$ 时， $h(f,\phi)$ 到达最大值。所以有

$$ f \cdot (\tau - \frac{d \sin (\phi-\beta)}{c}) = k, \enspace k \in \mathbb{Z}$$

解得

$$ \phi = \arcsin(\frac{\tau \cdot c -kc/f}{d}) + \beta$$

为了在频率扫描过程中，始终为单波束连续扫描，需要使得 $k$ 在扫描过程中维持恒定。因此成像带应该保证

$$ \phi_{max} < \arcsin[\frac{\tau \cdot c -kc/(f_c+\frac{B}{2})}{d}] + \beta$$
$$ \phi_{min} > \arcsin[\frac{\tau \cdot c -kc/(f_c-\frac{B}{2})}{d}] + \beta$$

<center>  频率随俯仰角的变化

![alt text](/assets/Fscan/频率随俯仰角变化.png)

频率随俯仰角的变化。天线参数: $d = 0.05 \enspace m$ , $N = 10$ , $\tau = 0.235 \enspace ns$ 。天线指向 $\beta = 25^{\circ}$ 。载波频率 $f = 30Ghz$
</center>

如图，主瓣俯仰角与频率存在正相关关系。可以观察到频带分布在整个成像带中，也就是对于一个单一点目标，其处于相控阵所形成的窄波束中的时间远小于整个脉宽。因此点目标所能反射的信号带宽要小于整个信号带宽。定义点目标经过 $h(f,\phi)$ 主瓣最近的零点时，目标开始和完成扫描。

$$N \cdot f (\tau - \frac{d \sin (\phi-\beta)}{c}) = N \cdot k \pm 1$$

可以得到点目标反射信号的带宽 $B_r$ 为

$$ B_r(\phi) = \frac{2}{N[\tau - d \sin(\phi-\beta)/c]} < B$$

点目标反射信号的带宽决定了SAR系统的距离向分辨率，其为

$$\rho_r(\phi) = \frac{c}{2B_r} = N (\tau \cdot c - d\sin(\phi-\beta))$$

不合适的参数选择会存在 $\phi$ 使得  $\rho_r(\phi) \leq 0$ , 此时相控阵无法形成窄波束，应该避免该情况。


<center>  

![alt text](/assets/Fscan/fscan_dbf_strip_resolution.png)

F-SCAN，DBF-SCORE，条带模式的距离向分辨率对比。其中DBF-SCORE模式与条带模式的距离向分辨率相同。参数: $d = 0.05 \enspace m$ , $N = 10$ , $\tau = 0.235 \enspace ns$ 。天线指向 $\beta = 25^{\circ}$ 。载波频率 $f = 30Ghz$ , $T_n = 300K$ , $L_n = 0.4$ , 轨高 $H = 519 \enspace km$ 。$f_p = 1670 Hz$
</center>

可以发现，距离向分辨率与俯仰角存在负相关关系，这在条带模式，DBF-SCORE模式中是不存在的。但这也给设计人员一定便利，通过调整系统的 $\tau$ , $N$ , $d$ ，可以让不同俯仰角的目标分辨率不同。从而使得更加关注的成像区域分辨率更高，不关注的成像区域分辨率大大降低，让整个系统更加灵活[2]。同时需要关注的是，相比于DBF-SCORE，条带模式，F-SCAN模式的距离向分辨率不再由信号带宽决定，而是由天线的设计参数决定。因此在F-SCAN的设计过程中，距离向分辨率会受到距离模糊，信噪比等要求的约束。而点目标反射信号脉宽 $T_p(\phi)$ 为

$$T_p(\phi) = \frac{2}{N K [\tau - d \sin(\phi-\beta)/c]}$$

对于SNR，SAR系统一般使用等效噪声系数（NESZ）进行评估。NESZ越低，SNR越好。其NESZ的表达式如下

$$NESZ = \frac{256 \pi^3 K_n T_n v L_n R(\phi)^3 B_r(\phi)^3 \sin(\eta)}{P \lambda^3 c f_p G^2 N^2 T_p(\phi)}$$

其中 $K_n$ 为玻尔兹曼常数， $T_n$ 等效系统噪声温度。 $L_n$ 为系统损耗如大气层影响等。 $R(\phi)$ 为俯仰角为 $\phi$ 时，目标到雷达的距离，即斜距。 $\eta$ 为入射角，当雷达正侧视时，入射角与俯仰角相等。当存在斜视时，入射角要大于俯仰角。$P$ 为每个天线单元的发射功率。 $\lambda$ 为波长。 $f_p$ 为脉冲重复频率（PRF）。 $G$ 为单一天线单元的增益，设 $A$ 为天线单元的有效面积，则 $G$ 满足

$$G = \frac{4\pi A}{\lambda^2} sinc[\frac{d \sin(\phi-\beta)}{\lambda}]^2$$

相较于 DBF-SCORE 只在接收端使用天线阵列，而条带模式完全不使用天线阵列，频率扫描模式在发射端，接收端均使用真时延相控阵，其NESZ要远小于DBF-SCORE模式与条带模式。

<center>  

![alt text](/assets/Fscan/fcan_dbf_strip_nesz.png)

F-SCAN，DBF-SCORE，条带模式的NESZ对比。参数同距离向分辨率，各个模式满足总发射功率相同，即 $P\cdot N$ 相同
</center>

天线的旁瓣会引起距离模糊。在模糊目标与主目标的斜距差 $\Delta R$ 满足

$$\Delta R = \frac{m \cdot c}{2 f_p} , \enspace m \in \mathbb{Z}$$

模糊目标所反射的信号会对前脉冲间隔或者后脉冲间隔的接收回波产生影响。对其的评估为 RASR。在F-SCAN模式中，模糊目标与主目标被同时照射，此时信号频率满足使 $h(f,\phi)$ 的主瓣指向主目标。因此RASR为

$$RASR = \frac{\sum_{m \in {\mathbb{Z}, m \neq 0}} h(f_0, \phi_m)^4 G^2/(R(\phi_m)^3 \sin(\eta_m))}{h(f_0, \phi_0)^4 G^2/(R(\phi_0)^3 \sin(\eta_0))}$$

其中 $f_0$ 为波束指向主目标时的信号频率。 $\phi_0$ 为主目标俯仰角， $\eta_0$ 为主目标入射角。 $\phi_m$ 为模糊目标俯仰角， $\eta_m$ 为模糊目标的斜视角。
相较于DBF-SCORE的单发多收，条带模式单发单收，F-SCAN的多发多收（MIMO）特性使其RASR要好于另外两种模式。

<center>  

![alt text](/assets/Fscan/fscan_dbf_strip_rasr.png)


F-SCAN，DBF-SCORE，条带模式的RASR对比。参数同NESZ
</center>




## 载频波长影响

### 距离向分辨率
由于天线参数受到距离向分辨率的约束，因此 $h(f,\phi)$ 的极值点方程可以改写为

$$\frac{f \rho_r(\phi)}{cN} = k $$

显然等式左边所有参数均大于0，而 $k \in \mathbb{Z}$ ， 因此 $k \geq 1$ 。由于 $\rho_r(\phi) < \rho_{max}$ ， 所以可以得到 $k$ 的取值范围 。

$$1 \leq k < \frac{f \rho_{max}}{c N}$$

因此，假如信号带宽 $B << f_c$ 有

$$\rho_{max} > \frac{cN}{f} \approx N \cdot \lambda$$

到这步，可以得到一个推论：F-SCAN系统的分辨率约束会限制载频的选取。也可以认为载频大小决定了系统可达的最小分辨率。比如对于X波段F-SCAN雷达，一般所选载频为 $9.8 GHz$ ，其所能达到的最小分辨率为 
$$\rho_{X} > 0.0306 N \enspace m $$

受到天线波束宽度的约束，随着幅宽的增大， 天线单元的相位中心间距 $d$ 需要降低到一个合适的量级，以保证天线单元的宽波束能够覆盖整个成像区域。而为了保证距离模糊比与信噪比满足系统要求，往往需要增大通道数量，对于宽幅成像，如果 $N$ 取 30，则X波段的分辨率受到载频的约束, 需满足 $\rho_X > 0.9 m$ 。因此如果想要优化F-SCAN系统分辨率参数，要么减少天线通道数，这会使距离模糊比与信噪比恶化；要么提高载频，比如上升到Ka波段，取载频为 $35 GHz$ , 则最小分辨率为

$$\rho_{Ka} > 0.0086 N \enspace m$$


### 信号带宽
由 $h(f,\phi)$ 的解析式可以看出，载波波长会显著影响 $h(f,\phi)$ 的特性，进而影响F-SCAN模式的系统性能，而不再如DBF-SCORE，条带模式一样，载波波长仅影响信号在外部环境中的传播效率。同样考虑 $h(f,\phi)$ 的位置。固定天线参数，成像区域。为让系统始终使用单波束连续扫描整个成像带，需要让 $k$ 在恒定，由此可以得到成像区域所需的信号带宽为

$$ B = k/ (\tau - \frac{d \sin (\phi_{max}-\beta)}{c}) - k/ (\tau - \frac{d \sin (\phi_{min}-\beta)}{c})$$

可以看出，成像区域所需的信号带宽与 $k$ 成正比，而 $k$ 与载波频率成正相关关系。因此随着载波频率的提高，照射相同的成像区域需要的带宽也就越大。其中分辨率会对天线参数进行约束，因此信号带宽可改写为

$$B = \frac{k \cdot cN}{\rho_r(\phi_{max})} - \frac{k \cdot cN}{\rho_r(\phi_{min})}$$  

<center>  

![alt text](/assets/Fscan/fscan_carrier.png)


F-SCAN模式下，相同成像区域与天线参数下不同载波频率所需的最小信号带宽，参数: $d = 0.05 \enspace m$ , $N = 10$ , $\tau = 0.235 \enspace ns$ 。 天线指向 $\beta = 25^{\circ}$ 。 成像区域为 $20^{\circ} \sim 30^{\circ}$
</center>

如图所示，在波束连续扫描这个条件下，固定分辨率，则成像区域所需带宽随着载波频率的提高，呈阶梯状上升，这大大抵消了随波段上升可用带宽增加的优势。同时随着波段的提高，可选载波频率也会减少，设计约束愈发严苛。假如设计要求仅约束了距离向分辨率的上限，则信号带宽的大小可由以下优化模型得出。

$$\min_{B \in \mathbb{R}} B$$
$$ s.t. \left\{
\begin{matrix}
\rho_r(\phi) < \rho_{max} \\ \\
\arcsin(\frac{\tau \cdot c -kc/(f_c-B/2)}{d}) + \beta < \phi_{min} \\ \\
\arcsin(\frac{\tau \cdot c -kc/(f_c+B/2)}{d}) + \beta > \phi_{max} \\ \\
0.886 \lambda / d > \phi_{max} - \phi_{min} \\ \\
k = round(f_c \cdot  (\tau - \frac{d \sin (\phi_{mid}-\beta)}{c}))
\end{matrix}
\right.
$$

该优化模型比较复杂，可以考虑先通过成像区域对天线相位间距进行约束，在通过RASR和NESZ的要求对天线通道数进行约束，减少优化变量, 然后通过优化真时延 $\tau$ 来获取最小信号带宽，优化结果如下

<center>  

![alt text](/assets/Fscan/fscan_carrier_bw.png)


F-SCAN模式下，相同成像区域下不同载波频率所需的最小信号带宽。天线部分参数: $d = 0.05m$ , $N = 10$ 。 天线指向 $\beta = 25^{\circ}$ 。成像区域为 $20^{\circ} \sim 30^{\circ}$ , $\rho_{max} = 0.2m$
</center>

可以看出, 简单的提高波段以获取更大的频带并不能有效改善SAR系统的成像宽度. 在分辨率与成像区域的约束下,波段越高,频带利用效率越低. 虽然如此, 由于不同波段的可用带宽相差巨大, 所以提高波段依旧可以改善F-SCAN模式的成像宽度。




### RASR 与 NESZ
由上所述，F-SCAN 模式之所以能够获取到更低的RASR与NESZ，主要源于其MIMO特性。而在MIMO中，对信噪比影响最大的便是收发的通道数 $N$ 。随着 N 的提高，理论上RASR与NESZ会显著降低。以最小化RASR为目标，优化    $\tau$ , 同时保证信号带宽 $B$ 不超过最大信号带宽 $B_{max}$ ， 距离向分辨率 $\rho_r(\phi)$ 不超过 $\rho_{max}$ , 可以得到最优RASR

<center>  

![alt text](/assets/Fscan/fscan_ant_rasr.png)
![alt text](/assets/Fscan/fscan_ant_nesz.png)


F-SCAN模式下，相同成像区域下最大RASR,最大NESZ 相较于通道数的变化。天线部分参数: $d = 0.01m$ 。 天线指向 $\beta = 25^{\circ}$ 。成像区域为 $20^{\circ} \sim 30^{\circ}$ , $\rho_{max} = 0.2m$  , 最大可用信号带宽 $B_{max} = 2GHz$
</center>

可以观察到，随着 $N$ 的增加, RASR 确实有明显的降低。其中Ka波段的RASR，NESZ相较于X波段会有所改善。但是如果固定天线相位间距 $d$ ，随着 $N$ 的提高，系统便裕量愈发紧张，甚至最后会出现无解的情况，该情况在Ka波段更加普遍。当出现无解情况时，需要降低天线相位间距，而天线相位间距的降低会引起天线增益下降，单一天线单元的主瓣展开，导致RASR和NESZ的提高，系统性能恶化。

<center>  

![alt text](/assets/Fscan/fscan_ant_drasr.png)
![alt text](/assets/Fscan/fscan_ant_dnesz.png)


F-SCAN模式下，相同成像区域下最大RASR,最大NESZ 相较于通道数的变化。载波频率 $f_c = 34GHz$ 。 天线指向 $\beta = 25^{\circ}$ 。成像区域为 $20^{\circ} \sim 30^{\circ}$ , $\rho_{max} = 0.2m$  , 最大可用信号带宽 $B_{max} = 8GHz$
</center>

因此，在相同的成像幅度下， $N \cdot d$ 大小的上限受到限制，约束了当前波束扫描F-SCAN系统的距离模糊比与信噪比的性能上限。


## 分段扫描F-SCAN SAR系统
### 系统描述
再次分析 $h(f,\phi)$ 极值点的位置。假如分辨率约束较松，通道数较小，而载频较高，则 $k \geq 2$ 便是有可能的。而如果成像区域极宽，则

$$\frac{(f+B/2)\rho_r(\phi_{max})}{cN} - \frac{(f-B/2)\rho_r(\phi_{min})}{cN} > 1$$

此时在波束扫描过程中，$h(f, \phi)$ 的远端对应的极点位置 $f_{far}$ 与近端的极点位置 $f_{near}$ 不在相同，反而满足 $f_{far} < f_{near}$。此时导致远端所需的频率最低，近端频率较高，中心频率最高。此时F-SCAN 进入分段扫描模式。通过调节信号发射时间与频率的关系，我们可以获得更小的接收窗，特别是在PRF较高，成像区域较宽，导致的发射脉宽较小的情况[3]。

<center>  

![alt text](/assets/Fscan/doa_t_f.png)


分段F-SCAN模式下，俯仰角与接收时间，频率的对应关系。
</center>

如图所示，相比于 $k$ 恒定的单段F-SCAN使用调频率 $K < 0$ 的信号，为了压缩时间窗，防止远端接收窗与近端接收窗出现分隔，分段F-SCAN需要使用调频率 $K > 0$ 的信号。此时系统首先扫描远端区域，在远端目标区域完成扫描后，如果系统会继续扫描天线单元宽波束覆盖区域以外的区域，这可能会稍微恶化距离模糊比。之后宽波束中的 $h(f,\phi)$ 的极点发生跳变，$h(f,\phi)$ 切换所选主瓣，回到近端，并开始扫描，直到极点再次发生跳变。

### 系统设计方案
该扫描情况只在相控阵会出现栅瓣时发生，通过天线单元宽波束对扫描窄波束的栅瓣的选取，实现扫描区域的跳变与分段扫描。所以需要精心设计相控阵天线的各个参数

## 系统仿真

### 参数设计

### 仿真结果



## 参考文献
>[1] L. Nan, G. Gai, T. Shiyang and Z. Linrang, "Signal Modeling and Analysis for Elevation Frequency Scanning HRWS SAR," in IEEE Transactions on Geoscience and Remote Sensing, vol. 58, no. 9, pp. 6434-6450, Sept. 2020
>
>B. Li, D. Liang, Y. Nan, J. Li, P. Lu and R. Wang, "A Novel Nonlinear Frequency Scanning SAR Imaging Mode," in IEEE Transactions on Geoscience and Remote Sensing, vol. 62, pp. 1-24, 2024
>
>M. Younis, F. Q. de Almeida, T. Bollian, M. Villano, G. Krieger and A. Moreira, "A Synthetic Aperture Radar Imaging Mode Utilizing Frequency Scan for Time-of-Echo Compression," in IEEE Transactions on Geoscience and Remote Sensing, vol. 60, pp. 1-17, 2022