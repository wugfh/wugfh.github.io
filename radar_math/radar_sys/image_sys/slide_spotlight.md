---
layout: default
title: sliding-spotlight mode
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

## 成像几何系统
相较于条带成像，滑动聚束模式关键在于天线波束以一个固定的角速度 $\omega$ 旋转。所以其斜视角 $\theta$ 会以一个固定速度变化。设初始斜视角为 $\theta_0$ ，斜视角满足

$$\theta = \theta_0 + \omega \eta$$

$\eta$ 为方位向时间，即慢时间。通过粗略的想象，我们可以大概认知到如果波束旋转引起地面波束移动与卫星或者飞机的移动相反，使得地面波束移动的速度更慢，那么可以让目标获得更长的照射时间，从而得到更高的方位向分辨率。设在波束旋转下，地面波束的移动速度为 $v_f$ ，雷达移动速度为 $v_s$ ，相应无旋转时（条带模式）的波束移动速度为 $v_g$ ， 波束张角为 $\theta_a$。对于某个点目标 $P$，刚开始进入覆盖区时，波束中心斜视角为 $\theta_0$ ,  $P$ 最短斜距为 $r_0$。 我们做平面近似，所以有 $v_r = v_g = v_s$。

### 多普勒历程
![alt text](/assets/radar_sys1/image_sys/slide_spot1_2.png)  

如图虽然波束在旋转，但是雷达的移动与条带模式相同，所以其斜距模型是一样的。我们设 $\eta = 0$ 时， 目标与雷达的距离最短。设载波波长为 $\lambda$ ，所以目标的斜距历程为  

$$R(\eta) = \sqrt{r_0^2 + v_r^2\eta^2}$$  

由于斜距模型未变，所以多普勒频率依然为
$$f(\eta) = -\frac{2}{\lambda}\frac{\mathrm{d}R(\eta)}{\mathrm{d}\eta} \approx -\frac{2v_r^2 \eta}{\lambda R(\eta)} $$

多普勒频率的形式没有变化，这是我们期望的，但显然带宽与照射时间发生了变化，设照射时间为 $T_a$。目标刚进入波束覆盖区时，目标对应的斜视角为
$$\theta_{P0} = \theta_0 + \frac{\theta_a}{2}$$
目标刚退出波束覆盖区时，对应的斜视角为
$$\theta_{P1} = \theta_0-\omega T_a - \frac{\theta_a}{2}$$
所以照射时间为
$$T_{a} = \frac{r_0\tan(\theta_{P0}) - r_0\tan(\theta_{P1})}{v_r} \approx  \frac{r_0(\theta_a+\omega T_a)}{v_r \cos^2(\theta_{0})}$$

这里将 $\tan(\theta_{P1})$ 与 $\tan(\theta_{P0})$ 关于 $\theta_{0}$ 进行泰勒展开，做一阶近似。因此解得 $T_a$
$$T_a \approx \frac{r_0 \theta_a}{v_r \cos^2(\theta_0) - r_0 \omega} = \frac{r_0\theta_a}{v_r \cos^2(\theta_0)(1 - \frac{\omega r_0}{v_r \cos^2(\theta_0)})}$$

对比条带模式下照射时间，由于波束不再旋转，可得其照射时间为

$$T_{strip} = \frac{r_0 \theta_a}{v_r \cos^2(\theta_0)}$$

我们可以发现，可以定义一个缩放因子 $A$ ，来表征照射时间的变化

$$A = \frac{T_{strip}}{T_a} = 1 - \frac{\omega r_0}{v_r \cos^2(\theta_0)}$$

事实上，大部分论文会参照聚束模式，也就是波束中心会一直对着场景中心的模式，定义一个旋转中心 $O$，和旋转中心的最短斜距 $r_{rot}$，依旧如上图所示，运动过程中波束中心始终指向 $O$ , 则照射时间为

$$T_a = \frac{r_{rot} \tan(\theta_0) - r_{rot} \tan(\theta_0 - \omega T_a)}{v_r} \approx \frac{r_{rot} \omega T_a}{v_r \cos^2(\theta_0)}$$

可得在匀速旋转下，等效的旋转中心的最短斜距为

$$r_{rot} = \frac{v_r \cos^2(\theta_0)}{\omega}$$

