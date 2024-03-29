# ゲームで学ぶ 探索アルゴリズム実践入門 ～木探索とメタヒューリスティクス
青木 栄太. 株式会社技術評論社, 2023.03.25.

Finish reading at: 2023.10.03

###### Purpose
強化学習を学ぶ前に基本的な探索について学んでおくことで，適切な技術を使えるようになりたい．

---
## 第1章
二人零和有限確定完全情報ゲーム
- 零和: プレイヤー間の利害が完全に対立する．一方のプレイヤーが得をすると，他方のプレイヤーは同量の損をする．
  - 将棋は零和じゃないという意見
- 有限: ゲームが必ず有限の手番で終わる
  - 千日手など厳密には有限じゃない場合も

「文脈がある」: プレイヤーの行動と共に状況が変化する

ゲームの種別とおすすめのアルゴリズム
- プレイヤー人数: 一人
  - 文脈がある
    - 多様化に自信あり -> ビームサーチ
    - 多様化に自信なし -> Chokudaiサーチ
  - 文脈がない -> 焼きなまし法
- プレイヤー人数: 二人
  - 手番: 交互
    - 全探索可能 -> AplhaBeta
    - 全探索不可能
      - 盤面評価可能 -> AlphaBeta, Thunderサーチ
      - 盤面評価不可能 -> MCTS
  - 手番: 同時 -> DUCT

## 第3章
貪欲法
- 1ターン後の盤面で一番評価が高い盤面に進める手を選択する

ビームサーチ
- 複数ターンの探索をする際に，範囲を絞って現実的な計算量で探索をする
- 手順
  - 1ターン後の盤面をすべて列挙 & スコア順にソート
  - 深さ(ターン数)と幅(盤面の数)を設定
  - スコアの高い順から幅の数だけ，次の盤面を列挙してソート
    - このとき，同じターンの中でソートする
    - スコアの合計でソートする
  - 深さの分だけ繰り返す
- $盤面列挙の数の最大 = 親ノードの数 \times 1盤面当たりの最大合法手数$
- 計算時間によって深さを固定する(計算時間を固定する)
- 深さだけあっても，多様性がない
  - 同じ場面から遷移したものがソートで上位に

Chokudaiサーチ
- 幅の狭いビームサーチを何度も繰り返して多様性を確保
  - ビームのたびに，探索された盤面を追加し，すでに選択された盤面を除いてソートし深く探索する
- 一定時間内にある程度の結果が得られる
- 多様性のために期待度の低い探索を多く行う
- 極限までチューニングされたビームサーチのほうが高性能

## 第4章
文脈がない: ゲーム中に1度しかプレイヤーが介入せず，順序性がない特性を示す

オート数字集め迷路
- 数字がランダムに配置されたグリッド
- プレイヤーはエージェントの配置のみを行う
  - その際の数字はスコアに加算されない
- エージェントは4方向で一番数字の高いグリッドに移動し，その数字だけスコアを加算する
  - 同じグリッドに移動した場合は，一回のみ加算される
- 全てのエージェントが獲得したスコアの合計を最大化する

山登り法
- 局所探索法
- 近接最適性(良い解同士は似ている)があるという前提のもと，似た構造を中心に探索する手法(集中化)
- 解をランダムな近傍(少し構造を変えた状態)に遷移させ，条件でその遷移を保たせたり，元の状態に戻したりする
  - 今回はスコアが高い場合に遷移を保つ

焼きなまし法 (Simulated Annealing)
- 山登り法の局所最適解に陥ってしまう欠点を改善
- スコアが改善しない時も遷移を保つ
  - スコアが改善するなら遷移を保つ
    - 遷移の確率 $\delta \leq 0$ の時 $1$
  - 改善しなくても， $\delta = Score(next) - Score(now)$ に応じて確率的に遷移を保持
    - 遷移の確率 $\delta < 0$ の時 $e^{\frac{\delta}{t}}$
    - $t$ は温度パラメータ．初期は高く，徐々に下げていく
- 十分な評価回数が確保できない場合は，スコアが確実に改善する山登り法のほうが良い場合もある

メタヒューリスティクス: 数理最適化を解くための方針の1つ
- 集中化(過去の良い解に似た解を探索)と多様化(過去の解とは異なる解を探索)のバランスを撮りながら探索と評価
- 遺伝的アルゴリズムやタブーサーチなどある

## 第5章
交互着手数字集め迷路

MiniMax法
- 外部要因(相手の手)を考慮した探索
- 2手先までのリーフノードを評価する(自分の手と相手の手)
  - $評価値 = Aのスコア - Bのスコア$
- 相手はこちらの評価が最小化するような手を選択するはず
  - i.e. 相手にとって評価が最大化する手
