---
layout:       post
title:        "密码学学习--签名算法"
author:       "Luc"
header-style: text
catalog:      true
mathjax:      true
tags:
    - 密码学
---

## 1 Elgamal 签名

### 1.1 公共参数

- $\alpha$ 是 $q$ 的原根。

### 1.2 Gen 过程

- 随机选择 $X_A \in \mathbb{Z}$， $1 < X_A < q - 1$。

- 计算 $Y_A = \alpha^{X_A}\bmod q$。

- 私钥 $\\{X_A\\} $，公钥 $\\{Y_A\\}$。

### 1.3 Sign 过程

- $m = H(M)$， $0 \leq m \leq q-1$。

- 随机选择 $K \in \mathbb{Z}$， $1 \leq K \leq q-1$ 和 $gcd(K,q-1)=1$。

- 计算 $S_1 = \alpha^{K}\bmod q$。

- 计算 $S_2 = [K^{-1}(m - X_AS_1)]\bmod(q-1)$。

- 输出签名 $(S_1, S_2)$。

### 1.4 Vrfy 过程

- 计算 $V_1 = \alpha^m\bmod q$。

- 计算 $V_2 = [(Y_A)^{S'_1}(S'_2)^{S'_1}]\bmod q$。

- 验证 $V_1 \overset{\text{?}}= V_2$。

## 2 Schnorr 签名

### 2.1 公共参数

- 选择素数 $p$ 和 $q$ ， $p - 1 = 0\bmod q$。

- 选择 $\alpha \in \mathbb{Z}$， $\alpha^q = 1\bmod p$。

### 2.2 Gen 过程

- 随机选择 $s \in \mathbb{Z}$，$0 < s < q$。

- 计算 $v = \alpha^{-s}\bmod p$。

- 私钥 $\\{s\\}$，公钥$\\{v\\}$。

### 2.3 Sign 过程

- 随机选择 $r \in \mathbb{Z}$，$0 < r < q$。

- 计算 $x = \alpha^r\bmod p$。

- 计算 $e = H(M\\|x)$。

- 计算 $y = (r + se)\bmod q$。

- 输出签名 $(e, y)$。

### 2.4 Vrfy 过程

- 计算 $x' = (\alpha^{y'}v^{e'})\bmod p$。

- 验证 $e' \overset{\text{?}}= H(M\\|x')$。

## 3 DSA 签名

### 3.1 公共参数

- 选择素数 $p$，$2^{L-1} < p < 2^L$，$512\leq L\leq 1024$，$L = 0\bmod 64$。

- 选择素数 $q$，$p - 1 = 0\bmod q$，$2^{N-1} < q < 2^N$。

- 选择 $h \in \mathbb{Z}$，$h^{(p - 1) / q}\bmod p > 1$，$1 < h < p - 1$

- 计算 $g = [h(p - 1)/q]\bmod p$。

### 3.2 Gen 过程

- 随机选择 $x\in \mathbb{Z}$，$0 < x < q$。

- 计算 $y = g^x\bmod p$。

- 私钥 $\\{x\\}$，公钥 $\\{y\\}$。

### 3.3 Sign 过程

- 随机选择 $k \in \mathbb{Z}$，$0 < k < q$。

- 计算 $r = (g^k\bmod p)\bmod q$。

- 计算 $s = [k^{-1}(H(M) + xr)]\bmod q$。

- 输出签名 $(r, s)$。

### 3.4 Vrfy 过程

- 计算 $w = (s')^{-1}\bmod q$。

- 计算 $u_1 = [H(M')w]\bmod q$。

- 计算 $u_2 = (r'w)\bmod q$。

- 计算 $v = [(g^{u_1}y^{u_2})\bmod p]\bmod q$。

- 验证 $v \overset{\text{?}}= r'$。

## 4 ECDSA 签名

### 4.1 公共参数

- 选择素数 $q$。

- 选择 $a\in Z_p$ 和 $b\in Z_p$，定义椭圆曲线 $y^2 = (x^3 + ax + b)\bmod q$。

- 椭圆曲线的基点 $G = (x_g, y_g)$。

- 基点 $G$ 的阶为 $n$。

### 4.2 Gen 过程

- 随机选择 $d\in \mathbb{Z}$，$1\leq d\leq n - 1$。

- 计算 $Q = dG$。

- 私钥 $\\{d\\}$，公钥 $\\{Q\\}$。

### 4.3 Sign 过程

- 随机选择 $k\in \mathbb{Z}$，$1\leq k\leq n - 1$。

- 计算 $P = (x, y)= kG$ 和 $r = x\bmod n$，如果 $r = 0$ 则重新选择。

- 计算 $e = H(m)$。

- 计算 $s = [k^{-1}(e + dr)]\bmod n$，如果 $s = O$ 则重新选择。

- 输出签名 $(r, s)$。

### 4.4 Vrfy 过程

- 检验 $r'$ 和 $s'$ 是否是 $[1, n - 1]$ 中的整数。

- 计算 $e' = H(m')$。

- 计算 $w = s'^{-1}\bmod n$。

- 计算 $u_1 = e'w$。

- 计算 $u_2 = r'w$。

- 计算 $X = (x_1, y_1) = u_1G + u_2Q$，如果 $X = O$ 则拒绝。

- 计算 $v = x_1\bmod n$。

- 验证 $v \overset{\text{?}}= r'$。

## 5 RSA 签名

### 5.1 Gen 过程

- 选择两个 $n$ 比特的素数 $p$ 和 $q$。

- 计算 $n = p\times q$。

- 计算 $\phi(n) = (p - 1)\times (q - 1)$。

- 随机选择 $e\in \mathbb{Z}$，$0 < e < \phi(n)$，$gcd(e, \phi(n)) = 1$。

- 计算 $d = e^{-1}\bmod \phi(n)$。

- 私钥 $\\{p, q, d\\}$，公钥 $\\{n, e\\}$。

### 5.2 Sign 过程

- 计算 $\sigma = m^d\bmod n$。

- 输出签名 $\sigma$。

### 5.3 Vrfy 过程

- 计算 $m'' = \sigma'^e\bmod n$。

- 验证 $m' \overset{\text{?}}= m''$。

## 6 SM2 签名

### 6.1 公共参数

- 椭圆曲线 $E(F_q)$ 的规模 $q$ 和两个元素 $a,b\in F_q$。

- 椭圆曲线的基点 $G = (x_G, y_G)$。

- 基点 $G$ 的阶。

### 6.2 Gen 过程

- 随机选择 $d_A\in \mathbb{Z}$，$1\leq d_A\leq n - 1$。

- 计算 $P_A = [d_A]G = (x_A, y_A)$。

- 私钥 $\\{d_A\\}$，公钥 $\\{P_A\\}$。

### 6.3 Sign 过程

- 计算 $Z_A = H(ENTL_A\\|ID_A\\|a\\|b\\|x_G\\|y_G\\|x_A\\|y_A)$。

- 计算 $\overline{M} = Z_A\\|M$。

- 计算 $e = H(\overline{M})$。

- 随机选择 $k\in \mathbb{Z}$，$1\leq k\leq n - 1$.

- 计算 $(x_1, y_1) = [k]G$。

- 计算 $r = (e + x_1)\bmod n$，若 $r = 0$ 或 $r + k = n$ 则重新选择。

- 计算 $s = [(1 + d_A)^{-1}\times (k - rd_A)]\bmod n$，若 $s = 0$ 则重新选择。

- 输出签名 $(r, s)$。

### 6.4 Vrfy 过程

- 检验 $r'$ 和 $s'$ 是否是 $[1, n - 1]$ 中的整数。

- 计算 $\overline{M}' = Z_A\\|M'$。

- 计算 $e' = H(\overline{M}')$。

- 计算 $t = r' + s'$，若  $t = 0$ 则不通过。

- 计算 $(x'_1 + y'_1) = [s']G + [t']P_A$。

- 计算 $R = (e' + x'_1)\bmod n$。

- 验证 $R \overset{\text{?}}= r'$。

## 7 BLS 签名

### 7.0 预备知识

- 双线性映射：对于阶数均为素数 $p$ 的加性群 $G_1$ 和 $G_2$，乘性群 $G_T$ ，$G_1$ 的生成元为 $P$，$G_2$ 的生成元为 $Q$。双线性映射 $e:G_1\times G_2\rightarrow G_T$ 满足以下性质：
  
  - 双线性性：$\forall a,b\in \mathbb{Z}:e(aP, bQ) = e(P,Q)^{ab}$；
  
  - 非退化性：$e(P,Q)\neq 1$；
  
  - 出于应用目的，$e$ 的计算应是可高效计算的。
  
  特别地，当 $G_1 = G_2 = G$ 时，称 $e$ 是对称的；当 $G$ 是循环群时，$e$ 是可交换的，即 $e(P,Q) = e(g^p, g^q) = e(g, g)^{pq} = e(g^q, g^p) = e(Q, P)$。

- Computational Diffie-Hellman Problem, CDHP：对于 $G$，给定 $g^a, g^b\in G$，计算 $g^{ab}$ 是困难的。

- Decisional Diffie-Hellman Problem, DDHP：对于 $G$，以下两个三元组的概率分布是计算不可区分的：
  
  - $(g^a, g^b, g^{ab})$，其中 $a,b$ 是从 $\mathbb{Z}_q$ 中独立且随机抽取的；
  
  - $(g^a, g^b, g^c)$，其中 $a,b,c$ 是从 $\mathbb{Z}_q$ 中独立且随机抽取的。

- Computational co-Diffie-Hellman, co-CDH：对于 $(G_1, G_2)$，给定 $g_2, g_2^a\in G_2$ 和 $h\in G_1$，计算 $h^a\in G_1$ 是困难的。

- Decisional co-Diffie-Hellman, co-DDH：对于 $(G_1, G_2)$，给定 $g_2, g_2^a\in G_2$ 和 $h, h^b\in G_1$，判断 $a\overset{\text{?}}= b$。如果 $a = b$，则称 $(g_2, g_2^a, h, h^a)$ 是 co-DDH 元组。

### 7.1 公共参数

- Gap co-Diffie-Human group pair $(G_1, G_2)$，$\|G_1\| = \|G_2\| = p$，co-CDH 计算困难，co-DDH 计算简单。

### 7.2 Gen 过程

- 随机选择 $x\in \mathbb{Z}_p$。

- 计算 $v = g_2^x\in G_2$。

- 私钥 $\\{x\\}$，公钥 $\\{v\\}$。

### 7.3 Sign 过程

- 计算 $h = H(M)\in G_1$。

- 计算 $\sigma = h^x\in G_1$。

- 输出签名 $\sigma$。

### 7.4 Vrfy 过程

- 验证 $(g_2, v, h, \sigma)$ 是否为 co-DDH 元组。
