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

# 検証可能計算 2
# P2Pネットワーク1

---

# 検証可能計算
- Multi-prover interactive proofs system (MIPs)

---

## Interactive proof system (IPs)
\mathcal{}

$(V, x, r, P)$
$V$：検証者、$P$：証明者
$r$：検証者のランダムネス
$x$：入力
$out$：$V$が$P$を受理する場合は1、拒否する場合は0となる関数

Completeness
$$
    Pr[out(V, x, r, P) = 1] > 2 / 3
$$

Soundness
$$
    Pr[out(V, x, r, P') = 1] \leq 1 / 3
$$


---

## なんで閾値1/3？

- ぶっちゃけ1/2未満だったらなんでもいいらしい

[気持ち]
- どのくらいの確率で正しい値を返してほしいのかはタスクによる

[理論]
- 複数回実行して多数決をとれば、高確率で正しい値を返すアルゴリズムが構成できる
    → 複数回実行というのは計算複雑性理論では無視できる

※ 厳密な議論ではない

---

## Interactive Proofの例

- 離散対数問題のある解を知っていることの証明
    - $(p,g,y)$が与えられた時 $y \equiv g^x \pmod p$となる$x$を知っている

<br>

1. $P$は$a \leftarrow g^r \pmod p$を計算して$V$に渡す
2. $V$は$b \leftarrow \{0, 1\}$をランダムに選び、$P$に渡す
3. $P$は$c \leftarrow r+bx \pmod q$を計算して、$V$に渡す
4. $V$は$g^c \overset{?}{\equiv} ay^b \pmod p$を確認して、異る場合は$P$を拒否する
5. 1～4を$V$が納得するまで繰り返す

---

## Completenessの証明

<div class="columns">
<div>

### b = 1の場合

$g^c = g^{(r+x)} = g^rg^x = g^ry$

$ay^b = g^ry$

よって
$g^c \equiv ay^b \pmod p$

</div>
<div>

### b = 0の場合

$g^c = g^r = a$
$ay^b = a$

よって
$g^c \equiv ay^b \pmod p$

</div>
</div>

---

## Soundnessの考察

$P$はbをランダムに選ぶことで成功率$\frac{1}{2}$の攻撃ができる
1. $b$、$c$をランダムに選ぶ
2. $a \leftarrow g^cy^{-b} \pmod p$を計算してプロトコル実行

$b$があっているとすると $ay^b = g^cy^{-b}y^b = g^c$となるのでテストを通過できる

<br>

1回繰り返すと$P$が$V$を騙せる確率は$\frac{1}{2}^{1} = 50\%$
20回繰り返すと$P$が$V$を騙せる確率は$\frac{1}{2}^{20} = 0.0001\%$

#### 十分な回数繰り返せば、不正な$P$が受理される確率は指数関数的に減少する

---

## 今なにやってんの？
- GKR Protocolの理解、実装
    - 証明者は1人 Uni-prover interactive proofs （UIPs）
    - 任意の算術回路を計算可能
    - 効率
![](../../img/gkp_protocol.png)

※  Uni-prover interactive proofsは一般的な呼びかたではないが、MIPsと対比してそう呼ぶことにする

---

# P2Pネットワーク

- 勉強不足のため、不確定な要素が多いです

---

## P2Pネットワークの分類

#### 構造化ネットワーク
- 接続のルールを固定することでネットワークの構造をある程度限定するもの
- DHT （分散ハッシュテーブル）
    - ハッシュ値が隣合うものとN個離れているものは接続を確保するなどのルールがある
- Hybrid P2P
    - 接続を管理する中央サーバーが存在する

#### 非構造化ネットワーク
- 接続のルールが固定されておらず、ネットワークの構造を予測しにくい
- ゴシップ（噂話し）プロトコル
    - 接続されているピアにマルチキャストして情報を伝達する

---

プライバシー
実効速度
セキュリティ


multi proverの過程を満たしている
ピアの接続スロットを独占されない

ピアの接続スロットの種類
インバウンドピアスロット
アウトバウンドピアスロット

ピアの接続スロットを占拠する攻撃
エクリプス攻撃

インバウンドピアを埋め尽くす攻撃
アウトバウンドピアを埋め尽くす攻撃

---

## P2Pネットワークに参加する

#### ブートストラップノードに接続する
- アプリにハードコードされた信頼されたサーバー（ブートストラップノード）にアクセスする
- 信頼されたサーバーの管理は、コミュニティによるものや第三者機関にまかせるなど自由

#### 信頼できるピアからの紹介
- 例) 信頼できる人からピアのIPアドレスを教えてもらい、ネットワークに参加する

---

## IDの偽造

- オーバレイネットワークの場合
    - 1つのコンピュータで大量にアプリ起動すれば偽装できそう

- IP address spoofing
    - トランスポート層

---

- 本研究で対策する攻撃はSybil攻撃ではなくEclipse（覆い隠す）攻撃になる
- Sybil攻撃とEclipse攻撃の共通点
    - IDを偽造して1つのコンピュータで大量のノードを動かしているように見せる

- 相違点
    - Sybil攻撃：ノード全体における仮定を壊す
        - 例) ノードの2/3はhonestなノードである
    - Eclipse攻撃：あるノードを決め打ちして通信経路を断ち、不正を行う

※ IDEのEclipseは関係ないよ！

---

論文名：Eclipse Attacks on Bitcoin’s Peer-to-Peer Network
著者：Ethan Heilman and Alison Kendler
発表場所：USENIX Security Symposium
発表年：2015年

- シミュレーションをやっている

新しいノードの受け入れやすさとEclipse攻撃の耐性にはトレードオフの関係がある
