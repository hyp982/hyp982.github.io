---
layout:       post
title:        "安全规约--签名方案攻击方法总结"
author:       "Luc"
header-style: text
hidden:       false
catalog:      true
mathjax:      true
tags:
    - 密码学
    - 施工中
---

> 本文内容来源于郭福春老师的《Introduction to Security Reduction》和《卧村学报》第52期《Practice Makes Perfect: Attack Signature Schemes》。

## 1 Digital Signature

A digital signature scheme consists of the following four algorithms.

- **SysGen:** The system parameter generation algorithm takes as input a security parameter $ \lambda $. It returns the system parameters $ SP $.

- **KeyGen:** The key generation algorithm takes as input the system parameters $ SP $. It returns a public/secret key pair $ (pk, sk) $.

- **Sign:** The signing algorithm takes as input a message $ m $ from its message space, the secret key $ sk $, and the system parameters $ SP $. It returns a signature of $ m $ denoted by $ \sigma_m $.

- **Verify:** The verification algorithm takes as input a message-signature pair $ (m, \sigma_m) $, the public key $ pk $, and the system parameters $ SP $. It returns "accept" if $ \sigma_m $ is a valid signature of $ m $ signed with $ sk $; otherwise, it returns "reject".

A digital signature scheme satisfies the following two properties.

- **Correctness:** Given any $ (pk, sk, m, \sigma_m) $, if $ \sigma_m $ is a valid signature of $ m $ signed with $ sk $, the verification algorithm on $ (m, \sigma_m, pk) $ will return "accept".

- **Security:** Without the secret key $sk$, it is hard for any probabilistic polynomial-time (PPT) adversary to forge a valid signature $ \sigma_m $ on a new message $ m $ that can pass the signature verification.

## 2 EU-CMA

The security model of existential unforgeability against chosen-message attacks (EU-CMA) can be described as follows.

- **Setup:** Let $ SP $ be the system parameters. The challenger runs the key generation algorithm to generate a key pair $ (pk, sk) $ and sends $ pk $ to the adversary. The challenger keeps $ sk $ to respond to signature queries from the adversary.

- **Query:** The adversary makes signature queries on messages that are adaptively chosen by the adversary itself. For a signature query on the message $ m_i $, the challenger runs the signing algorithm to compute $ \sigma_{m_i} $ and then sends it to the adversary.

- **Forgery:** The adversary returns a forged signature $ \sigma_{m^ *} $ on some $ m^ * $ and wins the game if
  - $ \sigma_{m^ *} $ is a valid signature of the message $ m^ * $.
  - A signature of $ m^ * $ has not been queried inn the query phase.

The advantage $ \epsilon $ of winning the game is the probability of returning a valid forged signature.

- **Definition 1 (EU-CMA):** A signature scheme is $ (t, q_s, \epsilon) $-secure in the EU-CMA security model if there exists no adversary who can win the above game in time $ t $ with advantage $ \epsilon $ after it has made $ q_s $ signature queries.

- **Definition 2 (SU-CMA):** A signature scheme is $ (t, q_s, \epsilon) $-secure in the security model of strong unforgeability against chosen-message attacks (SU-CMA) if there exists no adversary who can win the above game in time $ t $ with advantage $ \epsilon $ after it has made $ q_s $ signature queries, where the forged signature can be on any message as long as it is different from all queried signatures.

## 3 Scheme-1

### 3.1 Description

Let $ (\mathbb{G}, g, p) $ be the cyclic group and $ H: \\{ 0, 1 \\}^* \rightarrow \mathbb{Z}_p $ be the cryptographic hash function that will be shared by all users.

- **KeyGen:** The key generation algorithm chooses a random number $ \alpha \in \mathbb{Z}_p $, computes $ g_1 = g^{\alpha} $, and returns a public/secret key pair $ (pk, sk) $ as follows: <br><center> $ pk = g_1, sk = \alpha $.

- **Sign:** The signing algorithm take as input a message $ m \in \\{ 0, 1 \\}^* $ and the secret key $ sk $. It computes the signature $ \sigma_m $ on $ m $ as <br><center> $ \sigma_m = \alpha + H(m) \bmod p $.

- **Verify:** The verification algorithm takes as input a message-signature pair $ (m, \sigma_m) $ and the public key $ pk $. It accepts the signature if <br><center> $ g^{\sigma_m} = g_1 \cdot g^{H(m)} $.

### 3.2 Attack Method

The adversary makes a signature query on message $ m_1 $ adaptively chosen by the adversary itself. For the signature query on the message $ m_1 $, the challenger runs the signing algorithm to compute $ \sigma_{m_1} = \alpha + H(m) $ and then sends it to the adversary.

Because the adversary holds both $ \sigma_{m_1} $ and $ m_1 $, it can compute $H(m_1)$ even $ \alpha $ with equation $ \alpha = \sigma_{m_1} - H(M) $.

