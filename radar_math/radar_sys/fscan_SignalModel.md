---
layout: default
title: F-scan 信号模型
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

## 信号模型

### 线性F-scan 信号模型
天线的频扫特征通过多个发射接收单元实现，每个发射单元发射相同信号，但各个单元之间通过TTD（true time delay）进行相移方式。以线性调频信号作为例子。每个发射单元发射的信号为

$$s_{ref}(t) = rect[t/T_p] \cdot e^{j(2 \pi f_c t + \pi K(t-T_p/2)^2)}$$   


<center> 

发射单元构成方式

![alt text](/assets/Fscan/发射单元构成.png)

</center>

其中 $f_c$ 为载频频率，B 为信号带宽，而 $K = B/T_p$ 。不同信号单元按顺序，以一定时延 $\tau$ 接连发射，则第 $n$ 个发射单元发射的信号为

$$s_n(t) = s(t) \otimes \delta(t - (n- \frac{N+1}{2})\cdot \tau)$$

$N$ 为发射单元数量 ，以中心发射单元为相位，时间参考. $\otimes$ 表示卷积运算, $\delta(t)$ 表示冲激响应函数.
 

<center>航迹切面, 参考[1] </center>

![alt text](/assets/Fscan/cross_track.png)

地面的点目标会发射所有发射单元所发送的信号, 也就是对所有发射信号进行求和, 设发射单元相邻单元的间距为 $d$ 光速为 $c$ . 则第 $n$ 个发射单元的传播时延为 $-(n-\frac{N+1}{2}) \cdot d\sin\phi / c$ , 总时延为

$$\tau_n = (n-\frac{N+1}{2}) \cdot (\tau - \frac{d\sin\phi}{c})$$

$$s(t,\phi) = \sum_{n=1}^{N}rect(\frac{t-\tau_n}{T_p}) e^{j[2\pi f_c (t-\tau_n) + \pi K (t-\tau_n)^2]}$$

根据系统参数,可以得到 $N \cdot \tau_n << T_p$ . 并将平方打开, 可以得到

$$s(t,\phi) \approx s_{ref}(t) \cdot \sum_{n=1}^{N} e^{-j[2 \pi (f_c + K(t-\frac{T_p}{2}))\tau_n - \pi K \tau_n^2]}$$

后面求和式可以使用傅里叶变换与驻级相位定理求出,为

$$p_e(t,\phi) = \sin[N \cdot \frac{\pi (\tau c - d \sin \phi)}{\lambda(t)}] / \sin[\frac{\pi (\tau c - d \sin \phi)}{\lambda(t)}]$$

$$\lambda(t) = c/[f_c + K(t-T_p)]$$

所以总发射信号波形为

$$s(t,\phi) = s_{ref}(t) \cdot p_e(t,\phi)$$

相应的设当前斜距为 $R$ , 算上传播时延，考虑到接收机也使用与发射机相同的频扫接收，则接收回波为

$$s_r(t,\phi,R) = [s_{ref}(t) \cdot p_e(t,\phi) \cdot p_e(t,\phi)] \otimes \delta(t - \frac{2 R}{c})$$


## 参考文献
>[1] L. Nan, G. Gai, T. Shiyang and Z. Linrang, "Signal Modeling and Analysis for Elevation Frequency Scanning HRWS SAR," in IEEE Transactions on Geoscience and Remote Sensing, vol. 58, no. 9, pp. 6434-6450, Sept. 2020
