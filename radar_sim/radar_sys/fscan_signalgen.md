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

其中 $\phi_{max}$ 为成像区域最远端的俯仰角，$\phi_{min}$ 为成像区域最近端的俯仰角。

<center>  频率随俯仰角的变化

![alt text](/assets/Fscan/频率随俯仰角变化.png)

频率随俯仰角的变化。天线参数: $d = 0.05 \enspace m$ , $N = 10$ , $\tau = 0.235 \enspace ns$ 。天线指向 $\beta = 25^{\circ}$ 。载波频率 $f = 30Ghz$
</center>

如图，主瓣俯仰角与频率存在正相关关系。可以观察到频带分布在整个成像带中，也就是对于一个单一点目标，其处于相控阵所形成的窄波束中的时间远小于整个脉宽。因此点目标所能反射的信号带宽要小于整个信号带宽。定义点目标经过 $h(f,\phi)$ 主瓣最近的零点时，目标开始和完成扫描。

$$N \cdot f (\tau - \frac{d \sin (\phi-\beta)}{c}) = N \cdot k \pm 1$$

可以得到点目标反射信号的带宽 $B_r$ 为

$$ B_r(\phi) = |\frac{2}{N[\tau - d \sin(\phi-\beta)/c]} | < B$$

其中加 $|\cdot|$ 符号是源于 $\tau - \frac{d \sin (\phi-\beta)}{c}$ 可能小于0。点目标反射信号的带宽决定了SAR系统的距离向分辨率，其为

$$\rho_r(\phi) = \frac{c}{2B_r} = \frac{1}{4}|N (\tau \cdot c - d\sin(\phi-\beta))| < \rho_{max}$$

其中 $\rho_{max}$ 为系统要求的分辨率约束。需要注意的是当 $\tau \cdot c - d\sin(\phi-\beta) = 0$ 时，$\rho_r(\phi) = 0$ 。这当然是不可能的，但仍然需要探求下此时发生了什么情况。设 $\phi = \phi_u$ 时，发生该情况。此时 $h(f,\phi)$ 的主瓣指向不再和频率 $f$ 存在关系，任意频率均有波束指向 $\phi_d$ 。设 $\phi_u$ 对应的斜距为 $R_u$ 。考虑到 F-SCAN系统通过调整频率来更改波束指向，对于斜距满足如下关系的点 

$$R_i = R_d \pm \frac{i\cdot c}{f_p}, \enspace i \in \mathbb{Z}, \enspace i \neq 0$$ 

其将被 $\phi_d$ 严重影响，导致其距离模糊比很大，远远高于系统要求。因此在设计过程中应该避免 $\tau \cdot c - d\sin(\phi-\beta) = 0$ 的情况出现。又因为发射信号为带限信号，其频率下限 $f_c - B/2$ 是远大于0 的 ，因此 $k \neq 0$ 。在分辨率表达式中，将 $k$ 带入，可以得到

$$ \rho_r(\phi) =| \frac{kcN}{4 f(\phi)} |$$

当信号带宽远小于载波频率 $f_c$ 时，可以得到

$$\rho_r(\phi) \approx |\frac{kcN}{4f_c}| < \rho_{max}$$
$$|k| = round(\frac{4\rho_r(\phi)f_c}{Nc})$$

这里 $round(\cdot)$ 表示取最近的整数。因此可以得到 $k$ 的大致取值范围

$$\min(\frac{4\rho_r(\phi)f_c}{Nc}) < |k| < \max(\frac{4\rho_r(\phi)f_c}{Nc}) , k \in \mathbb{Z}$$


<center>  

![alt text](/assets/Fscan/fscan_dbf_strip_resolution.png)

F-SCAN，DBF-SCORE，条带模式的距离向分辨率对比。其中DBF-SCORE模式与条带模式的距离向分辨率相同。参数: $d = 0.05 \enspace m$ , $N = 10$ , $\tau = 0.235 \enspace ns$ 。天线指向 $\beta = 25^{\circ}$ 。载波频率 $f = 30Ghz$ , $T_n = 300K$ , $L_n = 0.4$ , 轨高 $H = 519 \enspace km$ 。$f_p = 1670 Hz$
</center>