Then the adversary forges the signature on message $ m^* $ by the following steps.
- Compute $ H(m^ *) $.
- Return the forged signature $ \sigma_{m^ *} = \alpha + H(m^ *) $.

### 3.3 Remediation Scenario

Let $ (\mathbb{G}, g, p) $ be the cyclic group and $ H: \\{ 0, 1 \\}^* \rightarrow \mathbb{Z}_p $ be the cryptographic hash function that will be shared by all users.

- **KeyGen:** The key generation algorithm chooses a random number $ \alpha \in \mathbb{Z}_p $, computes $ g_1 = g^{\alpha} $, and returns a public/secret key pair $ (pk, sk) $ as follows: <br><center> $ pk = g_1, sk = \alpha $.

- **Sign:** The signing algorithm take as input a message $ m \in \\{ 0, 1 \\}^* $ and the secret key $ sk $.
  - Choose a random $ r \in \mathbb{Z}_p $ and compute $ \sigma_2 = g^r $.
  - Compute $ \sigma_1 = \alpha \sigma_1 + H(m) \cdot r \bmod p$.
  - Return the signature $ \sigma_m = (\sigma_1, \sigma_2) $.

- **Verify:** The verification algorithm takes as input a message-signature pair $ (m, \sigma_m) $ and the public key $ pk $. It accepts the signature if <br><center> $ g^{\sigma_2} = g_1^{\sigma_1} \cdot \sigma_1^{H(m)} $.

There are 18 variant scenarios for Elgamal signature scheme summarized by Harn in *Design of Generalized ElGamal Type Digital Signature Schemes Based on Discrete Logarithm*. The above scheme is the instance No.3. Besides, the standard Elgamal signature scheme is the instance No.4. DSS is a transformation of standard ElGamal signature scheme introducing a modula parameter $ q $. Every variant scenario can be transformed into a DSA-type signature scheme.

Notations changed in the table below, where $ x $ is the user's private key, $ y $ is the user's public key, $ k $ is a random number, $ \alpha $ is an intrinsic element of modulo $ p $, $ m $ is the message to be signed, and $ r $ and $ s $ are the two components of the signature.

| No. | Sign($ \bmod p - 1 $) | Verify($ \bmod p $)       |
| --- | --------------------- | ------------------------- |
| 1   | $ mx = rk + s $       | $ y^m = r^r\alpha^s $     |
| 2   | $ mx = sk + r $       | $ y^m = r^s\alpha^r $     |
| 3   | $ rx = mk + s $       | $ y^r = r^m\alpha^s $     |
| 4   | $ rx = sk + m $       | $ y^r = r^s\alpha^m $     |
| 5   | $ sx = rk + m $       | $ y^s = r^r\alpha^m $     |
| 6   | $ sx = mk + r $       | $ y^s = r^m\alpha^r $     |
| 7   | $ rmx = k + s $       | $ y^{rm} = r\alpha^s $    |
| 8   | $ x = mrk + s $       | $ y = r^{rm}\alpha^s $    |
| 9   | $ sx = k + mr $       | $ y^s = r\alpha^{rm} $    |
| 10  | $ x = sk + rm $       | $ y^{rm} = r^s\alpha $    |
| 11  | $ rmx = sk + 1 $      | $ y^{rm} = r^s\alpha $    |
| 12  | $ sx = rmk + 1 $      | $ y^s = r^{rm}\alpha $    |
| 13  | $ (r + m)x = k + s $  | $ y^{r + m} = r\alpha^s $ |
| 14  | $ x = (m + r)k + s $  | $ y = r^{r + m}\alpha^s $ |
| 15  | $ sx = k + (m + r) $  | $ y^s = r\alpha^{m + r} $ |
| 16  | $ x = sk + (r + m) $  | $ y = r^s\alpha^{m + r} $ |
| 17  | $ (r + m)x = sk + 1 $ | $ y^{r + m} = r^s\alpha $ |
| 18  | $ sx = (r + m)k + 1 $ | $ y^s = r^{r + m}\alpha $ |

## 4 Scheme-2

### 4.1 Description

Let $ (\mathbb{G}, g, p) $ be the cyclic group and $ H: \\{ 0, 1 \\}^* \rightarrow \mathbb{Z}_p $ be the cryptographic hash function that will be shared by all users.

- **KeyGen:** The key generation algorithm chooses a random number $ \alpha \in \mathbb{Z}_p $, computes $ g_1 = g^{\alpha} $, and returns a public/secret key pair $ (pk, sk) $ as follows: <br><center> $ pk = g_1, sk = \alpha $.

- **Sign:** The signing algorithm take as input a message $ m \in \\{ 0, 1 \\}^* $ and the secret key $ sk $.
  - Choose a random $ r\in \mathbb{Z}_p $ and compute $ \sigma_1 = g^r $.
  - Compute $ \sigma_2 = r + \alpha H(m)\bmod p $.
  - Return the signature $ \sigma_m = (\sigma_1, \sigma_2) $.

