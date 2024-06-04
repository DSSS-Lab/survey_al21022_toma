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

- Garbled Circuitでは、どう頑張っても <span style="color: #87CEFA;"> 初期コスト > 計算コスト </span> となる

- N回計算して 
    <span style="color: #87CEFA;"> 初期コスト + N × 検証コスト < N × 計算コスト </span>
    とするしかない（計算コストを償却する）

- 目標
    - 計算コストを償却する
    - 入出力の暗号化
    - 計算結果の検証

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

<div class="columns">
<div>

- 回路生成

| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{0}, C_{\lambda}^{0}))$ |
|---|
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{1}, C_{\lambda}^{1}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{1}, C_{\lambda}^{3}))$ |

</div>
<div>

- 回路計算

入力ラベル $A_{\lambda}^{1}$、$B_{\lambda}^{0}$とすると、復号することで出力ラベルである$C_{\lambda}^{2}$が得られる

$$
\begin{align*}
    &Dec(B_{\lambda}^{0}, Dec(A_{\lambda}^{1}, Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))) \\
        &= C_{\lambda}^{2}
\end{align*}
$$

</div>
</div>

※  FHE上の暗号とGarbled Circuitの暗号は違うので注意
    - FHE：$Encrypt_{FHE}$、$Decrypt_{FHE}$ （公開鍵暗号方式）
    - GC ：$Enc$、$Dec$ （共有鍵暗号方式）

---

# Efficient Verifiable-Computation Scheme

---


1. [検証者] Garbled Circuitの生成と入力ラベルの準備

例）入力ラベル：$A_{\lambda}^{1}$、$B_{\lambda}^{0}$
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{0}, C_{\lambda}^{0}))$ |
|---|
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{1}, C_{\lambda}^{1}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2}))$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{1}, C_{\lambda}^{3}))$ |

<br>

2. [検証者] FHE公開鍵、秘密鍵の準備
$$
    KeyGen_{FHE}(\lambda) = (SK, PK)
$$

---

3. [検証者] 計算者にGarbled Circuitと、<span style="color: #ff4444">入力ラベルをFHE公開鍵で暗号化</span>したものを渡す

例）Garbled Circuit

| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{0}, C_{\lambda}^{0})) = \gamma_0$ |
|---|
| $Enc(A_{\lambda}^{0}, Enc(B_{\lambda}^{1}, C_{\lambda}^{1})) = \gamma_1$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{0}, C_{\lambda}^{2})) = \gamma_2$ |
| $Enc(A_{\lambda}^{1}, Enc(B_{\lambda}^{1}, C_{\lambda}^{3})) = \gamma_3$ |

入力：$Encrypt_{FHE}(PK, A_{\lambda}^{1})$、$Encrypt_{FHE}(PK, B_{\lambda}^{0})$を計算者に渡す


---

4. [計算者] FHE上でGarbled Circuitを計算する
4.1. Garbled CircuitをさらにFHEの公開鍵で暗号化する

| $Encrypt_{FHE}(PK, \gamma_0)$ |
|---|
| $Encrypt_{FHE}(PK, \gamma_1)$ |
| $Encrypt_{FHE}(PK, \gamma_2)$ |
| $Encrypt_{FHE}(PK, \gamma_3)$ |

---

4. [計算者] FHE上でGarbled Circuitを計算する
4.2. 検証者からもらった入力をもとにFHE上で復号化する

$$
\begin{align*}

& Evaluate(Encrypt_{FHE}(PK, B_{\lambda}^{0}), Encrypt_{FHE}(PK, A_{\lambda}^{1}), Encrypt_{FHE}(PK, \gamma_?)) \\
    &= Encrypt_{FHE}(PK, C_{\lambda}^{2})
\end{align*}
$$

<br>

<span style="color: #ff4444">この段階で$Encrypt_{FHE}(PK, \gamma_2)$を使うということがバレてはいけない</span>
→ こんなことはできるのか