- 自分の手からの子ノードごとにMin選択を行い，選択されたノードの評価値で一番高い手を選択する(Max選択)
- ゲーム終了まで探索できるなら最善手を選択できる
- 途中までしか探索できないなら，評価値が重要になる
  - ゲームによって盤面の評価を変えると良い
- NegaMax: MiniMax法を手番プレイヤー視点で評価して実装するテクニック

AlphaBeta法
- MiniMax法の派生．計算時間が膨大になる弱点を改善した完全上位互換
- $\beta$ カット
  - min選択を踏まえて，max選択の探索を打ち切る
    - min選択の評価値$0$がある時に，一つ深いmax選択で評価値$0$以上が出たときにそれ以上の探索を打ち切る
- $\alpha$ カット
  - max選択を踏まえて，min選択の探索を打ち切る
- NegaAlpha: 常に手番プレイヤー視点で探索を行い，$\beta$ カットのみを考慮する

反復深化 (Iterative Deepening)
- 時間が許す限り深くしていくAlphaBeta法

浅い探索での解が深い探索での解となる可能性が高いという性質
- 浅い探索結果でノードを評価の高い順に並べ替えてから深い探索を行うことで $\beta$ カットを早期に発生させるテクニック

原始モンテカルロ法
- 盤面評価なしで利用できる探索手法
- プレイアウト: ゲーム終了までの1シミュレーションの単位
  - ランダムな行動での1シミュレーションのことを指すことが多い(正確にはランダムプレイアウトと呼ぶ)
- 1プレイアウトごとに，1手目の行動選択に対して累計価値($w$)と試行回数($n$)を記録する
  - e.g. 勝ち1, 引き分け0.5, 負け0
- $w/n$ (勝率)が一番高い手を選択する
- 1手目はランダムにしない
- プレイアウト数に対して勝率が大きく上がるわけではない
  - 都合の良い手も悪い手も同じ確率で選択するため

モンテカルロ法とラスベガス法
- モンテカルロ法: 必ず正しい解が得られるとは限らない代わりに時間内に終わることが保証される乱択アルゴリズム
- ラスベガス法: 計算時間が未知数だけど解が必ず正しくなる乱択アルゴリズム

MCTS (モンテカルロ木探索)
- UCB1 (Upper Confidence Bound 1)
$$
USB1 = \frac{w}{n} + C\sqrt{\frac{2\ln(t)}{n}}
$$
  - $\ln(t)=\log_e(t)$, $t=全ノードのnの総和$, $C$ は重み
  - $n$ が小さいほどバイアスが大きく， $n$ が大きいほどバイアスが小さくなる
  - 勝率とバイアスのバランスの良い探索を実現
  - UCB1 の値が高いノードを選択してプレイアウトを行う
- 2手目以降の探索
  - 試行回数が閾値を超えたノードについてはもう1手先のノード(リーフノード)を追加する
  - リーフノードの価値と試行回数はルートノードまでさかのぼって親ノードにも追加される
- 試行回数が増えるほど累積価値が洗練される
- 最終的には試行回数が最も多い行動を選択する
  - 勝率の高いノードが多く選択されている

Thunderサーチ
- MCTSのプレイアウトを盤面評価とし，探索量が少なくてもある程度信頼できる解を出す探索手法
- 筆者(青木栄太)が考案
- あらかじめ盤面評価を決めておく(どの程度自分が勝てそうかどうか)
  - 0-1の範囲に収める
  - $1 - 自分の評価 = 相手の評価$ になるようにする
- 探索の流れ
  - 合法手から到達できるノードを列挙(展開)
  - 未評価のノードを選択
  - 選択したノードを評価
  - 評価後にそのノードを展開
  - 未評価のノードを選択して評価と展開
  - ノードが全て選択済みになったら，勝率の高いノードを選択する
    - リーフノードになるまで選択し，評価を上位ノードに伝搬
    - 評価したノードを展開
  - 試行(選択，展開)を繰り返す
- リーフノードを評価する(プレイアウトを行わない)
  - 深さ偶数 -> 相手が行動した直後の評価
  - 相手に都合がいいノードの評価が逆伝搬していく
  - 評価が逆転してバランスがいい探索になる
- 制限時間付きでAlphaBetaより強い結果に
- AlphaZeroから着想
  - 探索アルゴリズムのベースがMCTS
  - 学習済みモデルで価値を決定
  - ノードの価値だけでなく行動に対しても価値を評価
  - ノード選択にはアーク評価値
  - 強化学習によって評価している部分をルールで評価できるようにしたのがThunderサーチ

## 第6章
同時着手数字集めゲーム

原始モンテカルロ法の実装
- 相手の行動を考慮する必要がないため，交互でも同時でも大差ない
  - 視点の入れ替わりがない

