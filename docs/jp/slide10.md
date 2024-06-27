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

# 計算量 / プログラムの表現

---

# 計算量について

- GKRプロトコルなどは当然計算量の話は必要
- 並列化については不要

---
- poly(N)
    - $N^2$、$N^3$、...、$N^k$はみんなこれに分類される
- polylog(N)
    - $log(N)^2$、$log(N)^3$、...、$log(N)^k$はみんなこれに分類される
    log2(N)^2

---

# polylog(N) vs N

log(N)^kのkがどんなに大きくてもNには及ばない

https://www.momoyama-usagi.com/entry/math-limit-order

---

## 以上を踏まえてGKRプロトコルの計算オーダーを見てみよう

### 手元で計算した場合
$$
O(S)
$$

### 検証コスト
$$
O(n + d \times polylog(S)) 
$$

---

### 証明コスト naive実装
$$
O(poly(S))
$$

### 証明コスト 工夫した実装
論文名：Practical Verified Computation with Streaming Interactive Proofs (2012)

$$
O(Slog(S))
$$

### 証明コスト もっと工夫した実装
論文名：Libra: Succinct Zero-Knowledge Proofs with Optimal Prover Computation

$$
O(S)
$$

---

# プログラムの表現について

---

## プログラムを回路に変換
- どうやってやる？
- 

## CPU-like transformations

## ASIC-like transformations

---

- ASIC-like transformationsを使いたい
- 並列化もしたい

<br>

どうする

---

## MapReduceモデル

- Googleによって開発されたプログラミングモデル
- 関数型言語のmap関数とreduce関数が元になっている
- 非常に分散化しやすいモデル

### Mapタスク
- input => list(key, value)
- inputはわりと何でもよい（ファイルでも良いし、DBでもよい）

### Shuffleタスク
- list(key, value) -> list(key, value)
- Mapタスクの出力をkeyごとにまとめる
- ユーザーはここを制御することは（たぶん）ない

### Reduceタスク
- list(key, value) => (key, value)
- Mapタスクで得た情報を集計するイメージ


入力 -> [Map] -> 中間値 -> [Shuffle] -> 中間値 -> [Reduce] -> 結果

---

## MapReduceモデルの例）文字数カウント

Input
```
hello world, goodbey world
```

Mapタスク：単語を抜き出して(word, 1)を出力

Map task output
```
(hello, 1)
(world, 1)
(goodbey, 1)
(world, 1)
```

Shuffleタスク：ワードごとにまとめる

Shuffle task output
```
(hello, 1)
(world, 1)
(world, 1)
(goodbey, 1)
```

Reduceタスク：同じ単語を合計する

Reduce task output
```
(hello, 1)
(world, 2)
(goodbey, 1)
```

---

## MapReduceモデルと組み合せると良さそう

- 各タスクは状態を持たない
    - ASIC-like transformationsが容易に適応できる
- 並列化しやすい
    - 複数の証明者が使えそう
