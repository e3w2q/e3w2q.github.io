# 自作キーボード設計に参考となる公開情報
2019/12/25

国内の自作キーボードコミュニティにおいては、基板データを公開するという文化がちょっとあったりします。

Let's Splitの基板データが公開されて、このグループバイが国内の自作キーボードの広まりのきっかけとなったということが影響しているのでしょうか？

私も基板設計にあたって、公開されているフットプリントを使わせていただき、公開されている基板データを参考にさせていただきました。

これから自作キーボード設計をしようという方向けに、私の知っている公開情報のリンクを以下にまとめました。

## KiCADの使い方

[KiCadで雑に基板を作る チュートリアル](https://www.slideshare.net/soburi/kicad-53622272)

KiCADの使い方が初歩の初歩から書いてあります

[PCB Guide Part 1 - Preparations](https://wiki.ai03.me/books/pcb-design/page/pcb-guide-part-1---preparations)

ai03さんのチュートリアル

KiCADでキーボード設計をする手順が丁寧に書かれています（英語）

- [Dm9records :: 自作キーボードAdvent Calendar 2018](http://www.dm9records.com/index.php/tips/elec/diy_kbd_adv_2018/)

  キースイッチのレイアウトを行うための操作が書いてあります。

## KiCADのフットプリント

- [foostan/kbd: Publish data for Keyboard](https://github.com/foostan/kbd)

  foostanさんのフットプリント　ライセンスはMIT

  各種スイッチ（MX、choc、Midheight、ソケットありなし）はもちろんPro Micro、OLED、TRRSジャック、2つ足のリセットスイッチなど、国内のキーボードキットでよく使われているものが網羅されています

[ai03-2725/MX_Alps_Hybrid: KiCad Library of custom MX-Alps footprints](https://github.com/ai03-2725/MX_Alps_Hybrid)

ai03さんのフットプリント　ライセンスはMIT

各種スイッチはもちろんスタビライザーの穴付きのフットプリントがあります

[hsgw/SMKJP_kicad_components: kicad components library for diy mechanical keyboard.](https://github.com/hsgw/SMKJP_kicad_components)

hsgwさんのフットプリント　ライセンスはKiCad Libraries License（[Creative Commons CC-BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/legalcode)）

KiCADの3Dビューアで見たときにきちんと表示されます（すごい）

[keebio/Keebio-Parts.pretty: Various KiCAD footprints for mechanical keyboard parts](https://github.com/keebio/Keebio-Parts.pretty)

Keebio社のフットプリント　ライセンスはMIT

zigzag仕様のPro Microフットプリント、Elite-Cのフットプリントがあります



## 基板データ（PCB）

[climbalima/let-s-Split-v2: second version of split ortho](https://github.com/climbalima/let-s-Split-v2)

Let's Split

[komar007/gh60: GH60 - an open-source mechanical keyboard PCB designed for the community at geekhack.org](https://github.com/komar007/gh60)

GH60　Pro Microを使わないキーボード設計、60%ケースのネジ穴の確認にも



## プレート用データ作成

[KiCADで作った外形データをDXFで作成する - Self-Made Keyboards in Japan](https://scrapbox.io/self-made-kbds-ja/KiCAD%E3%81%A7%E4%BD%9C%E3%81%A3%E3%81%9F%E5%A4%96%E5%BD%A2%E3%83%87%E3%83%BC%E3%82%BF%E3%82%92DXF%E3%81%A7%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B)





Duplex-Matrix（倍マトリクス）とは、キーボードの状態のスキャンの仕方を工夫することで、コントローラーで把握できるキー数を2倍にするというものです。

国内の自作キーボードコミュニティにおいては、[あぷろ](https://twitter.com/elfmimi)さんが2018/11/10に[Self-Made Keyboards in Japan Discord Server](https://scrapbox.io/self-made-kbds-ja/Self-Made_Keyboards_in_Japan)に着想を投稿<a href="#foot1">[1]</a>し、その後2019/5/1に実装例が投稿<a href="#foot2">[2]</a>され、認知されるようになりました。

最近では、天下一キーボードわいわい会Vol.3で[かーくんさんが販売された](https://twitter.com/kakunkb/status/1199512313822240770)[わんわんわんきーぼーど](https://github.com/qmk/qmk_firmware/tree/master/keyboards/thedogkeyboard)にもこの方法が使われています。

このDuplex-Matrixを自分の理解できる範囲で整理してみました。C言語のきちんとした知識を持っていないので、間違っている部分があればご指摘いただけると幸いです。

## 基本のキーマトリクススキャン

本題のDuplex-Matrixの説明に入る前に、基本のマトリクススキャンについて触れておきます。

多くの自作キーボードでは、限られたGPIO数でたくさんのキーの状態を取得するために、キースイッチをマトリクス状に配線して、何行目、何列目の通電状態が変わったかを判定します。

### 基本の回路

下図は、自作キーボード（片側2行×3列の分割キーボード）の回路図の例です。

![schema3](schema3.png)

この例では、例えばSW2のキーが押されたときに、COL2からROW1に流れる電流の状態が変化します。

ダイオードは、キーが同時押しされたときに正しく判別するために必要となります。以下の記事が詳しいです。

- IKeJIさんの[キーボードのマトリクス方式の分類](https://blog.ikejima.org/make/keyboard/2019/12/14/keyboard-circuit.html)の[マトリクスの項](https://blog.ikejima.org/make/keyboard/2019/12/14/keyboard-circuit.html#%E3%83%9E%E3%83%88%E3%83%AA%E3%82%AF%E3%82%B9)
- [キーボード自作のススメ](https://www.slideshare.net/Retrieva_jp/ss-86955149)の24スライド目以降
- ゆかりさんの[オリジナルキーボードを作ってみる その7「キーマトリックス」 - ゆかりメモ](http://eucalyn.hatenadiary.jp/entry/original-keyboard-07)

### 基本の回路の実装

自作キーボード試作用基板の[SU120](https://github.com/e3w2q/su120-keyboard)で実装してみた例です。

![6key](6key.jpg)

![6key_back](6key_back.jpg)

### 基本のファームウェア

この回路図に対応したファームウェアをQMK Firmwareで作成してみます。

QMK Firmwareのインストールについては、[はじめに - QMK Firmware](https://docs.qmk.fm/#/ja/newbs_getting_started)を参照してください。

QMK Firmwareのインストールの後、プロジェクトのひな形を作成します。

```bash
$ ./util/new_keyboard.sh
Generating a new QMK keyboard directory

Keyboard Name: 2x3test
Keyboard Type [avr]:
Your Name [e3w2q]:
```

回路図に合うようにプロジェクトを修正します。config.hのキーマトリクスに関する部分は以下のようになります。

```c
/* key matrix size */
#define MATRIX_ROWS 2*2 // 行数 右手側と左手側があるので、*2
#define MATRIX_COLS 3   // 列数
```

```c
/* COL2ROW, ROW2COL*/
#define DIODE_DIRECTION COL2ROW // COL（列）のピンからROW（行）のピンに電流が流れるようにダイオードを付けた場合はCOL2ROW、逆向きの場合はROW2COL
```

[修正点の全体はここに載せています](https://github.com/e3w2q/qmk_firmware/commit/75feae784f16063fcb511fa320448eeebfb23902)。

これをPro Microに書き込んで、回路図のとおりに配線すると、片手2x3の分割キーボードとなります。

### 基本のキーマトリクススキャンはどこで行われているか

通常、自作キーボードを設計する場合はここまでの知識で十分なのですが、今回は深堀りして、マトリクススキャンが行われているコードまで辿ってみます。

[Makefile](https://github.com/qmk/qmk_firmware/blob/master/Makefile#L449)

```makefile
    MAKE_CMD := $$(MAKE) -r -R -C $(ROOT_DIR) -f build_keyboard.mk $$(MAKE_TARGET)
```

↓

[build_keyboard.mk](https://github.com/qmk/qmk_firmware/blob/master/build_keyboard.mk#L346)

```makefile
include common_features.mk
```

↓

[common_features.mk](https://github.com/qmk/qmk_firmware/blob/master/common_features.mk#L362-L369)

```makefile
# Include the standard or split matrix code if needed
ifneq ($(strip $(CUSTOM_MATRIX)), yes)
    ifeq ($(strip $(SPLIT_KEYBOARD)), yes)
        QUANTUM_SRC += $(QUANTUM_DIR)/split_common/matrix.c
    else
        QUANTUM_SRC += $(QUANTUM_DIR)/matrix.c
    endif
endif
```

↓

分割キーボードでrules.mkにおいて「SPLIT_KEYBOARD = yes」と設定していれば[quantum/split_common/matrix.c](https://github.com/qmk/qmk_firmware/blob/master/quantum/split_common/matrix.c)

そうでない一体型キーボードであれば[quantum/matrix.c](https://github.com/qmk/qmk_firmware/blob/master/quantum/matrix.c)

このmatrix.cの、matrix_scan()という関数が、マトリクススキャンを行っている大元のコードです。

```c
uint8_t matrix_scan(void) {
    bool changed = false;

#if defined(DIRECT_PINS) || (DIODE_DIRECTION == COL2ROW)
    // Set row, read cols
    for (uint8_t current_row = 0; current_row < MATRIX_ROWS; current_row++) {
        changed |= read_cols_on_row(raw_matrix, current_row);
    }
#elif (DIODE_DIRECTION == ROW2COL)
    // Set col, read rows
    for (uint8_t current_col = 0; current_col < MATRIX_COLS; current_col++) {
        changed |= read_rows_on_col(raw_matrix, current_col);
    }
#endif

    debounce(raw_matrix, matrix, MATRIX_ROWS, changed);

    matrix_scan_quantum();
    return (uint8_t)changed;
}
```

COL2ROWの場合なら、一行（row）ずつ、その行の各列(col)の状態を読んでいます（read_cols_on_row）。

## Duplex-Matrixによるマトリクススキャン

### Duplex-Matrixの回路

では、先ほどの片手2x3の回路を、使うGPIOピン数は変えずに、Duplex-Matrixを使って二倍のキー数を判別できるようにしてみます。

![schema5](schema5.png)

既存のCOL2ROWのキーマトリクス（2行×3列＝6キー）をコピーして、ダイオードの向きだけ変更したROW2COLのキーマトリクス（2行×3列＝6キー）を増やしました（片手側は計6キーから計12キーに）。

回路図の修正は、これだけです。とてもシンプル。

COLからROW方向にのみスキャンしていたキーマトリクスに、ROWからCOL方向にスキャンするキーマトリクスを加えたものが、Duplex-Matrixなのです。

### Duplex-Matrixの回路の実装

[SU120](https://github.com/e3w2q/su120-keyboard)で実装した例です。

![12key](12key.jpg)

![12key_back](12key_back.jpg)

（手持ちのソケットが尽きたので、キー入力のテストはピンセットで行いました）

### Duplex-Matrixのファームウェア(1)

QMK Firmwareの標準設定では、config.hのDIODE_DIRECTIONをCOL2ROW、またはROW2COLと設定することで、matrix.cの_matrix_scan関数でCOLからROW方向、またはROWからCOL方向のスキャンが行われます。

Duplex-Matrixでは、**COLからROW方向のスキャン**と**ROWからCOL方向のスキャン**を**両方**行いたいので、自分でマトリクススキャンを書く必要があります。

まずは、rules.mkで以下を記載します。

```makefile
CUSTOM_MATRIX = yes
```

これにより、標準のmatrix.cの読み込みが止まります。

[QMK Firmwareのドキュメント](https://docs.qmk.fm/#/getting_started_make_guide?id=rulesmk-options)に

> ```
> CUSTOM_MATRIX
> ```
>
>
> Lets you replace the default matrix scanning routine with your own code. You will need to provide your own implementations of matrix_init() and matrix_scan().

と書いてあるとおり、自前でmatrix_init()とmatrix_scan()を実装する必要があります。

rules.mkにさらに以下を追加し、自前のmatrix.cが読み込まれるようにします。

```makefile
SRC += matrix.c
```

matrix.cの前に、config.hを修正します。今回は列を2倍（横方向に2倍）することにします。

```diff
 /* key matrix size */
 #define MATRIX_ROWS 2*2 // 行数 右手側と左手側があるので、*2
-#define MATRIX_COLS 3   // 列数
+#define MATRIX_COLS 3*2 // 列数 Duplex-Matrix法により、*2
```

```diff
 #define MATRIX_ROW_PINS { F6, F7 }     // 各行に割り当てるピン番号
-#define MATRIX_COL_PINS { D1, D0, D4 } // 各列に割り当てるピン番号
+#define MATRIX_COL_PINS { D1, D0, D4, D1, D0, D4 } // 各列に割り当てるピン番号
```

DIODE_DIRECTIONは、通常、COL2ROWまたはROW2COLを設定しますが、独自にマトリクススキャンを行う場合のために[CUSTOM_MATRIXという値](https://github.com/qmk/qmk_firmware/blob/master/quantum/config_common.h#L19-L22)の定義が用意されています。

matrix.cの書き方次第でDIODE_DIRECTIONを無視することもできるのですが、今回はDIODE_DIRECTIONをCUSTOM_MATRIXとしておきます。

```diff
 /* COL2ROW, ROW2COL*/
-#define DIODE_DIRECTION COL2ROW
+#define DIODE_DIRECTION CUSTOM_MATRIX
```

列数が増えたので、2x3test.h、keymaps/default/keymap.cも手直ししておきましょう。

[ここまでの修正についてはここにまとめて載せています](https://github.com/e3w2q/qmk_firmware/commit/4f9630a07dc268771f6d62e96d5f3ab151a64c72)。

### Duplex-Matrixのファームウェア(2)

次にmatrix.cを用意します。

matrix.cはゼロから書くのではなく、分割キーボードであれば[quantum/split_common/matrix.c](https://github.com/qmk/qmk_firmware/blob/master/quantum/split_common/matrix.c)を、そうでない一体型キーボードであれば[quantum/matrix.c](https://github.com/qmk/qmk_firmware/blob/master/quantum/matrix.c)をコピーしてベースとするとよいです。

今回は分割キーボードなので、[quantum/split_common/matrix.c](https://github.com/qmk/qmk_firmware/blob/master/quantum/split_common/matrix.c)を利用します。

書き換えの基本的な考え方は以下のとおりです。

デフォルトmatrix.cではDIRECT_PINS、COL2ROW、ROW2COLのマトリクススキャンしか書かれていないので、CUSTOM_MATRIX用の分岐を入れます。

```diff
 #ifdef DIRECT_PINS
 //...
 #elif (DIODE_DIRECTION == COL2ROW)
 //...
 #elif (DIODE_DIRECTION == ROW2COL)
 //...
+#elif (DIODE_DIRECTION == CUSTOM_MATRIX)
+//...
 #endif
```

COL2ROWのスキャンを行ってからROW2COLのスキャンを行うようにします。COL2ROWのスキャンは、元の列の範囲（今回なら1～3列目）で行い、ROW2COLのスキャンは、増やした列の範囲（今回なら4～6列目）で行います。

```diff
+    // Set row, read cols
+    for (uint8_t current_row = 0; current_row < ROWS_PER_HAND; current_row++) {
+        changed |= read_cols_on_row(raw_matrix, current_row);
+    }
+    // Set col, read rows
+    for (uint8_t current_col = MATRIX_COLS/2; current_col < MATRIX_COLS; current_col++) {
+        changed |= read_rows_on_col(raw_matrix, current_col);
+    }
```

正直、matrix.cをDuplex-Matrix用に修正する作業がなかなかうまくいかず一番大変だったのですが、コードをたくさん貼っても面白くないので、[デフォルトのmatrix.cからの変更点についてはここにまとめて載せました](https://github.com/e3w2q/qmk_firmware/commit/762fe3e0a7cbea768245a75520f06ff5a2f00b9f)。

## おわりに

これで、通常のキーマトリクスに必要なピン数の1/2のピン数でキーマトリクススキャンを行えるようになりました！

例えば拙作の[SU120](https://github.com/e3w2q/su120-keyboard)では2つのPro Microを使うと120キーまでのキーボードを作れるというものですが、Duplex-Matrixを使えばなんと倍の240キーのキーボードが作れてしまいます。

![240key](240key.png)

まあ、ここまでのキー数は不要としても、1つのPro Microでフルキーボードのキー数を使えるのは魅力的ですよね。

また、ロータリーエンコーダーを使うとピン数が足りなくなりがちになるので、そういう際にも便利です。

サンプルコードの全体は以下に載せています。

[https://github.com/e3w2q/qmk_firmware/tree/2x3test/keyboards/2x3test](https://github.com/e3w2q/qmk_firmware/tree/2x3test/keyboards/2x3test)

皆さんの自作キーボード設計の一助となれば幸いです。

この記事は、あぷろさんの着想、かーくんさんの[わんわんわんきーぼーどのQMK Firmwareソースコード](https://github.com/qmk/qmk_firmware/tree/master/keyboards/thedogkeyboard)を読んで得た知識をもとに書きました。あぷろさん、かーくんさん、ありがとうございました。

## 補足:その他の特殊なマトリクスについて

記事を書くにあたって、Self-Made Keyboards in Japan Discord ServerでDuplex-Matrixの初出を尋ねたところ、ここで説明したDuplex-Matrixのほかのマトリクス回路のお話も聞くことができました。

### チャーリープレックス

1995年にMaxim Integrated社のCharlie Allen氏が考案したLEDマトリクスのコントロール方法。各ピンの間にLEDを2つ付けるもの（それぞれ逆向き）。Nピンで、(N-1)*Nのマトリクスを制御できます。

[https://en.wikipedia.org/wiki/Charlieplexing](https://en.wikipedia.org/wiki/Charlieplexing)

### 2乗マトリクス

チャーリープレックスをキーマトリクスに適用したもの。各ピンの間にスイッチ＋ダイオードを2つ付けるもの（ダイオードはそれぞれ逆向き）。Nピンで、(N-1)*Nのマトリクスを取得できるので、Duplex-Matrixよりさらに多くのキースイッチを付けられます。IKeJIさんの[キーボードのマトリクス方式の分類](https://blog.ikejima.org/make/keyboard/2019/12/14/keyboard-circuit.html)の[2乗マトリクスの項](https://blog.ikejima.org/make/keyboard/2019/12/14/keyboard-circuit.html#2%E4%B9%97%E3%83%9E%E3%83%88%E3%83%AA%E3%82%AF%E3%82%B9)に詳しく載っています。

国内自作キーボードコミュニティでは、IKeJIさんが[2017年10月3日にTwitterで触れ](https://twitter.com/ikeji/status/915069191119544321)、[2018年に電卓で実装された](https://www.ikejima.org/projects/2018021-calc.html)のが初出のようです。

### 海外キーボードコミュニティでの"Duplex Matrix"

マトリクスを組み替えることで使用するピン数を削減するもの。

[https://wiki.ai03.me/books/pcb-design/page/matrices-and-duplex-matrix](https://wiki.ai03.me/books/pcb-design/page/matrices-and-duplex-matrix)

例えばUS60%キーボード（61キー）を素直にマトリクスで組もうとすると、5行×14列=最大70キーとなり、ピンは5+14=19必要となります。

![us60percentkeyboard](us60percentkeyboard.png)

マトリクスの組み方を工夫し、列に使うピン数を1/2に、行に使うピン数を2倍にしても同じキー数を実現できます。こうすると10x7=最大70キーはそのままで、必要なピン数は10+7=17となり、使用するピン数が2つ減りました。

もともとai03さんが2017年にWebに掲載した記事の言葉がキーボードコミュニティで定着した（してしまった）とのことです<a href="#foot3">[3]</a>。

<hr />
<p id="foot1">[1] https://discordapp.com/channels/376937950409392130/377073908496465920/510497794843213824</p>
<p id="foot2">[2] https://discordapp.com/channels/376937950409392130/381074986208591884/572998059793842187<br />
https://discordapp.com/channels/376937950409392130/418326189644709889/573354228701986828</p>
<p id="foot3">[3] https://discordapp.com/channels/376937950409392130/635679787905712148/658272879430860833</p>
[一覧へ](../)