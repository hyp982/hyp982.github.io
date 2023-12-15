---
layout:       post
title:        "密码学中的可证明安全性--基本工具和概念"
author:       "Luc"
header-style: text
hidden:       true
catalog:      true
mathjax:      true
tags:
    - 密码学
---

## 1 数学知识

### 1.1 代数系统/结构

1. 设 $<\mathbb{G}, *>$ 是一个代数系统，$*$ 满足：
    - 封闭性；
    - 结合律；
则称 $<\mathbb{G}, *>$ 是 **半群**。

2. 设 $<\mathbb{G}, *>$ 是一个代数系统，$*$ 满足：
    - 封闭性；
    - 结合律；
    - 存在元素 $e$，对于 $\forall a \in \mathbb{G}$，有 $a * e = e * a = a$，$e$ 称为 $<\mathbb{G}, *>$ 的单位元；
    - 对 $\forall a \in \mathbb{G}$，存在元素 $a^{-1}$，使得 $a * a^{-1} = a^{-1} * a = e$，称 $a^{-1}$ 为元素 $a$ 的逆元；
则称 $<\mathbb{G}, *>$ 是 **群**。