可以发现，距离向分辨率与俯仰角存在负相关关系，这在条带模式，DBF-SCORE模式中是不存在的。但这也给设计人员一定便利，通过调整系统的 $\tau$ , $N$ , $d$ ，可以让不同俯仰角的目标分辨率不同。从而使得更加关注的成像区域分辨率更高，不关注的成像区域分辨率大大降低，让整个系统更加灵活[2]。同时需要关注的是，相比于DBF-SCORE，条带模式，F-SCAN模式的距离向分辨率不再由信号带宽决定，而是由天线的设计参数决定。因此在F-SCAN的设计过程中，距离向分辨率会受到距离模糊，信噪比等要求的约束。而点目标反射信号脉宽 $T_p(\phi)$ 为

$$T_p(\phi) = \frac{2}{N K [\tau - d \sin(\phi-\beta)/c]}$$

对于SNR，SAR系统一般使用等效噪声系数（NESZ）进行评估。NESZ越低，SNR越好。其NESZ的表达式如下

$$NESZ = \frac{256 \pi^3 K_n T_n v L_n R(\phi)^3 B_r(\phi)^3 \sin(\eta)}{P \lambda^3 c f_p G^2 N^2 T_p(\phi)} $$ 
$$ \cdot \frac{\Theta_{az}}{\int_{\Theta_{az}}|P(\theta_{az})|^4 d\theta_{az}} \cdot \frac{\Phi_{el}}{\int_{\Phi_{el}} |P(\phi_{el})|^4d\phi_{el}}$$

其中 $K_n$ 为玻尔兹曼常数， $T_n$ 等效系统噪声温度。 $L_n$ 为系统损耗如大气层影响等。 $R(\phi)$ 为俯仰角为 $\phi$ 时，目标到雷达的距离，即斜距。 $\eta$ 为入射角，当雷达正侧视时，入射角与俯仰角相等。当存在斜视时，入射角要大于俯仰角。$P$ 为每个天线单元的发射功率。 $\lambda$ 为波长。 $f_p$ 为脉冲重复频率（PRF）。 $\Phi_{az}$ 是方位向波束宽度。$P(\theta_{az})$ 为方位向天线方向图。$\Phi_{el}$ 为距离向波束宽度。 $P(\phi_{el})$ 为距离向天线方向图。$G$ 为单一天线单元的增益，设 $A$ 为天线单元的有效面积，则 $G$ 满足

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
由上所述，距离向分辨率与天线参数有关，相应的距离向分辨率的约束会限制天线参数的选取，反而与发射信号带宽无关。而在DBF-SCORE，条带模式中，距离向分辨率只与发射信号带宽有关。同时随着俯仰角的变化，距离向分辨率也会变化。这些约束可表诉为 

$$ \frac{1}{4}|N(\tau \cdot c - d\sin{\phi})| \leq \rho_{max}$$

其中 $\phi$ 会随着扫描的进行发生变化，而对于F-SCAN 系统 ，波束对准 $\phi$ 时其频率是确定的，满足

$$\tau \cdot c - d\sin{\phi} = \frac{c \cdot k}{f}$$

当信号带宽 $B << f_c$ 时，有

$$\tau \cdot c - d\sin{\phi} \approx k \lambda$$

带入距离向分辨率的约束中，可以得到

$$\frac{1}{4} N |k| \lambda \leq \rho_{max}$$

其中由于 $k \neq 0 , \enspace k \in \mathbb{Z}$ ，所以 $|k| \geq 1$ 因此有

$$\frac{1}{4} N \lambda \leq \rho_{max}$$

到这步，可以得到一个推论：F-SCAN系统的分辨率约束会限制载频的选取。也可以认为载频大小决定了系统可达的最小分辨率。比如对于X波段F-SCAN雷达，最高载频为 $12 GHz$ ，其所能达到的最小分辨率为 
$$\rho_{X} > \frac{N}{160}  \enspace m $$

