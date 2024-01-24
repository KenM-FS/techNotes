# ゲームから学ぶAI 環境シミュレータx深層強化学習で広がる世界
西田圭介. 株式会社技術評論社. 2022.08.06.

Finish reading at: YYYY.MM.DD

###### Purpose
シミュレーションと深層強化学習について学ぶ．
タスクに対して適切なシミュレーション環境と強化学習手法を提案できるようになる．

## Notes
- DeepMindが発表した論文を中心に紹介

歴史
- 2016年: AlphaGo (囲碁)
- 2019年: AlphaStar (StarCraft II)
  - 多くの先行研究のもとに成り立っている
    - 2013年のDQN

### 1章 ゲームAIの歴史
世界最初の電子計算機: ENIAC (1945年)
- 数年後にチェスをプレイするコンピュータプログラムの提案
- 1951年に実際に動くものが開発されている(Turochamp)

Deep Blue
- 1997年にGarry Kasparov(チェスの世界チャンピオン)に勝利
- IBMのスーパーコンピュータがベース
  - 2つのタワー型コンピュータ
  - 30個のプロセッサ
  - 480個のチェス専用チップ
- 2億手/sec
- オープニングを持つ
  - ゲーム序盤の最善とされる定石のデータベース

その後
- 2013 Ponanzaが将棋でプロ棋士に平手で勝利
- 2016 AlphaGoが囲碁で世界チャンピオンに勝利
- 2018 AlphaZeroがチェス，将棋，囲碁で最強のAIに

以前は探索と評価によって手を決定していた
- 効率の良い探索
- 質のいい評価関数
  - 手作り

将棋
- 2006 Bonanza
  - WCSC(world computer shogi championship)優勝
  - 評価関数を機械学習によって決定
    - 既存の棋譜データから評価関数を学習
  - 大量のデータから学習する時代へ
- 2013 Ponanza
  - 四段を破りプロの棋士レベルに到達
  - 自己対戦によって改善
    - 大量のリソースを使って事前に計算する
  - 2015, 2016 にWCSC優勝
- 2017 elmo
  - WCSCでPonanzaを破る
  - 学習方法に工夫
- 2018 AlphaZero
  - elmoに大差で勝利
  - 人間には太刀打ちできない領域へ

囲碁
- チェスや将棋と比較して手数がけた違いに多い
- 人間の直観的な絞り込みを再現する必要がある
- 2016 AlphaG0
  - 世界チャンピオンに勝利
  - 評価関数だけでなく，探索にも機械学習を取り入れる
- 自己対戦による学習
  - AlphaGo Zero では自己対戦に3500万ドルが投入された
- 2018 AlphaZero
  - チェス，将棋，囲碁で世界最強のAIに
  - ゼロからの機械学習でマスターに
    - 人間が積み重ねた知識に全く頼らない
    - 新しい戦法の発見

汎用ビデオゲームプレイ
- 2013 DQN
  - Atari 2600
  - 自らゲームのやり方を学習するAI
  - 深層学習と強化学習を合わせた深層強化学習の先駆け
- 2015 TensorFlow: 深層強化学習ライブラリのリリース

なぜAtari 2600なのか?
- 古典ゲームであり，ゲームに必要なCPUやメモリが少ない
  - 研究目的での利用に向いている
- 実際はStellaを使用する
  - オープンソースエミュレータ
  - ALE(Arcade Learning Environment)
    - Stella上でゲームAIを動かすためのライブラリ
      - 操作やスコア表示などゲームAI開発に便利な機能を備える
    - 2012年リリース
    - 50以上のゲームに対応
  - これらによって広く利用されるようになった
- ゲームROMは著作権が(70年は)切れていない
  - グレーなので取り扱いには注意が必要

強化学習のマップ
- https://image.slidesharecdn.com/rlssimai1-200914094519/75/-7-2048.jpg?cb=1665847238

Atari-57ベンチマーク
- Atari 2600 から選択された57のゲーム
- ベンチマークに用いられる
- 長期的な計画が必要となるゲームが苦手
  - 謎解きなど
  - プランニング(planning, 行動計画)の領域
- MuZero
  - 独自の未来予想の仕組みを導入
  - チェス，将棋，Atari-57まで対応できるように

GVGAI (General video game AI)
- 2014年に開始されたゲームAIの学習環境
- 著作権に保護されたゲームに頼らない学習環境の提供
- 200以上のミニゲームが公開
- フォワードモデル
  - ある行動を選択した際の結果をシミュレート

Malmo
- MinecraftをプレイするAIを開発するための環境
- 2016年3月にMicrosoftから発表
- APIによって操作
- Malmoを使ったAIコンペティション
