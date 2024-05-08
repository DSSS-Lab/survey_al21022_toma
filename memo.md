1. 暗号化したまま計算を行いたい系の技術
    - fully homomorphic encryption <- 前回説明した
    - garbled circuits
    - functional encryption
    - attribute-based encryption
    => 全部 論理回路を計算するもの

2. 論理回路 <= ギャップ => チューリングマシン
    - 論理回路でifやforを実行するのは非効率

3. チューリングマシンをシミュレートするには
    - メモリ
    - 状態機械 (オートマトン)

4. 暗号化したままチューリングマシンをシミュレートする研究が色々ありそう
    - 1の技術を利用している
