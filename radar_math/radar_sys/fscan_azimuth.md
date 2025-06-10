---
layout: default
title: 方位向频率扫描
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

## 系统结构

<center>

![alt text](/assets/Fscan_azimuth/geometry.drawio.png)

</center>

如图所示为成像过程中沿航迹切面，其中红色和蓝色用于雷达在不同慢时间 $\eta_1$ , $\eta_2$ 所发射的波束。设频率扫描天线的波束宽度为 $\theta_{az}$ , 扫描宽度为 $\theta_{sc}$。设当慢时间为 $\eta_1$ 时，目标P的斜视角为 $\theta_1$ ,$\eta_2$ 时斜视角为 $\theta_2$ 。设发射频率为载频 $f_c$ 时，波束中心的斜视角为 $\theta_c$。对于方位向频率扫描系统，其视角与频率差近似线性[1]。

$$\Delta f \approx \alpha \Delta \theta$$

其中 $\Delta f = f-f_c$ 为发射频率与载频之差。$\Delta \theta = \theta - \theta_c$ 为视角差。$\eta_1$ 时，当 $\Delta f$ 满足 $\theta_1 \in [\theta_c + \frac{\Delta f}{\alpha} - \frac{\theta_{az}}{2}, \theta_c + \frac{\Delta f}{\alpha} + \frac{\theta_{az}}{2}]$ , 目标反射的回波有效。这里假设 $\alpha > 0$  , 因此对于在 $\eta_1$ 时 P 反射的频率范围 $f_r \in [f_r^l(\eta_1),f_r^u(\eta_1)]$ 为

$$f_r^l(\eta_1) = \alpha (\theta_1 - \theta_c - \frac{\theta_{az}}{2})$$
$$f_r^u(\eta_1) = \alpha (\theta_1 - \theta_c + \frac{\theta_{az}}{2})$$

设系统发射带宽为 $B_{sys} = \alpha \theta_{sc}$ ，类似于[2], $\eta_1$ 时P反射的带宽 $B_w$ 为

$$B_w = \frac{\theta_{az}}{\theta_{sc}} B_{sys}$$
$$\alpha = \frac{B_{sys}}{\theta_{sc}}$$

如果卫星与目标的斜距模型为

$$R(\eta) = \sqrt{R_0^2 + V_r^2\eta^2}$$

其中 $ R(\eta) $ 为斜距，$R_0$ 为目标到平台的最短斜距 ，$V_r$ 为等效平台移动速度。则多普勒频率与斜视角间满足如下关系

$$f_a = \frac{2V_r}{\lambda} \sin(\theta)$$

代入到 $[f_r^l(\eta_1),f_r^u(\eta_1)]$ 中有

$$f_r^l(f_a) = \alpha [\arcsin(\frac{\lambda f_a}{2V_r}) - \theta_c - \frac{\theta_{az}}{2}]$$
$$f_r^u(f_a) = \alpha [\arcsin(\frac{\lambda f_a}{2V_r}) - \theta_c + \frac{\theta_{az}}{2}]$$

对于某个脉冲间隔，其接收回波的多普勒频率 $f_a \in [f_a^l(f_r), f_a^u(f_r)]$ 与发射频率 $f_r$ 相关，为讨论方便，假设发射信号为线性调频信号，则有

$$f_a^l(f_r) = \frac{2 V_r}{\lambda} \sin(\theta_c + \frac{f_r}{\alpha} - \frac{\theta_{az}}{2})$$

$$f_a^l(f_r) = \frac{2 V_r}{\lambda} \sin(\theta_c + \frac{f_r}{\alpha} + \frac{\theta_{az}}{2})$$

$$B_{fov}(f_r) = \frac{2 V_r}{\lambda} [\sin(\theta_c + \frac{f_r}{\alpha} + \frac{\theta_{az}}{2}) - \sin(\theta_c + \frac{f_r}{\alpha} - \frac{\theta_{az}}{2})]$$

上述中 $f_a \in [f_a^l(f_r), f_a^u(f_r)]$ 与 $f_r \in [f_r^l(\eta_1),f_r^u(\eta_1)]$ 是相互的，分别从不同角度表诉了频率扫描模式中多普勒频率与脉冲频率的相互约束关系，仿真中实现其一即可。 其中 $B_{fov}(f_r)$ 为发射频率为 $f_r$ 时， 接收回波的多普勒带宽。为了避免方位模糊，PRF 应该大于 $B_{fov}(f_r)$。而在此脉冲间隔中，回波的多普勒带宽 $B_d$ 为

$$B_d =  \frac{2 V_r}{\lambda} [\sin(\theta_c + \frac{\theta_{sc}}{2}) - \sin(\theta_c - \frac{\theta_{sc}}{2})]$$




## 参考文献

>>[1] T. Goto, K. Tsushima and J. T. Sri Sumantyo, "Synthetic Aperture Radar Imaging with Frequency Scanning in Azimuth Direction," IGARSS 2019 - 2019 IEEE International Geoscience and Remote Sensing Symposium, Yokohama, Japan, 2019  
>>[2] M. Younis, F. Q. de Almeida, T. Bollian, M. Villano, G. Krieger and A. Moreira, "A Synthetic Aperture Radar Imaging Mode Utilizing Frequency Scan for Time-of-Echo Compression," in IEEE Transactions on Geoscience and Remote Sensing, vol. 60, pp. 1-17, 2022