受到天线波束宽度的约束，随着幅宽的增大， 天线单元的相位中心间距 $d$ 需要降低到一个合适的量级，以保证天线单元的宽波束能够覆盖整个成像区域。而为了保证距离模糊比与信噪比满足系统要求，往往需要增大通道数量，对于宽幅成像，如果 $N$ 取 30，则X波段的分辨率受到载频的约束, 需满足 $\rho_X > 0.9 m$ 。因此如果想要优化F-SCAN系统分辨率参数，要么减少天线通道数，这会使距离模糊比与信噪比恶化；要么提高载频，比如上升到Ka波段，取载频为 $35 GHz$ , 则最小分辨率为
$$\rho_{Ka} > 0.00214 N \enspace m$$

其显著优于X波段


### 信号带宽
由 $h(f,\phi)$ 的解析式可以看出，载波波长会显著影响 $h(f,\phi)$ 的特性，进而影响F-SCAN模式的系统性能，而不再如DBF-SCORE，条带模式一样，载波波长仅影响信号在外部环境中的传播效率。同样考虑 $h(f,\phi)$ 的位置。固定天线参数，成像区域。为让系统始终使用单波束连续扫描整个成像带，需要让 $k$ 在恒定，由此可以得到成像区域所需的信号带宽为

$$ B = k/ (\tau - \frac{d \sin (\phi_{max}-\beta)}{c}) - k/ (\tau - \frac{d \sin (\phi_{min}-\beta)}{c})$$

可以看出，成像区域所需的信号带宽与 $k$ 成正比，而 $k$ 的上限在分辨率与天线通道数固定时与载波频率成正相关关系。因此随着载波频率的提高，照射相同的成像区域需要的带宽也就越大。其中分辨率会对天线参数进行约束，因此信号带宽可改写为

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
k > (f-\frac{B}{2}) \cdot  (\tau - \frac{d \sin (\phi-\beta)}{c}) \\ \\
k < (f+\frac{B}{2}) \cdot  (\tau - \frac{d \sin (\phi-\beta)}{c})
\end{matrix}
\right.
$$

该优化模型比较复杂，可以考虑先通过成像区域对天线相位间距进行约束，在通过RASR和NESZ的要求对天线通道数进行约束，减少优化变量, 然后通过优化真时延 $\tau$ 来获取最小信号带宽，优化结果如下

<center>  

![alt text](/assets/Fscan/fscan_carrier_bw.png)


F-SCAN模式下，相同成像区域下不同载波频率所需的最小信号带宽。天线部分参数: $d = 0.03m$ , $N = 10$ 。 天线指向 $\beta = 25^{\circ}$ 。成像区域为 $20^{\circ} \sim 30^{\circ}$ , $\rho_{max} = 0.2m$
</center>

可以看出, 简单的提高波段以获取更大的频带并不能有效改善SAR系统的成像宽度. 在分辨率与成像区域的约束下,波段越高,频带利用效率越低. 虽然如此, 由于不同波段的可用带宽相差巨大, 所以提高波段依旧可以改善F-SCAN模式的成像宽度。同时，由于约束距离向分辨率，天线参数一般是确定的，此时随着天线通道数的增加，信号带宽也会快速增大，但是在RASR，NESZ的约束下，$N$ 的最小值是受到限制的，因此该值应该仔细选取。同时为了更高效的利用信号带宽，往往需要在信号处于载频时，波束指向成像区域的中心，也就是满足

$$f_c \tau = k, \enspace k \in \mathbb{Z}$$

由之前的分析可知， $k$ 的大致取值范围可以通过距离向分辨率确定，因此可得

$$f_c |\tau| = round(\frac{4\rho_r(\phi_{mid})f_c}{Nc}) $$

其中 $\phi_{min}$ 为成像区域中心对应的视角。化简得到 $\tau$ 的大致取值为

$$|\tau| = round(\frac{4\rho_r(\phi_{mid})f_c}{Nc})/f_c \approx \frac{4\rho_r(\phi_{mid})}{Nc}$$

