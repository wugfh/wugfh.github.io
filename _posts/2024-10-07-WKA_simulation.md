---
layout: post
title: wk 算法实测数据成像
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

[数据集 RADARSAT1](https://github.com/wugfh/sar/tree/main/data/RadarSAT%E6%95%B0%E6%8D%AE/RadarSAT%E6%95%B0%E6%8D%AE)  

## $\omega$ K 成像算法仿真

### 参数与初始化
```matlab
load("../data/English_Bay_ships/data_1.mat");

c = 299792458;                     %光速
Fs = 32317000;      %采样率                                   
start = 6.5959e-03;         %开窗时间 
Tr = 4.175000000000000e-05;        %脉冲宽度                        
f0 = 5.300000000000000e+09;                    %载频                     
PRF = 1.256980000000000e+03;       %PRF                     
Vr = 7062;                       %雷达速度     
B = 30.111e+06;        %信号带宽
fc = -6900;          %多普勒中心频率
Fa = PRF;

% Ka = 1733;

lambda = c/f0;
Kr = -B/Tr;
% Kr = -7.2135e+11;

[Na_tmp, Nr_tmp] = size(data_1);
kai = kaiser(Nr_tmp, 2.5);
Ext_kai = repmat(kai', Na_tmp, 1);
data_1 = data_1.*Ext_kai;
[Na, Nr] = size(data_1);
data = zeros(Na+Na, Nr+Nr);
data(1:Na, 1:Nr) = data_1;
[Na,Nr] = size(data);


R0 = start*c/2;
theta_rc = asin(fc*lambda/(2*Vr));
Ka = 2*Vr^2*cos(theta_rc)^3/(lambda*R0);
R_eta_c = R0/cos(theta_rc);
eta_c = 2*Vr*sin(theta_rc)/lambda;

f_tau = fftshift((-Nr/2:Nr/2-1)*(Fs/Nr));
f_eta = fc + ((-Na/2:Na/2-1)*(Fa/Na));

tau = 2*R_eta_c/c + (-Nr/2:Nr/2-1)*(1/Fs);
eta = eta_c + (-Na/2:Na/2-1)*(1/Fa);

[Ext_time_tau_r, Ext_time_eta_a] = meshgrid(tau, eta);
[Ext_f_tau, Ext_f_eta] = meshgrid(f_tau, f_eta);

R_ref = R_eta_c; % 将参考目标设为场景中心
```
数据集，参数与RD，CS算法保持一致。

### 参考函数相乘

$\omega K$ 算法与CS算法类似。首先也是校正参考点的RCMC，即一致RCMC，在 $\omega K$ 算法中，为参考函数相乘。对于距离为 $R_0$ 处的目标，其回波基带相位为 

$$\theta_{2df}(f_\tau,f_\eta) = -\frac{4 \pi R_0}{c}\sqrt{(f_0+f_\tau)^2-\frac{c^2 f_\eta^2}{4 V_r^2}} - \frac{\pi f_\tau^2}{K_r}$$  

相位中第二部分为线性调频信号的频域二次相位。第一部分本是线性相位，但由于RCM，与方位向频率产生关系，是需要校正的部分。所以参考相位相乘目的在于将参考目标相位的第一部分补偿掉，并将其线性相位置为0，这样其他目标的校正便是相对于参考目标的了。对应的滤波器为

$$H_{rfm} = \frac{4 \pi R_{ref}}{c}\sqrt{(f_0+f_\tau)^2-\frac{c^2 f_\eta^2}{4 V_r^2}}$$

如果将第二部分也补偿掉，便提前完成了距离压缩，这部分可以现在做，也可以在stolt插值后做。书中推荐在之前做，但我的聚焦效果并不好。

![alt text](/assets/wk_sim/range_compress_before_stolt.png)

参考函数相乘实现很简单
```matlab
data_ftau_feta = fft2(data); % 首先变换到距离多普勒域

H_rfm = exp(4j*pi*(R_ref)/c*sqrt((f0+Ext_f_tau).^2-c^2*Ext_f_eta.^2/(4*Vr^2)) + 1j*pi*Ext_f_tau.^2/Kr);
data_ftau_feta = data_ftau_feta.*H_rfm; %一致rcmc
```

### stolt 插值

$\omega K$ 算法的重点便是在stolt插值上。stolt插值的目的在于完成距离频率空间的变换。在参考函数相乘后，剩余相位为

$$\theta_{rfm} = -\frac{4 \pi (R_0-R_{ref})}{c}\sqrt{(f_0+f_\tau)^2-\frac{c^2 f_\eta^2}{4 V_r^2}}$$

如果没有RCM，相位里应该没有有关方位频域的项，所以算法提出了一个新的距离向频率轴，使得满足

$$f_\tau^{\prime}+f_0 = \sqrt{(f_0+f_\tau)^2-\frac{c^2 f_\eta^2}{4 V_r^2}}$$

对于一个坐标轴，其刻度肯定是线性增长。但显然，由于频率是离散的，新的stolt频率空间的离散点不一定在旧的频率空间上有对应的离散点，所以需要通过插值估算出新频率空间上离散点的值。

其实现方式与rd算法的RCMC相似。

```matlab
Ext_map_f_tau = sqrt((f0+Ext_f_tau).^2+c^2*Ext_f_eta.^2/(4*Vr^2))-f0; %线性变化的stolt频率轴与原始频率轴的对应（stolt 映射）

delta = (Ext_map_f_tau - Ext_f_tau)/(Fs/Nr);
delta_int = floor(delta);
delta_remain = delta-delta_int;

%插值使用8位 sinc插值
sinc_N = 8;
data_ftau_feta_stolt = zeros(Na,Nr);
for i = 1:Na
    for j = 1:Nr
        predict_value = zeros(1, sinc_N);
        dR_int = delta_int(i,j);
        sinc_x = delta_remain(i,j) - (-sinc_N/2:sinc_N/2-1);
        sinc_y = sinc(sinc_x);
        for m = 1:sinc_N
            index = dR_int+m+j-sinc_N/2;
            if(index > Nr)
                predict_value(m) = data_ftau_feta(i,Nr);
            elseif(index < 1)
                predict_value(m) = data_ftau_feta(i,1);
            else
                predict_value(m) = data_ftau_feta(i,index);
            end
        end
        data_ftau_feta_stolt(i,j) = sum(predict_value.*sinc_y)/sum(sinc_y);
    end
end
```

### 成像

```matlab

data_ftau_feta_stolt = data_ftau_feta_stolt.*exp(-4j*pi*R_ref*Ext_f_tau/c);
data_final = fftshift(ifft2(data_ftau_feta_stolt), 1);
data_final = flip(data_final, 1);

%简单的后期处理
data_final = abs(data_final)/max(max(abs(data_final)));
data_final = 20*log10(data_final+1);
data_final = data_final.^0.4;
data_final = abs(data_final)/max(max(abs(data_final)));
```

### 效果

<center>没有stolt插值</center>  
 
![alt text](/assets/wk_sim/no_stolt.png)  

  
<center>有stolt插值</center>  

![alt text](/assets/wk_sim/complete.png)  