MCTSの実装
- 手番が交互に代わることが前提
- 自分と相手の行動を1ターンとしている -> MCTS内ではターン数を2倍で計算
- MCTSの原始モンテカルロ法に対する勝率があまり高くない
  - 同時と交互でどうしても動作の差が生まれてしまうから
  - 今回は，偶数ターンのみ床のポイントの取得と消失を行えばいいが，工夫ができないゲームもある
  - 相手の行動を事前に知っているかどうかも影響する

DUCT (Decoupled Upper Confidence Tree)
- 両者が同時に着手するルールを正確にシミュレートする探索方法
- MCTSと同様にゲーム木を構築して展開と選択を繰り返す
- 1つのノードに両プレイヤーの情報を持つ
  - 自分の合法手と相手の合法手の前パターンに対しての情報をもち，各組み合わせからノードを展開
- 評価と選択は同じ
- $展開するノード数 = N(自分の合法手) \times M(相手の合法手)$
- ゲーム性によってはMCTSの方が勝率が高いこともあるので，いろいろ考慮して手法選定をする

## 第7章
壁有り数字集め迷路
- 障害物がある
- 「棒倒し法」で壁を生成

評価関数の設計
- 補助スコアを加える
  - e.g. ポイントのある床への最短経路
    - 連続してポイントを取得しに行ける
  - 距離の計算
    - マンハッタン距離やユークリッド距離は使えない(壁があるため)
    - 幅優先探索で計算する

多様性の確保方針
- 同一盤面除去
  - 別の親ノードから同じ盤面が出現したときに，1つを残して探索対象から除去する
  - ハッシュを用いて盤面を記録し，探索キューにすでにそのハッシュがあればその盤面を探索しないようにする
    - Zobrist hashing
      - 衝突: 異なる入力データから同じハッシュ値が計算されてしまうこと
      - キャラクターの座標，ポイントの値・座標のみを入力データにする
        - 情報を減らすために壁やターン数は利用しない
      - 各キャラの座標，ポイントの値・座標に対してランダムな値を割り当てる
      - 各要素の値全てを排他的論理和して求める
      - ターンが進んだ際は，直前の盤面のハッシュ値を元に差分だけ更新することで高速に新しいハッシュ値を計算できる
        - 全てのビット列について自分同士をXORすると$0$になる
        - 元のキャラクター位置をXORして情報を消す
        - 次のキャラクター位置をXORして情報を付与する
        - 盤面が広いと，全てのマスのXORをするのはコストが高いので，この方法がよい
          - 移動だけなら2回，ポイントの消滅を含めても3回まで

高速化
- 盤面の表現をビットボードにすることで幅優先探索を高速化
  - 1つの変数を2進数のビット列とみなして配列のように扱う
    - キャラクター位置
    - ポイント
    - 壁
  - 一手で行ける範囲をORで求める
  - 壁でいけない範囲をANDで求める
  - ポイントに到達できるかをANDで計算する
- 単一のビット列で盤面を表現するともっと高速化する
  - 上下の移動が，幅分だけシフトすればいいことになる
  - ビットマスクではみ出しに対応
- コピー回数の抑制
  - 探索キューに格納する対象をオブジェクトではなくポインタにする
  - 参照カウント方式: 参照元の数を記録しながらメモリ管理

## 第8章
コネクトフォーにMCTSを適用
- ビットボードによる盤面表現で高速化
  - マス+1のサイズでビッドボードを作成
  - 自分の駒があるか & 自分か相手のいずれかの駒があるかを表すビットボード
  - 全ての駒 + 盤面の全ての底にビットを立たせたビットボード = 各列で次に駒をおける場所
    - 列ごとに足し合わせる
    - 枠外にはみ出る場合はその列にはおけないということ
    - 確認したい列を1で埋めて，ANDをとり，0出なければ合法手，0であれば合法手ではない
  - 自分の駒 XOR 全ての駒 = 相手の駒
  - 全ての駒 + 選択した列の底が1のビットボード = 選択した列は新しい駒だけが1のビットボード
    - 選択した列は新しい駒だけが1のビットボード OR 全ての駒 = 更新後の全ての駒
    - 相手の駒 XOR 更新後全ての駒 = 更新後の自分の駒
  - 勝敗判定
    - 横に2個の駒が連続しているか
      - 自分の駒 AND 自分の駒>>7 = 横に2個つながった駒
        - これは1になっているビットは，自信とその右隣のビットが立っていることを示している
    - 横に4個の駒が連続しているか
      - 横に2個が，右方向距離2の位置にビットがたっているか調べればよい
        - 1がたっていれば繋がっている
      - 横に2個繋がった駒 AND 横に2個つながった駒>>14 = 横に4個つながったもの
    - 右下は >>6 と >>12
    - 右上は >>8 と >>16
    - 縦は >>1 と >>2
