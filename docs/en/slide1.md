---
marp: true
class: invert
---

# 興味のある研究分野

1. 検証可能計算
2. 秘密計算

---

# 1. 検証可能計算について

---

# 検証可能計算 背景

- 現状ではクライアントは第三者を信頼して計算をしてもらう
    - 本当に計算が正しく実行されたかどうかはわからない
- 例 機械学習の計算を依頼してデータを返してもらったとする
    - もしかしたらそのデータは機械学習のアルゴリズムを実行した結果ではなく疑似乱数生成器で作ったゴミみたいなデータかもしれない
- お互いに計算の正しさを担保できないのが問題

---

# 検証可能計算 研究内容

- 計算結果だけでなく、計算が正しく実行されたかどうかの証明を返してそれを検証したい
- Probabilistically Checkable Proofs (PCPs)が基礎になっている$^{[pep]}$

### 基本スキーム$^{[vcwr]}$

```
verifier                          prover
                =   circuit   =>
                =    input    =>
                                  computation based on input
               <=   output    = 
               <=   queries   => 
test
accept/reject
```

---

# 検証可能計算に必要な特性

- 計算が正しく行なわれた場合、クライアントはその結果を受け入れる (Completeness)
- 計算が間違っていた場合、クライアントはその結果を(高確率で)否定する (Soundness)
- 検証にかかる計算コストは、依頼した計算コストより少ない (Efficiency)
    - もし検証コストが計算コストより高い場合は自分で計算すればいいじゃんとなってしまう

---

# 1. 秘密計算について

---

# 秘密計算 背景

- サーバーはクライアントのデータを暗号化せずに保持していることがほとんどである
    - パスワードなどを除く
- サイバー攻撃などによって個人情報が盗まれる可能性がある
- サーバーの管理者ならば、多くの人の個人情報にアクセスできる
    - プライバシーとはなんなのか

---

# 秘密計算 研究内容

- 完全準同型暗号
    - 任意の回路をシミュレートできる $^{[he]}$
- Garbled CircuitベースのMPCプロトコル $^{[mpc]}$
    - こちらも任意の回路を計算するやつ

---

# 2つの分野に共通していえること

信頼できないクラウドなどの計算資源を利用して、信頼性の高いサービスやアプリケーションを実現する

---

# 2つの研究を組み合わせることができる 

- Garbage Circuitと完全準同型暗号の組み合わせ $^{[nvc]}$
- 検証可能計算とゼロ知識証明を組み合わせ $^{[pino]}$
    - 証明者の内部状態を隠した状態で、計算の妥当性を証明する

---

# 課題

- パフォーマンス
    - 証明、検証のオーバーヘッドが発生する
    - 暗号化されたまま計算するのも計算コストが高い
- 計算の表現力
    - 検証可能計算は表現が限られていることが多い
        例: 副作用なし、状態を持たない
- プライバシー
    - クライアント: 入力を秘密にしたい
    - サーバー: 内部状態を秘密にしたい

---

# 今後やっていきたいこと

- 具体的な手法を理解する
    - PCPsとかIPsとか良くわかっていない
    - 準同型暗号も良くわかっていない
- どのような特性を持ったシステムを作るのか考える
    - 速度重視にするのか機能性重視にするのか？
    - どのような命令セットで動作させる？

---

# 参考文献

[pep]: NYU and UT Austin. Pepper: toward practical verifiable computation. website. 2022. https://www.pepper-project.org

[vcwr]: PictureMichael Walfish, PictureAndrew J. Blumberg. Verifying computations without reexecuting them. Communications of the ACM. 2015. https://dl.acm.org/doi/10.1145/2641562

[pino]: Bryan Parno, Jon Howell, Craig Gentry, Mariana Raykova. Pinocchio: Nearly Practical Verifiable Computation. IEEE Symposium on Security and Privacy. 2013. https://ieeexplore.ieee.org/document/6547113

---

[nvc]: Rosario Gennaro, Craig Gentry, Bryan Parno. Non-Interactive Verifiable Computing: Outsourcing Computation to Untrusted Workers. Cryptology ePrint Archive, Paper 2009/547. 2009. https://eprint.iacr.org/2009/547

[he]: Zama. Homomorphic Encryption 101. website. 2021. https://www.zama.ai/post/homomorphic-encryption-101

[mpc]: mpcalliance. MPC wiki. website. 2020. https://wiki.mpcalliance.org/