- **Verify:** The verification algorithm takes as input a message-signature pair $ (m, \sigma_m) $ and the public key $ pk $. It accepts the signature if <br><center> $ g^{\sigma_2} = sigma_1\cdot g_1^{H(m)} $.

## 8 Scheme-6

### 8.1 Description

Let $ (\mathbb{G}, \mathbb{G}_T, g, e, p) $ be the pairing group and $ H: \\{ 0, 1 \\}^* \rightarrow \mathbb{Z}_p $ be the cryptographic hash function that will be shared by all users.

- **KeyGen:** The key generation algorithm chooses random numbers $ \alpha, \beta \in \mathbb{Z}_p $, computes $ g_1 = g^{\alpha}, g_2 = g^{\beta} $, and returns a public/secret key pair $ (pk, sk) $ as follows: <br><center> $ pk = (g_1, g_2), sk = (\alpha, \beta) $.

- **Sign:** The signing algorithm takes as input a message $ m \in \{ 0, 1\}^* $ and the secret key $ sk $.
  - Choose a random $ r \in \mathbb{Z}_p $ and compute $ \sigma_2 = g^r $.
  - Compute $ \sigma_1 = g^{\alpha \beta + H(m) \cdot r} $.
  - Return the signature $ \sigma_m = (\sigma_1, \sigma_2) $.

- **Verify:** The verification algorithm takes as input a message-signature pair $ (m, \sigma_m) $ and the public key $ pk $. It accepts the signature if <br><center> $ e(\sigma_1, g) = e(g_1, g_2)e(g^{H(m)}, \sigma_2) $.

### 8.2 Attack Method
The adversary makes a signature query on message $ m_1 $ adaptively chosen by the adversary itself. For the signature query on the message $ m_1 $, the challenger runs the signing algorithm to compute $ \sigma_{m_1} = (\sigma_{m_1}^{(1)} = g^{\alpha \beta + H(m_1) \cdot r_1}, \sigma_{m_1}^{(2)} = g^{r_1}) $ and then sends it to the adversary.

Because the equation $ \sigma_{m_1}^{(1)} = g^{\alpha \beta + H(m_1) \cdot r_1} = g^{\alpha \beta} \cdot (\sigma_{m_1}^{(2)})^{H(m_1)} $ holds, the adversary is able to compute $ g^{\alpha \beta} = \dfrac{\sigma_{m_1}^{(1)}}{(\sigma_{m_1}^{(2)})^{H(m_1)}} $.

Then the adversary forges the signature on message $ m^* $ by the following steps.
- Choose a random $ r^* \in \mathbb{Z}_p $ and compute $ \sigma_2^* = g^{r^*} $.
- Compute $ \sigma_1^ * = g^{\alpha \beta + H(m^ *) \cdot r^ *} = g^{\alpha \beta} \cdot (\sigma_2^ *)^{H(m^ *)}$.
- Return the forged signature $ \sigma_{m^ *} = (\sigma_1^ *, \sigma_2^ *) $.

### 8.3 Remediation Scenario

The $ \mathrm{BB}^{RO} $ digital signature scheme can be described as follows.

- **SysGen:** The system parameter generation algorithm takes as input a security parameter $ \lambda $. It chooses a pairing group $ \mathbb{PG} = (\mathbb{G}, \mathbb{G}_T, g, p, e) $, selects a cryptographic hash function $ H: \\{ 0, 1 \\}^* \rightarrow \mathbb{G} $, and returns the system parameters $ SP = (\mathbb{PG}, H) $.

- **KeyGen:** The key generation algorithm takes as input the system parameters $ SP $. It randomly chooses $ g_2 \in \mathbb{G}, \alpha \in \mathbb{Z}_p $, computes $ g_1 = g^{\alpha} $, and returns a public/secret key pair $ (pk, sk) $ as follows: <br><center> $ pk = (g_1, g_2), sk = \alpha $.

- **Sign:** The signing algorithm takes as input a message $ m \in \\{ 0, 1 \\}^* $, the secret key $ sk $, and the system parameters $ SP $. It chooses a random number $ r \in \mathbb{Z}_p $ and returns the signature $ \sigma_m $ on $ m $ as <br><center> $ \sigma_m = (\sigma_1, \sigma_2) = (g_2^{\alpha}H(m)^r, g^r) $.

- **Verify:** The verification algorithm takes as input a message-signature pair $ (m, \sigma_m) $, the public key $ pk $, and the system parameters $ SP $. Let $ \sigma_m = (\sigma_1, \sigma_2) $. It accepts the signature if <br><center> $ e(\sigma_1, g) = e(g_1, g_2)e(H(m), \sigma_2) $.

Suppose the hash function $ H $ is a random oracle. If the CDH problem is hard, the $ \mathrm{BB}^{RO} $ signature is provably secure in the EU-CMA security model with reduction loss $ L = q_H $, where $ q_H $ is the number of hash queries to the random oracle.