因此 $\tau$ 的限制会影响距离向分辨率与天线通道数的选择。其中 $\tau$ 的最小值或者说精度受到当前工艺的制约，其选择应该考虑当前工艺限制优先确定，之后确定天线通道数。 




### RASR 与 NESZ
由上所述，F-SCAN 模式之所以能够获取到更低的RASR与NESZ，主要源于其MIMO特性。而在MIMO中，对信噪比影响最大的便是收发的通道数 $N$ 。随着 N 的提高，理论上RASR与NESZ会显著降低。以最小化RASR为目标，优化    $\tau$ , 同时保证信号带宽 $B$ 不超过最大信号带宽 $B_{max}$ ， 距离向分辨率 $\rho_r(\phi)$ 不超过 $\rho_{max}$ , 可以得到最优RASR

<center>  

![alt text](/assets/Fscan/fscan_ant_rasr.png)
![alt text](/assets/Fscan/fscan_ant_nesz.png)


F-SCAN模式下，相同成像区域下最大RASR,最大NESZ 相较于通道数的变化。天线部分参数: $d = 0.01m$ 。 天线指向 $\beta = 25^{\circ}$ 。成像区域为 $20^{\circ} \sim 30^{\circ}$ , $\rho_{max} = 0.2m$  , 最大可用信号带宽 $B_{max} = 2GHz$
</center>

可以观察到，随着 $N$ 的增加, RASR 确实有明显的降低。其中Ka波段的RASR，NESZ相较于X波段会有所改善(why)。

<center>  

![alt text](/assets/Fscan/fscan_ant_drasr.png)
![alt text](/assets/Fscan/fscan_ant_dnesz.png)


F-SCAN模式下，相同成像区域下最大RASR,最大NESZ 相较于通道数的变化。载波频率 $f_c = 34GHz$ 。 天线指向 $\beta = 25^{\circ}$ 。成像区域为 $20^{\circ} \sim 30^{\circ}$ , $\rho_{max} = 0.2m$  , 最大可用信号带宽 $B_{max} = 2GHz$
</center>

因此，在相同的成像幅度下， $N \cdot d$ 大小的上限受到限制，约束了当前波束扫描F-SCAN系统的距离模糊比与信噪比的性能上限。同时对于天线子孔径的选择，也理应使得在成像区域里不能再栅瓣。考虑到F-SCAN的视角与频率存在关系，该限制分为两种情况：一是对于某个视角，在频带内只存在一个频率使得栅瓣指向该视角。即频带决定成像区域。二是对于某个频率，在成像区域内只存在一个栅瓣指向一个视角，这与传统的栅瓣干扰相一致， 会导致距离模糊比恶化，需要详细分析。 如图所示

<center>  

![alt text](/assets/Fscan/doa_t_f.png)


分段F-SCAN模式下，俯仰角与接收时间，频率的对应关系。参数: 载波频率 $ f=35GHz$ , 信号带宽 $B= 1000MHz$
天线单元相位间距 $d = 0.098m$ ， 天线单元时延 $\tau = -2.651ns$，天线通道数 $N = 10$
</center>

可以发现，对于成像区域的近端，由于带宽的限制，只有单独一个栅瓣进行扫描。而对于远端，便存在两个栅瓣在不同的频段进行扫描。此时对于接收机，由于发射信号存在脉宽 $T_p$，如果使用线性调频信号，则在接收过程中，某个视角存在两个接收时间，以对应不同栅瓣使用不同频率扫描该视角。这会导致某一视角干扰其他视角的接收，即同一时间接收到两个视角反射信号的叠加，恶化了其中一个视角的距离模糊比。设低频扫描栅瓣序号为 $k_1$, 高频  栅瓣扫描情况为 $k_2$ , 满足 $k_1 = k_2+1$ 。则 $k_1$ 栅瓣由于受到最大可能频率 $f_c+B/2$ 的限制，最远只能扫描到 $\phi_{1,max}$ 。同理 $k_2$ 栅瓣由于受到最小可能频率 $f-B/2$ 的限制，最近只能从 $\phi_{2,min}$ 开始扫描。如果 $\phi_{2,min} < \phi_{max}$ ，便会导致整个成像区域由多个栅瓣共同扫描完成。为使得讨论比较方便，这里假设 $k_1,\enspace k_2$ 均大于0，$k_1,\enspace k_2$ 的符号不影响讨论的最后结果。由之前所诉，$h(f,\phi)$ 的两个栅瓣的扫描区域满足以下条件

