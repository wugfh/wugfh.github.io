---
layout: default
title: Stepped-Frequency Technology articles
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




## 参考论文
>
>1. X. Wang et al., "Precise Calibration of Channel Imbalance for Very High Resolution SAR With Stepped Frequency," in IEEE Transactions on Geoscience and Remote Sensing, vol. 55, no. 8, pp. 4252-4261, Aug. 2017, doi: 10.1109/TGRS.2017.2688728  
>
>2. X. Chen, Z. Dong, Z. Zhang, C. Tu, T. Yi and Z. He, "Very High Resolution Synthetic Aperture Radar Systems and Imaging: A Review," in IEEE Journal of Selected Topics in Applied Earth Observations and Remote Sensing, vol. 17, pp. 7104-7123, 2024, doi: 10.1109/JSTARS.2024.3374429
>
>3. R. T. Lord and M. R. Inggs, "High resolution SAR processing using stepped-frequencies," IGARSS'97. 1997 IEEE International Geoscience and Remote Sensing Symposium Proceedings. Remote Sensing - A Scientific Vision for Sustainable Development, Singapore, 1997, pp. 490-492 vol.1, doi: 10.1109/IGARSS.1997.615924.
>
>4. LONG Teng, DING Zegang, XIAO Feng, et al. Spaceborne high-resolution stepped-frequency
 SAR imaging technology[J]. Journal of Radars, 2019, 8(6): 782–792. doi: 10.12000/JR19076

### 论文1
这篇论文给出了step-frequency 超高精度雷达的实现，主要贡献在于子带间的误差校正算法，并完成了机载测试，效果很好。
他给出的校正算法主要是在雷达系统内部进行的，通过多路反馈，使用类似于模拟退火，遗传算法的优化算法得出最小误差。子带的传输方式是每个PRI传输一个子带。   
![alt text](/assets/step_frequency/article1_1.png)  
具体理论了解可以阅读论文。

### 论文2

该篇论文是关于高精度sar的综述，我只阅读了有关step-frequency 的部分。
1. 该篇论文对 step-frequency 的技术方向做出了分类，并浅显地指出各部分的优缺点。  
    ![alt text](/assets/step_frequency/article2_1.png)

2. 由于需要做仿真，我主要关注了系统与算法部分。
    * 算法上主要在于子带合成与子带误差校正两方面。其中更为重要的是子带误差校正，重点在于校正子带间的幅度，相位，时延误差。
        1.  Internal calibration technique：校正过程完全进行在系统内部，与外界无交互。外界的其他干扰可能无法校正，所以需要比较复杂的优化算法来在实际运行过程中降低误差。
        2. External calibration technique：校正过程种使用了外部的参考源，但在实际环境中没有参考源。
           
    * 系统上主要关注了各个子带间是怎么收发的，论文里将其分成3种
        1.  interpulse serial transmission：不同子带在不同的PRI里发，每个PRI只发一个子带。这样做会极大地增大PRF。
        2.  intrapulse serial transmission：不同子带在相同的PRI里发，但各自占据PRI里的不同时间。使用MIMO可以很好的实现该方法。
        3. intrapulse concurrent transmission：子带在同一时间一起发。能节省发射脉冲的时间，但有射频兼容性问题。
    ![alt text](/assets/step_frequency/article2_2.png)


### 论文3
这是一篇古老的有关 stepped-frequencies论文，这篇论文可以看到 stepped-frequencies的起源与基本思想。
解决硬件设备带宽有限的问题。如果想在距离向获得更高的分辨率，那么就必须要有更大的带宽，如何获得更大的带宽？使用更快的硬件设备是最简单的（也没那么简单，只是从系统复杂度来说是的）。如果设备能力不够呢，那就用在不同时间发不同频带，在接收时又拼起来，合成更大的带宽。

### 论文4 
看完这篇论文后，大致对子带拼接的性能分析有个概念，应该可以做步进频的仿真了。