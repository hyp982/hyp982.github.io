---
layout:       post
title:        "安全规约--签名方案攻击方法总结"
author:       "Luc"
header-style: text
hidden:       true
catalog:      true
mathjax:      true
tags:
    - 密码学
---

## 1 Digital Signature

A digital signature scheme consists of the following four algorithms.

> **SysGen:** The system parameter generation algorithm takes as input a security parameter $ \lambda $. It returns the system parameters $ SP $.

> **KeyGen:** The key generation algorithm takes as input the system parameters $ SP $. It returns a public/secret key pair $ (pk, sk) $.

>**Sign:** The signing algorithm takes as input a message $ m $ from its message space, the secret key $ sk $, and the system parameters $ SP $. It returns a signature of $ m $ denoted by $ \sigma_m $.

>**Verify:** The verification algorithm takes as input a message-signature pair $ (m, \sigma_m) $, the public key $ pk $, and the system parameters $ SP $. It returns "accept" if $ \sigma_m $ is a valid signature of $ m $ signed with $ sk $; otherwise, it returns "reject".

A digital signature scheme satisfies the following two properties.

> **Correctness:** Given any $ (pk, sk, m, \sigma_m) $, if $ \sigma_m $ is a valid signature of $ m $ signed with $ sk $, the verification algorithm on $ (m, \sigma_m, pk) $ will return "accept".

> **Security:** Without the secret key $sk$, it is hard for any probabilistic polynomial-time (PPT) adversary to forge a valid signature $ \sigma_m $ on a new message $ m $ that can pass the signature verification.

## 2 EU-CMA

The security model of existential unforgeability against chosen-message attacks (EU-CMA) can be described as follows.

> **Setup:** Let $ SP $ be the system parameters. The 

## 8 Scheme-6

### 8.1 Description

Let $ (\mathbb{G}, \mathbb{G}_T, g, e, p) $ be the pairing group and $ H: \{ 0, 1 \}^* \rightarrow \mathbb{Z}_p $ be the cryptographic hash function that will be shared by all users.

> **KeyGen:** The key generation algorithm chooses random numbers $ \alpha, \beta \in \mathbb{Z}_p $, computes $ g_1 = g^{\alpha}, g_2 = g^{\beta} $, and returns a public/secret key pair $ (pk, sk) $ as follows: $$ pk = (g_1, g_2), sk = (\alpha, \beta). $$

> **Sign:** The signing algorithm takes as input a message $ m \in \{ 0, 1\}^* $ and the secret key $ sk $.
> - Choose a random $ r \in \mathbb{Z}_p $ and compute $ \sigma_2 = g^r $.
> - Compute $ \sigma_1 = g^{\alpha \beta + H(m) \cdot r} $.
> - Return the signature $ \sigma_m = (\sigma_1, \sigma_2) $.

> **Verify:** The verification algorithm takes as input a message-signature pair $ (m, \sigma_m) $ and the public key $ pk $. It accepts the signature if $$ e(\sigma_1, g) = e(g_1, g_2)e(g^{H(m), \sigma_2}). $$

### 8.2 Attack Method