$$ \arcsin[\frac{\tau \cdot c -k_1 c/(f_c+\frac{B}{2})}{d}] + \beta = \phi_{1,max}$$
$$ \arcsin[\frac{\tau \cdot c -k_2 c/(f_c-\frac{B}{2})}{d}] + \beta = \phi_{2,min}$$

设目标成像区域为 $\phi_{min} \leq \phi \leq \phi_{max}$ 。因此，可以描述为

$$\phi_{min} < \phi_{1,max} < \phi_{max}$$
$$\phi_{max} > \phi_{2,min} > \phi_{min}$$


此时，在 $\phi_{2, min} $ ， $\phi_{1,max}$ 之间的区域可能会被 $k_1$, $k_2$ 栅瓣以不同频率 $f_1$, $f_2$ 扫描到。$f_1, f_2$ 间满足

$$\arcsin[\frac{\tau \cdot c -k_1 c/(f_1)}{d}] = \arcsin[\frac{\tau \cdot c -k_2 c/(f_2)}{d}]$$

解得 $f_1$ ，$f_2$ 满足

$$\frac{f_1}{k_1} = \frac{f_2}{k_2}$$

在信号带宽 $B <<f_c$ 时，二者之差满足

$$f_1-f_2 = \frac{k_1-k_2}{k_1} f_1 \approx \frac{1}{k_1} f_c < B$$

此时，由于频率与时间的关系，在接收端该频率差会导致接收时间差，使得不同俯仰角的反射信号叠加在一起， 恶化距离模糊比。因此需要抑制该情况的发生，抑制方法可以从信号频段与天线参数两种方式下手。信号频段方面，可以通过优化载频位置，使得指向成像区域中心位置时的频率为载频，然后通过削减信号带宽的方式抑制栅瓣,或者调节天线参数，最终使得各个栅瓣位置满足

$$\phi_{0, max} <  \phi_{min}$$
$$\phi_{1,min} < \phi_{min}$$
$$\phi_{1,max} > \phi_{max}$$
$$\phi_{2, min} > \phi_{max}$$

其中 $\phi_{0, max}$ 为 $k_0$ 栅瓣的最大扫描位置，满足 $k_0 = k_1+1$ 。带入其他公式，解得 

$$d < \frac{\lambda}{\sin(\phi_{max}-\beta) - \sin(\phi_{min} - \beta)}$$

在天线子孔径满足上述条件后，便存在时延 $\tau$ 使得该模糊情况不在存在。如果 $\tau,d$ 已经确认好，则信号带宽 $B$ 应小于 $f_c/k$ ，以使得成像区域只有一个栅瓣在扫描。


## Ka 波段 F-SCAN SAR系统的设计

### 设计要求
在本节，本文将通过一个简单的Ka波段宽幅成像的星载F-SCAN SAR系统的设计样例，来汇总说明Ka波段F-SCAN系统的设计方案，分析说明各个参数间的相互约束。设计要求如下表

<center>

|指标|要求|
|:----:|:---:|
|轨道高度| 519 km|
|载波频率| 35 GHz|
|距离向分辨率| <2m |
|方位向分辨率| <5m|
|地距宽度| $\geq$ 50 km|
|距离模糊比| <-24 dB|
|方位模糊比| <-24 dB|
|等效噪声系数| <-22dB| 
|脉冲宽度| 40 us|

</center>

### 设计流程
在设计过程中，首先需要分辨率达标。从方位向分辨率可以得出距离多普勒的带宽，其中卫星速度由轨道高度决定。
而脉冲发射频率需要大于多普勒带宽，否则应使用方位向多孔径技术降低PRF要求。

