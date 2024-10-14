---
layout: default
title: 方位向多孔径重建算法
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

## 方位向多孔径重建算法表述
![alt text](/assets/multi_channel/m_chan2_1.png)

如图，多孔径雷达的信号传输框图，$U_s(f)$ 为发射信号频域。 $A(f)，H_s(f)$ 可以简单理解为发射信号所途径的信道的系统响应（发射天线->地面->接收天线）。 $U(f)$ 与传统单孔径收发一体sar雷达所接收的信号一致， $H_j(f)$ 为 对应孔径相较于传统sar雷达所发生的变化，或者说是多孔径系统附加的系统响应，在上一篇中，我们已经分析得到了其解析式。

$$H_j(f) = exp(j \Delta \phi_j) \cdot exp(-j 2\pi f \Delta t_j)$$

$$\Delta \phi_j = -\frac{v_g}{v_s}\frac{\pi \Delta x_j^2}{2 \lambda R_0}$$  

$$\Delta t_j = \frac{\Delta x_j^2}{2 v_s}$$  

合并得到

$$H_j(f) = exp(-j \pi \frac{v_g}{v_s}\frac{\Delta x_j^2}{2 \lambda R_0}-j \pi \frac{\Delta x_j^2}{v_s} f)$$

子通道输出信号为 $U_j(f)$，以 $PRF$ 采样率采样得到的信号为 $U_{j,p}(f)$ 。如果想要使用传统sar的聚焦算法，需要将多孔径系统的附加系统响应补偿掉，或者说从 $[U_{1,p}(f) \cdots U_{j,p}(f) \cdots U_{N,p}(f)]$ 中重建出 $U_p(f)$，$U_p(f)$ 为 $U(f)$ 以 $N \cdot PRF$ 采样率采样得到的信号。
由采样定理可以得到，混叠信号 $U_{j,p}$ 为

$$U_{j,p}(f) = \sum_{n=0}^{N-1}{U_j(f+n \cdot PRF)}=\sum_{n=0}^{N-1}U(f+n \cdot PRF)H_{j}(f+n \cdot PRF)$$

写成矩阵形式

$$\bf{U_{jp}} (f) = \bf{U_p}(f) \bf{H}(f)$$

$$\bf{U_{jp}} (f) = \begin{bmatrix}
U_{1,p}(f) & \cdots & U_{N,p}(f)
\end{bmatrix}$$

$$\bf{U_p}(f) = \begin{bmatrix}
U_p(f) & \cdots & U_p(f+(N-1) \cdot PRF)
\end{bmatrix}$$

$$\bf{H}(f) = \begin{bmatrix}
H_1(f) & \cdots & H_{N}(f)\\
H_1(f + PRF) & \cdots & H_{N}(f + PRF)\\
\vdots & \ddots & \vdots \\
H_1(f + (N-1) \cdot PRF) & \cdots & H_{N}(f + (N-1) \cdot PRF)\\
\end{bmatrix} $$

显然，如果 $\bf{H}(f)$ 可逆，则

$$\bf{U_p}(f) = \bf{U_{jp}}(f) \bf{H^{-1}}(f)$$

令 $\bf{P}(f) = \bf{H^{-1}}(f)$，$\bf{P}(f)$ 有如下形式

$$\bf{P}(f) = \begin{bmatrix}
P_1(f) & \cdots & P_{1}(f + (N-1) \cdot PRF)\\
P_2(f) & \cdots & P_{2}(f + (N-1) \cdot PRF)\\
\vdots & \ddots & \vdots \\
P_N(f) & \cdots & P_{N}(f + (N-1) \cdot PRF)\\
\end{bmatrix} $$

下图为 $N = 2$ 时的示例

![alt text](/assets/multi_channel/m_chan2_2.png)  

从上述讨论中，可以看出，该算法无法抑制 频率在 $N \cdot PRF$ 带宽外的信号，与传统sar相似的多普勒混叠依然存在。

## 算法实现
从 $H_j(f)$ 的解析式中可以发现，$H_j(f)$ 与最短斜距相关，因此 $P_j(f)$ 也与最短斜距相关。所以对于二维sar回波信号来说，每个距离门的重构系数 $\bf{P}(f)$ 不同，我们可能需要逐个进行重建。

同样，也必须考虑距离徙动是否影响重构。根据论文的推算，单平台的多孔径sar无需考虑距离徙动对重构系数的影响。多平台的多孔径sar，不同孔径间的距离徙动需要考虑一个常数项的漂移，这个漂移带来的相位误差就是 

$$\Delta \phi_j = -\frac{v_g}{v_s}\frac{\pi \Delta x_j^2}{2 \lambda R_0}$$

等效为

$$\Delta R_{0,j} = -\frac{v_g}{v_s}\frac{\Delta x_j^2}{4 R_0}$$

在重构时，这个距离漂移会导致重构系数与斜距不对应（重构系数的计算使用的是 $R_0$，但由于距离徙动，此时信号对应的距离为 $R_0 + \Delta R_{0,j}$）。因此，先进行方位压缩，同时将这个漂移补偿掉，再进行重构比较好。

