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

# 再利用可能なGarbled Circuit 2

---

### 前回：Garbled Circuit生成時に入力と出力の組み合わせわかるよね？
→ わかります😭

- どう頑張っても <span style="color: #87CEFA;"> 初期コスト > 計算コスト </span> となる

- N回計算して 
    <span style="color: #87CEFA;"> 初期コスト + N × 検証コスト < N × 計算コスト </span>
    とするしかない（計算コストを償却する）


---

## 計算コストを償却はどうやる？

### タイトル： Non-interactive Verifiable Computing: Outsourcing Computation to Untrusted Workers

### 著者：Gennaro, R., Gentry, C., Parno, B.

### 書誌情報：Advances in Cryptology -- CRYPTO 2010
- 30th Annual Cryptology Conference, Santa Barbara, CA, USA, August 15-19, 2010, Proceedings

※[検証者]と[計算者]がいるとする

---

## おさらい1：完全準同型暗号

$\lambda$: セキュリティパラメータ、$SK$: 秘密鍵、$PK$: 公開鍵

1. 鍵生成
$$
    KeyGen_{FHE}(\lambda) = (SK, PK)
$$
2. 暗号化
$$
    Encrypt_{FHE}(PK, plaintext) = ciphertext
$$
3. 復号化
$$
    Decrypt_{FHE}(SK, ciphertext) = plaintext
$$
4. 演算
$$
    Decrypt_{FHE}(Encrypt_{FHE}(A) * Encrypt_{FHE}(B)) = A * B
$$

- ポイント：公開鍵暗号である → 暗号化は計算者でもできる

---

## おさらい2：Garbled Circuit

1. 回路作成、入力の暗号化

$$
    GCGen(input, C) = (input', C')
$$

2. GCの評価
$$
    Evaluate(input', C') = output'
$$

3. 出力の復号化
$$
    Decord(output', C) = output
$$

---

# Efficient Verifiable-Computation Scheme

---


1. [検証者] Garbled Circuitと入力ラベルの生成 $GCGen$

例）入力ラベル：$A_{\lambda}^{1}$、$B_{\lambda}^{0}$
GC：
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{0}, C_{\lambda}^{0}))$ |
|---|
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{1}, C_{\lambda}^{1}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{1}, C_{\lambda}^{3}))$ |


---

2. [検証者] FHE公開鍵、秘密鍵の準備
$$
    KeyGen_{FHE}(\lambda) = (SK, PK)
$$

---

3. [検証者] 計算者にGarbled Circuitと、<span style="color: #ff4444">入力ラベルをFHE公開鍵で暗号化</span>したものを渡す

例）GC

| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{0}, C_{\lambda}^{0}))$ |
|---|
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{1}, C_{\lambda}^{1}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{1}, C_{\lambda}^{3}))$ |

と

$Encrypt_{FHE}(PK, A_{\lambda}^{1})$、$Encrypt_{FHE}(PK, B_{\lambda}^{0})$を計算者に渡す


---

4. [計算者] FHE上でGCを計算する
4.1.

---

4.2.

---

5 [計算者] 計算結果を検証者に返す

6 [検証者] 計算結果の検証を行う

$$
    Decrypt_{FHE}(SK, Encrypt_{FHE}(PK, C_{\lambda}^{2})) = C_{\lambda}^{2}
$$
が計算できれば、検証成功。できなければ検証失敗であり、無効な出力。

---

- Garbled Circuitを使い回すことができる
    - 入出力のラベルがバレてない
    - 2の手順からやれば良い
- この論文での実装はない

---


## アイディア

- Garbled Circuitを生成するGarbled Circuitは作れるか？
    - 課題
        - 入力の回路の大きさが異なる場合もあるので難しそう

## 今後の見通し

- 入力サイズに依存しないGarbled Circuit構成方はあるか？
- Reusable Garbled Circuitを最適化する別の方法の模索

---
