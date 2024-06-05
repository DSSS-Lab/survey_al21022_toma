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

# 再利用可能なGarbled Circuit 3

---

# まとめ

### 前回
- Reusable Garbled Circuit
    - 実際に実装してみよう
    - 並列化できそう

### 実際に実装してみよう
→ FHEの計算が遅すぎて難しそう

--- 

## 一番難しいものはGarbled Circuitの計算をFHE上で行なうこと

$$
M(\text{key}, \text{value}) = 
\begin{cases} 
1 & \text{(Decryption successful)} \\
0 & \text{(Decryption failed)}
\end{cases}
$$

$$
    Evaluate(w_*, \gamma_*) = \sum_{k=0}^{3} M(w_0, \gamma_k) \times M(w_1, \gamma_k) \times \text{Dec}(w_0, \text{Dec}(w_1, \gamma_k))
$$

$M$、$Dec$をFHE上で再実装する必要がある

---

## Advanced Encryption Standard (AES)
- 共通鍵暗号方式の代表格
    - Rijndaelという暗号方式が元になっている
- 現在有効な攻撃方法は見つかっていない
- 今回はAES128を使う
    - 128ビットセキュリティ
    → $2^{128}$の探索が必要

---

- AES: ブロック暗号
    - 暗号できる平文の長さが固定
    → 可変長の平文を暗号化するためにモードを指定する
    - 例）ECBモード：平文を一定長のブロックに分割して、それぞれに暗号化を適応する

## Galois/Counter Mode (GCM) 
- パフォーマンスが良い
- 並列化もできる
- 認証タグを使って、データの改竄チェックができる
    → $M$が構築できる

---

# FHEライブラリ

- https://github.com/zama-ai/tfhe-rs
    - Rustの中では一番よくメンテされたライブラリ（私調べ）
- オリジナルのTFHE実装はC++っぽい
    - https://tfhe.github.io/tfhe/

---

- NIST（米国国立標準技術研究所）が公開している仕様を元に実装していく

- Advanced Encryption Standard (AES)
    - https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.197-upd1.pdf

- Galois/Counter Mode (GCM) 
    - https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38d.pdf

- 実装
    - https://github.com/whtsht/reusable-garbled-circuit
    - 完成はしてない

---

# FHE上で計算すると辛いこと

- テーブルキャッシュできない
    - 例）1bytesで表現される有限体の逆数を求める$b^{-1} = b^{254}$
    - 普通だったら事前計算して、256個のテーブルとしてもっておけば、毎回計算せずにすむ
        - https://github.com/kokke/tiny-AES-c/blob/master/aes.c
    - FHEだと毎回計算結果がかわるのでキャッシュできない
- 制御構文は工夫する必要がある
    - 例）if a == 0 { c } else { b }
     → (a == 0) * c + (1 - (a == 0)) * b
    - 分岐先をバラすわけにはいかないので、短絡評価などができない

---

## FHEのパフォーマンス

https://docs.zama.ai/tfhe-rs/get-started/benchmarks

<div class="columns">
<div>

### 1byte unsigned integer
| operation | time |
| --- | --- |
| +,- | 57.7 ms |
| eq, ne | 31.9 ms |
| × | 80.8 ms |
| % | 613 ms |

</div>
<div>

- とても$M$、$Dec$を計算できる性能はない
- 有限体の乗算1回に8秒かかる
    - 一回の暗号化に4000回必要と見積もると9時間かかる
- 最初にこれ見ればよかった
- どうしたものか……

</div>
</div>

---

## FHE使わずになんとかならないか

論文名：Reusable Garbled Turing Machines Without FHE
著者：Wang, Yongge; Malluhi, Qutaibah M
発表場所：Codes, Cryptology and Information Security
発表年：2019

本文より意訳して抜粋
> FHEより効率的かどうかは定かではないが、この論文の目的は他アプローチを提案することにある

そっか……

---

GGPinReal: LWEを用いたGarbled CircuitとTFHEによる検証可能論理回路秘匿演算基盤
著者：松岡航太郎 佐藤 高史
発表場所：第18回ICTイノベーション
発表年：2024
URL：https://ict-nw.i.kyoto-u.ac.jp/ict-innovation/18th/panel/2035/

LWE（演算回数が限られている）で$M$、$Dec$を構築することができるのだろうか🤔

---

## 違う方法を模索

- 異なる技術を使う
    - Probabilistic Proof Protocols
    - 秘密分散

- 計算の表現を限定する
    - 行列計算
    - 限定された計算モデルで利用例を考える
        - MapReduce