因此放缩因子也可以表示为

$$A = 1 - \frac{r_0}{r_{rot}}$$

注意 ，如果我们让 $\omega$ 不变， 则$r_{rot}$ 是随目标变化的 ，而令 $r_{rot}$ 不变，则波束旋转不是匀速旋转。一般而言，成像过程中，波束的旋转角度较小，且使用场景中心的参数，则大致可以认为 $r_{rot}$ 是不变的 。我们考察P的多普勒频率变化，由于波束中心一直对准 $O$ ，我们设波束中心斜视角为  $\theta_0$  时 ，方位向时间 $\eta = 0$ 。由平面近似的几何关系可以知道 
$$v_r \eta = R(\eta) sin(\theta_0 - \omega \eta - \Delta \theta)$$ 

其中 $\Delta \theta$ 为雷达运动过程中目标 $P$ 的斜视角与波束中心斜视角的差，$\Delta \theta \in [-\theta_a/2, \theta_a/2]$。所以 $P$ 的多普勒频率为

$$f(\eta,\Delta \theta) = -\frac{2v_r^2 \eta}{\lambda R(\eta)} = \frac{2 v_r sin(\theta_0 - \omega \eta - \Delta \theta)}{\lambda}$$

所以多普勒带宽 $B_a$ 为 
$$B_a = |f(\frac{T_a}{2},-\frac{\theta_a}{2}) - f(-\frac{Ta}{2}, \frac{\theta_a}{2})| = \frac{4v_r\cos(\theta_0)sin(\frac{\omega T_a+ \theta_A}{2})}{\lambda} \approx \frac{2v_r\cos(\theta_0)}{\lambda}\theta_a + \frac{2 v_r \omega \cos(\theta_0)}{\lambda} T_a$$

由于 $\omega T_a + \theta_a$ 较小， 用到近似 $sin(x) \approx x$ 。这里我们可以看出滑动聚束模式中，多普勒频率可以分为两部分，一个与条带模式的多普勒带宽相同，另一个与照射时间成正比。同时，注意到由于斜视角的变化，多普勒中心也会随时间变化

$$f_{ac}(\eta) = \frac{2v_r\sin(\theta)}{\lambda} = \frac{2v_r\sin(\theta_0 - \omega \eta)}{\lambda}$$
$$\frac{\partial f_{ac}(\eta)}{\partial \eta} = -\frac{2v_r \omega \cos(\theta_0 - \omega \eta)}{\lambda} \approx  -\frac{2v_r \omega \cos(\theta_0)}{\lambda} \approx -\frac{2v_r^2 \omega \cos^3(\theta_0)}{\lambda r_{rot}} $$

可以发现多普勒中心频率的变化率近似为常数，所以不如设一个变化率 $k_rot$ ， 有
$$k_{rot} = \frac{2v_r^2 \omega \cos^3(\theta_0)}{\lambda r_{rot}}$$

此时多普勒带宽也可以写为

$$B_{a} \approx B_f + |k_{rot}| T_a$$

其中 $B_f$ 为条带模式下的多普勒带宽。到这一步，我们可以看出，只要通过某种方法多普勒频率中心的变化补偿掉，那么后续的步骤与条带模式的成像相同了。这一步我们不妨称之为解斜(dramping)。

## 信号处理
滑动聚束模式的回波，在距离向上与条带模式相同，在方位向上多出中心频率变化这个不同。中心频率变化由于近似为线性，很容易去除，所以如果 $PRF$ 大于滑动聚束模式的多普勒带宽，便只是多出一个解斜的预处理操作。但是，实际成像过程中，$PRF$ 是小于滑动聚束模式的多普勒带宽，这让处理的操作变得困难。需要注意的是，虽然总的多普勒带宽，即 $B_{a} \approx B_f + |k_{rot}| T_a > PRF$ ，但固定时间，可能的多普勒带宽依旧与条带模式相同 ，即 $B_{a}(\eta) \approx  B_f < PRF$ ，所以没有引起方位向的混叠，只是存在折叠现象。 其实这个困难在聚束模式中也存在，考虑到滑动聚束模式存在虚拟旋转中心，所以可以参考聚束模式的算法。

### two steps focus

