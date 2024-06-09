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

# 検証可能計算 1

---

検証可能計算を中心に研究する

なぜ？
- データの秘匿したまま計算は利用例が難しい
    - ユーザーが個々でデータを保持してる場合
    → 準同型暗号使うのが困難
    - DBとしてデータを大量に保持している場合
    → 個人が特定できるカラムだけ消せばいいじゃん

---

### 検証可能計算
- 「サーバーに計算させた値を検証したいですよね！？」といえば研究としての体裁は保てる
    - 現実的な計算速度ならば
- 完全準同型暗号よりはサーバーコストが少ないのでできることの幅が広がる（はず）
    - セットアップコストがめちゃくちゃ高いが

---

## ランダムネスを利用した検証の力
- Freivalds’ Algorithm
- N x Nの行列A、Bの積Cの検証を行う

---

## 現実的な計算速度か

- https://github.com/whtsht/pequin
    - https://github.com/pepper-project/pequin のフォーク
    - そのままじゃ動かなかったのでDockerfile弄った
    - 50x50の行列計算に検証一瞬、証明数秒くらい
    - コンパイルにやたらと時間がかかる 数

---

論文名：Verifying computations with state
著者：Benjamin Braun, Ariel J. Feldman, Zuocheng Ren, Srinath Setty, Andrew J. Blumberg, and Michael Walfish
発表場所：In Proceedings of the Twenty-Fourth ACM Symposium on Operating Systems Principles (SOSP '13)
発表年：2013年

---

- baselineが手元で計算した場合

![](../../img/Pantry_Performance.png)

---

## Multi-prover interactive proofs

- 複数の証明者はお互いに通信してはいけないという仮定がある
- この仮定を前提に効率的なシステムを開発したという研究
論文名：Verifiable computation using multiple provers
著者：Blumberg, Andrew Justin, Justin Thaler
発表場所：IACR Cryptology
発表年：2014年

---

## Multi-prover interactive proofs

- 複数の証明者はお互いに通信してはいけないという仮定がある
    - これはかなり強い仮定

---

## P2Pを利用する

- 検証者は自分が接続している任意のノードを複数選び、証明者とする
- Sybil攻撃の耐性があれば、証明者はお互いに通信するのは困難なのではないか
    - 勘で言っている

<br>

※ Sybil攻撃： IDを偽造して大量のノードが動いているかのように見せる

---

<div class="columns">
<div>

![](../../img/sybil.jpg)

</div>
<div>

## 安全じゃない例

- S1、S2、S3は攻撃者のノード
- H1、H2は健全なノード
- H1はS1とS2を証明者として選択
- S1とS2はお互い通信して不正をすることができる

</div>

</div>

---

<div class="columns">
<div>

![](../../img/sybil2.jpg)

</div>
<div>

## 安全な例

- S1、S2、S3は攻撃者のノード
- H1、H2は健全なノード
- H1はS1とH2を証明者として選択
- S1とH2はお互い通信できない
    - Multi-prover interactive proofsの仮定を満たしている

</div>

</div>

---

## Sybil攻撃に耐性を得るには

- Sybilネットワークを分断する

---

## 学部の研究でやること
- Multi-prover interactive proofsのシステムを作る

## 修士の研究でやること
- P2Pのネットワーク上で動かすシステムを作る
