---
layout: post
title: radar_sys_sim1
---

数据集 RADARSAT1 Vancouver airport

## RDA 成像算法仿真

### 小斜视角，第一次仿真

#### 参数与初始化
```Matlab
c = 299792458;                     %光速
Fs = 32317000;      %采样率                                   
start = 6.5959e-03;         %开窗时间 
Tr = 4.175000000000000e-05;        %脉冲宽度                        
f0 = 5.300000000000000e+09;                    %载频                     
PRF = 1.256980000000000e+03;       %PRF                     
Vr = 7062;                       %雷达速度     
B = 30.111e+06;        %信号带宽
fc = -6900;          %多普勒中心频率
Ka = 1733;
Fa = PRF;

lambda = c/f0;
Kr = B/Tr;
[Na, Nr] = size(data_1);
theta_rc = asin(abs(fc)*lambda/(2*Vr));
R_eta_c = 2*Vr^2*cos(theta_rc)^2/(lambda*Ka);
R0 = R_eta_c*cos(theta_rc);
eta_c = 2*Vr*sin(theta_rc)/lambda;

f_tau = (-Nr/2:Nr/2-1)*(Fs/Nr);
f_eta = fc + (-Na/2:Na/2-1)*(Fa/Na);

tau = 2*R_eta_c/c + (-Nr/2:Nr/2-1)*(1/Fs);
eta = eta_c + (-Na/2:Na/2-1)*(1/Fa);

[Ext_time_tau_r, Ext_time_eta_a] = meshgrid(tau, eta);
[Ext_f_tau, Ext_f_eta] = meshgrid(f_tau, f_eta);

```

#### 距离压缩

```matlab
data_fft_r = fftshift(fft(fftshift(data_1, 2), Nr, 2), 2);
Hr = (abs(Ext_f_tau) < B).*exp(1j*pi*Ext_f_tau.^2/Kr);
data_fft_cr = Hr.*data_fft_r;
data_cr = fftshift(ifft(fftshift(data_1, 2), Nr, 2), 2);
```

#### 方位压缩
```matlab
data_fft_a = fftshift(fft(fftshift(data_cr, 1), Na, 1), 1);
data_fft_a_rcmc = data_fft_a; % 后续添加RCMC
Ha = exp(1j*pi*Ext_f_eta.^2/Ka);
Offset = exp(-1j*2*pi*Ext_f_eta.*eta_c);
data_fft_ca_rcmc = data_fft_a_rcmc.*Ha.*Offset;
data_ca_rcmc = fftshift(ifft(fftshift(data_fft_ca_rcmc, 1), Na, 1), 1);
```
#### 效果

![第1次仿真效果](/assets/sar_sim3_1.png)  
压缩失败，什么也没有  

检查代码，发现方位向匹配滤波器错误，差个负号。
```matlab
Ha = exp(-1j*pi*Ext_f_eta.^2/Ka);
```
还有距离压缩那，ifft的对象错了
```matlab
data_cr = fftshift(ifft(fftshift(data_fft_cr, 2), Nr, 2), 2);
```
改正

![第2次仿真效果](/assets/sar_sim3_2.png)  

斜视角计算错误，不应该对多普勒中心频率取模
```matlab
theta_rc = asin(fc*lambda/(2*Vr));
```

由平行于距离向的横线，考虑是距离压缩出现了问题。应该是距离压缩的系统函数由问题
```matlab
Hr = (abs(Ext_f_tau) < B).*exp(1j*pi*Ext_f_tau.^2/Kr);
```
前面的矩形窗错了，但应该不会造成怎么大的影响。改正后，表达式没有问题，考虑是参数出现问题。由于距离向调频率可正可负，将Kr置为负数，效果如图

![第3次仿真效果](/assets/sar_sim3_3.png)  

可以看到点目标，但有散焦与虚像的问题。添加距离徙动校正
```matlab
IN_N = 8;
R0_RCMC = tau*c/2;  
delta_R = lambda^2*f_eta'.^2.*R0_RCMC/(8*Vr^2);
delta_R_cnt = delta_R*2/(c*(1/Fs));
for j = 1:Na
    for k = 1:Nr
        data_fft_a_rcmc(j,k) = 0;
        dR = delta_R_cnt(j,k);
        for m = -IN_N/2:IN_N/2-1
            if(k+floor(dR)+m>=Nr)
                data_fft_a_rcmc(j,k) = data_fft_a_rcmc(j,k)+data_fft_a(j,Nr)*sinc(dR-(Nr-k));
            elseif(k+floor(dR)+m<=1)
                data_fft_a_rcmc(j,k) = data_fft_a_rcmc(j,k)+data_fft_a(j,1)*sinc(dR-(1-k));
            else
                data_fft_a_rcmc(j,k) = data_fft_a_rcmc(j,k)+data_fft_a(j,k+floor(dR)+m)*sinc(dR-floor(dR)-m);
            end
        end
    end
end

```
同时减小对比度，方便观察
```matlab
data_ca = 20*log(abs(data_ca)+1);
data_ca_rcmc = 20*log(abs(data_ca_rcmc)+1);
```

![第4次仿真效果](/assets/sar_sim3_4.png)  