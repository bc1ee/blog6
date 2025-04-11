---
title: 2025-04-11-RAS法的Matlab代码例子
date: 2025-04-11 09:57 +/-TTTT 0000
categories: [计算机, Matlab]
tags: [RAS, Miller&Blair]     # TAG names should always be lowercase
---

本例子基于 Miller&Blair 的教科书，`Input-Output Analysis Foundations and Extensions` 第九章的内容。是一个step by step的RAS法的计算案例，旨在理解RAS方法的基本逻辑和计算思路。

RAS法的目的在于知道了旧版的A0矩阵和新版A矩阵的行和&列和，却不知道新版A矩阵的情况下，将A0矩阵更新为A矩阵。

- Z：投入产出表中的交易额矩阵。
- A：投入系数矩阵。

# RAS法分步过程step by step

## 设置初始值
``` matlab
%% 9.4.2 Example of the RAS Procedure
% (1) RAS: step by step
A0 =[0.120 0.100 0.049; 0.210 0.247 0.265; 0.026 0.249 0.145];
Z1 = [98 72 75; 65 8 63; 88 27 44];
x1 = [421; 284; 283];
u1 = [245 136 159]'; % sum(Z1, 2)
v1 = [ 251   107   182]'; % sum(Z1, 1)
```
## 用RAS法第一次更新A矩阵
``` matlab
% A_up1
Z_up1 = A0 * diag(x1);
u_up1 = sum(Z_up1, 2);
r_hat_up1 = diag(u1) / diag(u_up1);
A_up1 = r_hat_up1 * A0;
``` 

## Check更新结果
``` matlab
% checking
% row sum of A_up1 * diag(x1) will equal u1
sum( A_up1 * diag(x1), 2)
% checking end
```
## 用RAS法第二次更新A矩阵
``` matlab
% A_up2
v_up1 = sum( A_up1 * diag(x1), 1);
s_hat_up1 = diag(v1) / diag(v_up1);
A_up2 = A_up1 * s_hat_up1;
``` 
## 用RAS法第三四五次更新A矩阵
第三四次和第一二次操作方法一样。

``` matlab
% A_up3
u_up2 = sum(A_up2 * diag(x1), 2);
r_hat_up2 = diag(u1) / diag(u_up2);
A_up3 = r_hat_up2 * A_up2;

% A_up4
s_hat_up2 = diag(v1) / diag(sum( A_up3 * diag(x1), 1));
A_up4 = A_up3 * s_hat_up2

% A_up5
r_hat_up3 = diag(u1) / diag(sum(A_up4 * diag(x1), 2));
A_up5 = r_hat_up3 * A_up4;
```

# RAS总体过程
``` matlab
%% %% 9.4.2 Example of the RAS Procedure
% (2) RAS: loop
A = A0;
for loop = 1:12
  r_hat = diag(u1) / diag(sum(A * diag(x1), 2));
  A = r_hat * A;
  s_hat = diag(v1) / diag(sum(A * diag(x1), 1));
  A = A * s_hat;
end
```

# 当A矩阵中一个元素已知的情况下的RAS法

``` matlab
%% 9.4.6 Modified Example: One Coefficient Known in Advance
% One coefficient known in advance
K =[0 0 0; 0 0 0; .209 0 0];
A0_bar = A0;
A0_bar(3, 1) = 0;

Z1_31 = K(3, 1) * x1(1);

u1_bar = u1;
u1_bar(3) = u1_bar(3) - Z1_31;
v1_bar = v1;
v1_bar(1) = v1_bar(1) - Z1_31;


A = A0_bar;
for loop = 1:12
  r_hat = diag(u1_bar) / diag(sum(A * diag(x1), 2));
  A = r_hat * A;
  s_hat = diag(v1_bar) / diag(sum(A * diag(x1), 1));
  A = A * s_hat;
end
```