→ できます！ $Evaluate$については後ほど

---

5 [計算者] 計算結果を検証者に返す
$$
    Encrypt_{FHE}(PK, C_{\lambda}^{2})
$$

6 [検証者] 計算結果の検証を行う

$$
    Decrypt_{FHE}(SK, Encrypt_{FHE}(PK, C_{\lambda}^{2})) = C_{\lambda}^{2}
$$

復号化できれば、検証成功。復号化に失敗したら検証失敗であり、無効な出力。

---

## どうやって出力ラベルがバレずに、FHE上で復号化するのか？

Garbled Circuitの暗号プロトコルが以下の関数$M$が構成できるとする

$$
M(\text{key}, \text{value}) = 
\begin{cases} 
1 & \text{(Decryption successful)} \\
0 & \text{(Decryption failed)}
\end{cases}
$$

例）[Galois/Counter_Mode](https://en.wikipedia.org/wiki/Galois/Counter_Mode)
- 平文以外に検証用のタグを仕込む

---

- FHEで暗号化した入力：$w_0$、$w_1$
- FHEで暗号化したGarbled Circuitの各行：$\gamma_0$、$\gamma_1$、$\gamma_2$、$\gamma_3$、

例)  
$Decrypt_{FHE}(SK, M(w_0, \gamma_2)) = 1$であり、$Decrypt_{FHE}(SK, M(w_0, \gamma_{0,1,3})) = 0$となることは評価者はわかるが計算者はわからない。

※ $M(w_0, \gamma_*)$はFHE上で計算しているので0、1にならない

---

[計算者] 手順4で以下を計算して返す

$$
    Evaluate(w_*, \gamma_*) = \sum_{k=0}^{3} M(w_0, \gamma_k) \times M(w_1, \gamma_k) \times \text{Dec}(w_0, \text{Dec}(w_1, \gamma_k))
$$


[検証者] $Decrypt_{FHE}$で復号化したときに$M(w_0, \gamma_{0,1,3})$の項は消える

$$
\begin{align*}
    & Decrypt_{FHE}(Evaluate(w_*, \gamma_*)) \\
    & = Decrypt_{FHE}(SK, M(w_0, \gamma_2)) \times Decrypt_{FHE}(SK, M(w_1, \gamma_2)) \times Dec(w_0, Dec(w_1, \gamma_2)) \\
    & = 1 \times 1 \times Decrypt_{FHE}(SK, Encrypt_{FHE}(PK, C_{\lambda}^2)) \\
    & = 1 \times 1 \times C_{\lambda}^{2} \\
    & = C_{\lambda}^{2} \\
\end{align*}
$$

---

- 計算者はFHEで暗号化したGarbled Circuitの各行：$\gamma_0$、$\gamma_1$、$\gamma_2$、$\gamma_3$のどれを使って計算したのか判断できない！

<br>

- Garbled Circuitの復号、総和、$M$の計算すべてFHE上で行う
→ めちゃめちゃ効率が悪い💢
→ サーバーが計算するんだから多少効率悪くても良くね？という考え方もある

---

- 2の手順（FHEの鍵生成）からやれば良いので、計算の償却ができる
    - 検証コスト(m = 出力のサイズ、$\lambda$ = セキュリティパラメータ) $O(m·poly(\lambda))$
    - Garbled Circuit生成コスト $O(|C|·poly(\lambda))$

- この論文での実装はない → 愚直に実装しても効率わるいということだろう

---


## アイディア

- Garbled Circuitを生成するGarbled Circuitは作れるか？
    - 課題
        - 入力の回路の大きさが異なる場合もあるので難しそう
        - そもそも安全に生成できる？

## 今後の見通し

- 入力サイズに依存しないGarbled Circuit構成方はあるか？
- Reusable Garbled Circuitを最適化する方法の模索
    - 別の構成方法も調べる