$$PRF\cdot N_{az} > \frac{0.886 v_r}{\rho_{az}} = 1350 Hz$$

其中 $N_{az}$ 为方位向孔径数，$v_r$ 为卫星等效速度，由轨道高度可得出 $vr = 7.621$ km/s，$\rho_{az}$ 为方位向分辨率，在本例中为5 m。 同时SAR系统的时序与成像宽度也会约束脉冲发射频率。SAR系统的时序约束取决于成像几何与发射信号占空比，而当成像区域确定后，接收回波的时间窗宽度是确定，因此发射信号的占空比取决于脉冲发射频率。为了使接收回波不受到星下点回波与发射脉冲的干扰，在满足多普勒带宽的要求下，应该选取合适的脉冲发射频率。

<center>  

![alt text](/assets/Fscan/zebra_diagram.png)


时序约束图。
</center>

如图为本系统的时序约束图。蓝色区域为发射干扰，红色区域为星下点回波干扰，绿线为系统工作区间，可以看出系统的接收回波避免了相关干扰。其中视角由成像宽度与视角中心决定，如图所示，在 $<30^{\circ}$ 时时序较为宽松，由此，通过地距宽度可得视角范围为 $[22.79^{\circ}, \enspace 27.21^{\circ}]$ 。其中PRF为1720 Hz ，方位向孔径数 $N_{az} = 1$ ，所以 $PRF \cdot N_{az} = 1720$ Hz， 大于多普勒带宽。SAR系统的方位向特征决定了，天线子孔径长度大约为分辨率的两倍，可得方位向子孔径长度为 $d_{az} = 10m$ ，因此方位向天线长度 $L_{az} = d_{az} \cdot N_{az} = 10m$ 。

对于距离向，首先需要保证在成像区域不会形成栅瓣，因此可由波位图给出的扫描视角宽度得到距离向天线子孔径宽度 $d_{el}$ 的上限，其中扫描视角宽度 $\Delta \phi_{el} = 4.4^{\circ}$

$$d_{el} < \frac{\lambda}{\sin(\Delta \phi_{el}/2) - \sin(-\Delta \phi_{el}/2)} = 0.111$$

满足区域覆盖要求后，应尽可能增大天线子孔径从而在通道数相同时获得更大的天线增益，保证等效噪声系数NESZ足够小。因此这里取天线子孔径宽度 $d_{el} = 0.09$ m 由前几节分析可知，距离向分辨率会约束天线参数的选取，同时俯仰向通道数 $N_{el}$ 也会受到载波波长的约束，满足

$$N_{el} < \frac{4\rho_r f_c}{c} = 934$$

由于距离向分辨率较大，载波频率较高，$N_{el}$ 的上限较为宽松，而其下限由RASR决定。对 $N_{el}$ 的选择可以通过遍历所有可能的 $N_{el}$ 来选取可用的通道数，从而得到较为合适的值。相较于条带模式，信号带宽可由分辨率直接确定，F-SCAN模式的信号带宽由多个因素共同确定，想直接得到比较困难。因此在设计往往需要给出最大可用信号带宽，或者给出带宽利用效率，即条带模式达到所需分辨率的信号带宽占F-SCAN模式所有信号带宽的比例。本例中，分辨率为2m，对应的条带模式信号带宽为76 MHz左右。对于Ka波段的F-SCAN系统，由于其所使用的栅瓣序号较高，即 $k$ 较大，因此带宽利用效率较低，这里设带宽利用效率为 $15 \%$ 。因此最大可用信号带宽为 $506$MHz，即500 MHz左右。由此扫描可用的天线通道数 $N_{el}$ 。如图所示


<center>  

![alt text](/assets/Fscan/design_ant_drasr.png)
在最大信号带宽 $B = 500$ MHz，扫描可用的 $N_{el}$ 与 $\tau$

</center>

可以观察到，$N_{el}$ 的取值范围为 $6 \leq N_{el} \leq 13$ 。本例中选取 $N_{el}$ 为 10，对应的 $\tau = -2.629$ ns。此时可逐步缩小信号带宽，从而得到较小的，合适的值，这里选取信号带宽 $B = 400 $ MHz 。为了使NESZ达到要求，发射总功率为 1000 W，即每个天线单元为 100W 。其中各性能参数为

