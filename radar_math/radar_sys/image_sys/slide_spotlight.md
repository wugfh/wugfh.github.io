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

$$R(\eta) = \sqrt{r_0^2 + v_r^2\eta^2} \approx r_0 + \frac{v_r^2\eta^2}{2r_0}$$  

由于斜距模型未变，所以多普勒频率依然为
$$f_{\eta} = -\frac{2}{\lambda}\frac{\mathrm{d}R(\eta)}{\mathrm{d}\eta} \approx -\frac{2v_r^2 \eta}{\lambda r_0}$$

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

事实上，大部分论文会参照聚束模式，也就是波束中心会一直对着场景中心的模式，定义一个旋转中心 $O$，和旋转中心的最短斜距 $r_{rot}$，依旧如上图所示，我们在 $P$ 做一条平行于雷达轨迹的线。设P照射期间，波束中心移动距离为 $X_f$ ，则

$$\frac{X_f}{v_r T_a} = \frac{r_{rot}-r_0}{r_{rot}}$$

由此可得，平面近似下，相较于条带模式的波束中心的移动，滑动聚束模式下波束中心移动有个缩放因子

$$A^\prime = 1 - \frac{r_0}{r_{rot}}$$

而波束中心的移动表征了照射时间的变化，令 $A = A^\prime$ ， 有

$$r_{rot} = \frac{v_r \cos^2(\theta_0)}{\omega}$$

注意 ，如果我们让 $\omega$ 不变， 则$r_{rot}$ 是随目标变化的 ，而令 $r_{rot}$ 不变，则波束旋转不是匀速旋转。一般而言，成像过程中，波束的旋转角度较小，大致可以认为 $r_{rot}$ 是不变的 。


## 信号处理
