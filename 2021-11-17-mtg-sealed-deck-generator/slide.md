---
marp: true
theme: gaia
math: katex
style: |
  @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP&display=swap');
  section {
    font-family: 'Noto Sans JP', serif;
  }
---

<!-- class: invert -->

# MTGシールドデッキジェネレータ作成

2021/11/17(水)

---

## 自己紹介

- github: @shinji19
- ソシャゲ作っている会社で勤務
  - 業務で使っている言語： Golang, C#, 日本語
- 競技が趣味
  - SPAJAM2017 優秀賞
    - https://history.spajam.jp/2017/final/result/
  - Sekappyプログラミングチャレンジ 
    - 部門3『シールドデッキジェネレーター』 最優秀賞

---

## MTG(?)シールド(?)デッキ(?)ジェネレータ

- MTG(?)
  - ❌ meeting
  - ⭕️ Magic: The Gathering
- シールド(?)
  - 未開封(sealed)
  - カードがランダムに入ったパック
- デッキ(?)
  - カードの束

---

## シールドデッキ

- 1パック15枚入りのパックを6個開封
- 無限の「土地」カード
- パックから出たカードと土地カードを合わせて40枚以上のデッキを作る

---

## MTGシールドデッキジェネレータ

- 入力: カード90枚 (+無限枚の土地カード)
- 出力: デッキ（カード40枚）

---

## 実装方法を考える

- デッキの評価関数を作る
- 全てのカードの組み合わせ（デッキ）を用意し、点数を計算する
  - 土地は17枚固定
  - 残りの組み合わせは $_{90}C_{40-17}\fallingdotseq10^{21}$
- 全てのデッキを列挙するのは無理そう
  - 遺伝的アルゴリズムでごまかす
  - 多数のランダムなデッキを用意して、少しづつ改善してみる

---

## 実装 デッキの評価関数

- カード単体の強さ
  - 単純に強いカードが入っていれば、強いデッキ
  - 人力で
- 計算
  - カード単体の強さの合計を、デッキの評価値に足す

---

## 実装 デッキの評価関数

- デッキの色ペナルティ
  - 5色ある
  - 少なければ少ないだけ良い
  - 2色以下を目指す
- 計算
  - デッキのカードを色毎に分ける
  - デッキ内の1番目と2番目に多い色以外のカードの枚数を合計
  - （↑の値） / (デッキの枚数) をデッキの表価値から引く

---

## 実装 デッキの評価関数

- クリーチャーカードのマナカーブペナルティ
  - 2マナ: ****
  - 3マナ: *****
  - 4マナ: ****
  - 5マナ: **
  - 6マナ: *
- 計算
  - デッキ内のカードをマナ域毎ごとに枚数を計算し↑との差の絶対値を合計
  - （↑の値） / (デッキの枚数+ 16) をデッキの表価値から引く
---

## 実装 デッキの評価関数

- クリーチャー以外のカード枚数ペナルティ
  - 7枚を目指す
- 計算
  - クリーチャー以外のカード - 7の絶対値を計算
  - （↑の値） / (デッキの枚数+ 7) をデッキの表価値から引く
---

## 実装 デッキの評価関数

- カード単体の強さ
- デッキの色
- クリーチャーカードのマナカーブペナルティ
- クリーチャー以外のカード枚数ペナルティ

---

## 実装 遺伝的アルゴリズム

- 遺伝子 
  - 入力として与えられたカードが90枚
  - 90個の情報を持てるようにする
- 遺伝子の例
  - カード1, カード2, ..., カード90
  - 1, 0, ..., 1 
  - ↑の遺伝子であれば、デッキはカード1,カード90が含まれている（カード2は含まれない）ことを表す

---

## 実装

https://github.com/shinji19/sealed-deck-generator

---

## 実行

- 90枚のカード
  - https://www.moxfield.com/decks/7KW-NWrdiEOQkxL1qtvpqQ
- 結果
  - https://www.moxfield.com/decks/1r0w77ZCTEWD5JKMh_Y5nQ
  - https://www.moxfield.com/decks/7AncGnzLUU-FUxQ71Q73iQ
