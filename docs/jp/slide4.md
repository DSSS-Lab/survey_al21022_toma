---
marp: true
class: invert
style: |
  .columns {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 0.5rem;
  }
---

# 再利用可能なGarbled Circuit

---

## Garbled Circuit（以降GCと表記）の生成 （簡易版）


1. 生成者は計算したいBoolean回路（以降回路と表記）を準備する

- 例としてAND回路を準備する

| 0 | 0 | 0 |
|---|---|---|
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

---

## GCの構成方法

2. 入出力をランダムなラベル(セキュリティパラメータ$\lambda$)で置き換える

| $A_{\lambda}^{0}$ | $B_{\lambda}^{0}$ | $C_{\lambda}^{0}$ |
|---|---|---|
| $A_{\lambda}^{0}$ | $B_{\lambda}^{1}$ | $C_{\lambda}^{1}$ |
| $A_{\lambda}^{1}$ | $B_{\lambda}^{0}$ | $C_{\lambda}^{2}$ |
| $A_{\lambda}^{1}$ | $B_{\lambda}^{1}$ | $C_{\lambda}^{3}$ |

$A_{\lambda}^{{0, 1}}$、$B_{\lambda}^{{0, 1}}$：入力ラベル

$C_{\lambda}^{{0, 1, 2, 3}}$：出力ラベル

---

## GCの構成方法

3. ラベルを秘密鍵として共有鍵暗号方式で出力ラベルを暗号化する

- 共有鍵暗号
    - 暗号化 $Enc(key, plaintext) = ciphertext$
    - 復号化 $Dec(key, ciphertext) = plaintext$

| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{0}, C_{\lambda}^{0}))$ |
|---|
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{1}, C_{\lambda}^{1}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{1}, C_{\lambda}^{3}))$ |

---

## GCの計算方法

1. 計算者にGCと計算したい入力の入力ラベルを渡す

例）入力ラベル $A_{\lambda}^{1}$、$B_{\lambda}^{0}$と以下のGCを渡す

| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{0}, C_{\lambda}^{0}))$ |
|---|
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{1}, C_{\lambda}^{1}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{1}, C_{\lambda}^{3}))$ |

---

2. 計算者は渡された入力を使い、GCの1行を復号する
- 例) 復号することで、出力ラベルである$C_{\lambda}^{2}$が得られる

$$
    Dec(B_{\lambda}^{0}, Dec(A_{\lambda}^{1}, Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))) \\
        = C_{\lambda}^{2}
$$

| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{0}, C_{\lambda}^{0}))$ |
|---|
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{1}, C_{\lambda}^{1}))$ |
| <span style="color: blue;">$Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))$</span> |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{1}, C_{\lambda}^{3}))$ |

---

3. 計算者は復号できた出力ラベルを生成者に返す
- 例）$C_{\lambda}^{2}$を返す
4. 生成者は元の回路とGCを比較して出力を取得する
- 例）$C_{\lambda}^{2}$は0と対応しているので答えは0であることがわかる

<div class="columns">
<div>

| 0 | 0 | 0 |
|---|---|---|
| 0 | 1 | 0 |
| <span style="color: blue;">1</span> | <span style="color: blue;">0</span> | <span style="color: blue;">0</span> |
| 1 | 1 | 1 |

</div>
<div>

| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{0}, C_{\lambda}^{0}))$ |
|---|
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{1}, C_{\lambda}^{1}))$ |
| <span style="color: blue;">$Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))$</span> |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{1}, C_{\lambda}^{3}))$ |


</div>
</div>

---

# GCの特性
- 出力の検証ができる
    - 出力ラベル以外の値が返ってきたら、間違った計算をしていることがわかる
- 計算者は計算している回路、入力に関する情報を得ることができない
    - 計算者にとっては入力ラベルも出力ラベルもランダムな値にしか見えない

---

# GCは再利用ができない

- 回路に関する情報が漏れる
- 出力の検証ができなくなる

---

## 回路に関する情報が漏れる
- GCが異なる4入力に対して出力ラベル$C_{\lambda}^{a}$を3回、出力ラベル$C_{\lambda}^{b}$を1回出力した
- 少くともXOR回路でないことがわかる

| $A_{\lambda}^{0}$ | $B_{\lambda}^{0}$ | $C_{\lambda}^{a}$ |
|---|---|---|
| $A_{\lambda}^{0}$ | $B_{\lambda}^{1}$ | $C_{\lambda}^{a}$ |
| $A_{\lambda}^{1}$ | $B_{\lambda}^{0}$ | $C_{\lambda}^{a}$ |
| $A_{\lambda}^{1}$ | $B_{\lambda}^{1}$ | $C_{\lambda}^{b}$ |

---

## 出力の検証ができなくなる
- 前回出力したラベルをそのまま返されると、正しい出力と区別ができない

---

## GCを再利用しても安全性を保証する研究
- Non-Interactive Verifiable Computing: Outsourcing Computation to Untrusted Workers
- Verifiable Outsourcing of Computations Using Garbled Onions
- CRGC A Practical Framework for Constructing Reusable Garbled Circuits

---

## Non-Interactive Verifiable Computing: Outsourcing Computation to Untrusted Workers

- 完全準同型暗号（FHE）を利用する
- 良い点：計算コストを償却できる
- 微妙な点：効率はFHEに依存する
- Verifiable Computing（検証可能計算）という言葉を初めて使った
- 回路生成を行うConstructor、計算を行うEvaluater、計算を検証するVerifierの3パーティを想定
    - 計算を検証するVerifierは計算資源が少ないという想定

---

## Verifiable Outsourcing of Computations Using Garbled Onions

- Garbled Onionsというプロトコルを導入している
- 良い点：複雑な暗号方式を利用していない
- 微妙な点：計算コストを償却できない
    - 委託者の通信量、使用メモリを減らすことも目的としている
- 実装：https://gitlab.utu.fi/tcmdon/Fairenough
- 回路生成を行うConstructor、計算を行うEvaluater、計算を検証するVerifierの3パーティを想定
    - 計算を検証するVerifierは計算資源が少ないという想定

--- 

## CRGC A Practical Framework for Constructing Reusable Garbled Circuits

- ビット反転などを駆使して回路を難読化する
- 良い点：暗号化を伴わないので、回路の計算効率が非常に良い
- 微妙な点：保証するのはプライバシーだけで、計算の検証はできない
- 計算を行うEvaluater、回路生成を行う2パーティを想定、Nパーティに拡張可能

---

# 参考文献

https://wiki.mpcalliance.org/garbled_circuit.html

