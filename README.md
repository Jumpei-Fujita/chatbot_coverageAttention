# coverageAttention

## データセットの作成
chatbotを作成するにあたり、questionとanswerのペアになっているデータセットを以下のサイト（kaggle）からダウンロードし用いた。
データセットのQandAの例は以下のとおりである。<br>
![model](https://github.com/Jumpei-Fujita/chatbot_normalAttention/blob/main/example.png)
### 前処理
大文字の削除、短縮系や特殊文字等の排除を行った。
また、全て同じ長さに揃えるため、paddingなどの処理を行った。
## モデル構築
以下のようにAttentionベースのseq2seqモデルにCoverage Mechanismを導入し、attention weightの繰り返しの注目を回避する試みを行った。<br>
![model](https://github.com/Jumpei-Fujita/chatbot_coverageAttention/blob/main/coverageNormalAttn.png)<br>
また、Encoder, Decoder, Attentionは以下のような数式で表される。<br>
![calc1](https://github.com/Jumpei-Fujita/chatbot_normalAttention/blob/main/calc1.png)<br>
![calc2](https://github.com/Jumpei-Fujita/chatbot_coverageAttention/blob/main/calc2_coverage.png)<br>
Encoderで入力から特微量を抽出し、Decoderでは出力単語の回数だけ処理を行う。Attention機構ではEncoderで得た特微量、Decoderの内部表現を用いてattention weightを作成し、入力のどの部分に注目を与えるべきかを学習する。

## 学習
Decoderの出力の単語生成確率ベクトルをlogSoftmax関数により変形し、NLLLossを最小化するようにパラメータθを調整する。
また、attention weightが時間に依存せず同じ値を取り続けることを避けるため、ハイパーパラメータλを導入した。
最適化手法はAdamを用いた。
以下は学習の様子である。過学習になってしまっている。<br>
![model](https://github.com/Jumpei-Fujita/chatbot_coverageAttention/blob/main/coverageDialogLoss.png)

## 結果＋Attention可視化
Attentionを可視化したものを以下に示す<br>
1.coverage mechanism導入なし
![attention](https://github.com/Jumpei-Fujita/chatbot_normalAttention/blob/main/normalDialogAttn3500.png)
![attention](https://github.com/Jumpei-Fujita/chatbot_normalAttention/blob/main/normalDialogAttn2777.png)<br>
2.coverage mechanism導入
![attention](https://github.com/Jumpei-Fujita/chatbot_coverageAttention/blob/main/coverageDialogattn3500.png)
![attention](https://github.com/Jumpei-Fujita/chatbot_coverageAttention/blob/main/coverageDialogattn2777.png)<br>
coverage mechanismの有無によるattention weightを比較すると、coverage mechanismなしの場合、stepごとの注目度合いにあまり変化が見られず、繰り返しの原因や、全て同一の返答の原因になってしまっているが、coverage mechanism有りの場合はattention weightが時間によってばらつき具合が変化しているのがわかる。会話が成り立っていない可能性があり。教師データ増量などの改善の余地がある。


## コードの実行手順
上から順番に実行していけば良い。なおデータセットはしかるべきところに保存しておく必要がある。

## 最後に
何かアドバイス等ありましたらコメントいただけると幸いです。