<center>  

![alt text](/assets/Fscan/design_resolution.png)
![alt text](/assets/Fscan/design_nesz.png)
![alt text](/assets/Fscan/design_rasr.png)


</center>

系统参数汇总如下表

<center>  

|参数|符号|数值|
|:----:|:---:|:---:|
|信号带宽| $B$ | 400 MHz|
|载波频率| $f_c$ |35 GHz|
|天线距离向子孔径长度| $d_{el}$ | 0.9m|
|天线距离向子孔径数量| $N$ |10|
|天线距离向孔径间时延| $\tau$ | -2.629 ns|
|天线方位向孔径长度| $d_{az}$ | 10m|
|天线峰值功率|P| 10 kW|
|脉冲宽度|$T_p$ |40 us|

</center>


### 参数仿真验证
<center>  

![alt text](/assets/Fscan/simulation_graph.drawio.png)

仿真场景，绘于地距平面

</center>

如图所示为仿真场景，其绘制于地距平面。视角P1 为 $23.17^{\circ}$ , P2，P3,P4为 $25^{\circ}$ , P5为 $26.66^{\circ}$ 。下图为回波的二维频率图，可以看出相较于条带，DBF-SCORE模式不同斜距目标的频域相互重叠在一起，共用一个频段，F-SCAN模式下，不同斜距的目标只占用信号带宽中的一小部分，并分布在不同的频域范围内。

<center>  

![alt text](/assets/Fscan/sim_echo_fft.png)

点目标成像结果

</center>

下图为成像结果以及距离向，方位向剖面。其中最左端的(b) (g) (l) 对应P5，(c) (h) (m)对应P3, (d) (i) (n) 对应P2, (e) (j) (o) 对应P4, (f) (k) (p) 对应 P1 。其中距离向有着较大的展宽， 极低的旁瓣，其源于天线结构的频率响应对目标的调制，因此在成像时无需再对距离向加窗。

### 参数仿真
<center>  

![alt text](/assets/Fscan/dot_estimate.png)

点目标成像结果

</center>

成像指标如下表所示。

<center>  

|指标|P1|P2|P3|P4|P5|
|:----:|:---:|:---:|:---:|:---:|:---:|
|距离向IRW| 2.36 m|2.28 m|2.28 m|2.28 m|2.36 m |
|距离向PSLR| -36.18 dB|-40.13 dB|-40.13 dB|-40.13 dB|-35.99 dB |
|距离向ISLR| -40.99 dB|-45.90 dB|-45.90 dB|-45.90 dB|-40.78 dB |
|方位向IRW| 5.26 m |5.26 m|5.26 m|5.26 m|4.98 m |
|方位向PSLR| -10.72 dB|-12.48 dB|-12.48 dB|-12.52 dB|-12.32 dB | 
|方位向ISLR| -8.35 dB|-9.78 dB|-9.86 dB|-9.90 dB|-9.63 dB | 


</center>




## 参考文献
>[1] L. Nan, G. Gai, T. Shiyang and Z. Linrang, "Signal Modeling and Analysis for Elevation Frequency Scanning HRWS SAR," in IEEE Transactions on Geoscience and Remote Sensing, vol. 58, no. 9, pp. 6434-6450, Sept. 2020
>
>B. Li, D. Liang, Y. Nan, J. Li, P. Lu and R. Wang, "A Novel Nonlinear Frequency Scanning SAR Imaging Mode," in IEEE Transactions on Geoscience and Remote Sensing, vol. 62, pp. 1-24, 2024
>
>M. Younis, F. Q. de Almeida, T. Bollian, M. Villano, G. Krieger and A. Moreira, "A Synthetic Aperture Radar Imaging Mode Utilizing Frequency Scan for Time-of-Echo Compression," in IEEE Transactions on Geoscience and Remote Sensing, vol. 60, pp. 1-17, 2022