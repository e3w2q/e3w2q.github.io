# PRK Firmwareのドキュメントを充実させる方法

2021/12/16

これは、[PRK Firmware Advent Calendar 2021](https://adventar.org/calendars/7086)の16日目の記事です。

15日目の記事はhasumikinさんの[ロータリエンコーダ完全に理解した（後編）](https://shimane.monstar-lab.com/hasumin/rotary-encoder-2)でした。

## 経緯

RP2040を使ったキーボードに興味があり、その過程でPRK Firmwareについて調べたことを、[Raspberry Pi Pico、Pro Micro RP2040を使ってPRK Firmwareに対応した自作キーボードを設計する際のポイント](https://e3w2q.github.io/19/)という記事にまとめました。

PRK Firmwareはhasumikinさんがハイペースで開発を進めていて、ドキュメントがそこまで充実してません。

私が書いた記事を私のサイトに載せておくだけだと、PRK Firmwareに興味を持った方が辿り着けない可能性が高いので、本家のドキュメントに貢献したいなあとhasumikinさんに相談したところ、書いたドキュメントを本家のWikiに反映する仕組みを作っていただけました。

## ドキュメントを本家のWikiに反映する仕組み

こんな感じで、[picoruby/prk_firmware-wiki](https://github.com/picoruby/prk_firmware-wiki)にプルリクエストを出し、マージされると公式サイトのWikiに反映されます。

![flow](flow.png)

## ドキュメントの書き方

将来的に何らかのルールができるかもしれませんが、現時点では以下のとおりです。

- [picoruby/prk_firmware-wiki](https://github.com/picoruby/prk_firmware-wiki)にプルリクエストを出す

- 書き方は、既存のドキュメントを参考にする

  - [Mutual UART communication](https://github.com/picoruby/prk_firmware-wiki/blob/master/Mutual-UART-communication.md)
  - [RGB feature](https://github.com/picoruby/prk_firmware-wiki/blob/master/RGB-feature.md)

- 画像を載せたい場合は、prk_firmware-wiki/images/に置き、以下のようにフルURIで指定する

  ```
  ![](https://github.com/picoruby/prk_firmware-wiki/blob/master/images/RP2040_boards.jpg)
  ```



仕組みを作っていただいたので、[Raspberry Pi Pico、Pro Micro RP2040を使ってPRK Firmwareに対応した自作キーボードを設計する際のポイント](https://e3w2q.github.io/19/)をおいおい載せていきたいです。

[一覧へ](../)

