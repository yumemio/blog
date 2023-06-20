---
title: "Linux コマンドラインのコンセプト: ImageMagick"
date: 2023-06-20
draft: false

series:
  - 機械学習エンジニアのためのLinux
featured_image: "/images/lmle/8-imagemagick/hero.jpg"

---

この記事は、シリーズ [機械学習エンジニアのためのLinux](/series/機械学習エンジニアのためのlinux) の第8回です。

## 前回の復習

[前回の記事](/posts/linux-ml-7)までで、 `find`、`tr`、`cut` コマンドを組み合わせたパイプ処理を紹介した。

今回からは、パイプ機能を使った別の例として、`find` コマンドと画像編集ツール ImageMagick を使って大量の画像を効率的にリサイズする方法を見ていこう。

この記事では ImageMagick の使い方を紹介し、次回の記事では `find` と ImageMagick を組み合わせる方法を考えていく。

## ImageMagick とは？

ImageMagick は、**コマンドライン上で利用できる画像編集ツール群**だ。
画像のフォーマット変換、リサイズ・ぼかし・回転などの編集、さらにはCGの作成など幅広い機能をもち、Linux コマンドライン上で画像を加工するときに広く用いられている。

ImageMagick は単体のツールではなく、`convert`（画像加工コマンド）、`mogrify`（画像の上書き編集コマンド）、`identify`（画像の情報を表示するコマンド）など、いくつかのコマンドをまとめたパッケージとして提供されている。

その中核をなすのが `convert`（バージョン7では `magick`）コマンドだ。
このコマンドひとつで、画像の加工や作成などのさまざまな機能を使うことができる。

> **📔 ノート**
>
> ImageMagick は、バージョンによってコマンドの名前や機能が一部異なる。
> 最新の**バージョン7**では `convert` コマンドの名前が `magick` になる、という大きな変化があった。
> このため、最新の ImageMagick 公式ドキュメントでは `magick` コマンドの利用が前提となっている。
>
> それに対して、Ubuntu など一部の Linux ディストリビューションでは、少し古い **バージョン6** の ImageMagick がプリインストールされている場合がある。
>
> 本稿では、ImageMagick v6 の `convert` コマンドを前提としているが、ImageMagick v7 がインストールされた環境では `convert` を `magick` に適宜読み替えてほしい。
>
> ```console
> $ # ImageMagick v6 のコマンド例
> $ convert input.jpg -rotate 30 output.png
> $ # ImageMagick v7 での、同等のコマンド
> $ magick input.jpg -rotate 30 output.png
> ```
>
> なお、インストールされている ImageMagick のバージョンは、下記のコマンドで確認できる。
>
> ```console
> $ identify --version
> Version: ImageMagick 6.9.10-23 Q16 x86_64 20190101 https://imagemagick.org
> ```

## `convert` を使った画像加工

それでは、`convert` (`magick`) コマンドを使って画像を加工する方法を見ていこう。

`convert` を使った画像処理の流れは、次のようになる。

1. ある画像を**入力ファイル**として受け取り、
2. **操作**（回転やぼかしなど）をして、
3. **出力ファイル**に、操作を適用した画像を書き出す。

入力ファイルを指定せずにCGを作成する場合や、複数の画像を扱う場合もあるが、まずは基本的な「入力」→「操作」→「出力」の流れをおさえておこう。

`convert` コマンドの基本的な文法は、次のようになっている。

```
convert 入力ファイル [オペレータ] 出力ファイル
```

回転やぼかしなどの操作・処理は「**オペレータ**」を使って指定する。
例えば `-blur` を指定すればぼかし処理、 `-border` ならば枠線をつける処理、といった具合になる。
オペレータには、ぼかしの度合いや縁の幅などの設定情報をつけることもできる。

例えば、次のコマンドは 「`input.jpg` というファイルを読み込み、時計回りに30度回転して、`output.png` として書き出す」という意味になる。

```console
$ convert input.jpg -rotate 30 output.png
```

1つの `convert` コマンドで、複数の操作・処理をすることもできる。
例えば、次のコマンドでは「画像をぼかす処理」と「緑色の枠線を追加する処理」が順番に適用される。

```console
$ convert input.jpg -blur 15x5 -bordercolor limegreen -border 5 output.png
```

![convert を使った画像加工処理の流れとコマンド例](/images/lmle/8-imagemagick/convert_info_1.png)

![1つの convert コマンド内で、複数の操作・処理をするのもOK](/images/lmle/8-imagemagick/convert_info_2.png)

`convert` コマンドのオプションやオペレータはとても数が多く、この記事ですべてを紹介することはできない。
詳細な使い方は[公式ドキュメント](https://imagemagick.org/script/command-line-processing.php)や、有志による日本語チュートリアル（[Imagemagick の使い方日本語マニュアル](https://imagemagick.biz/)、[とらりもん](https://pen.envr.tsukuba.ac.jp/~torarimon/?Imagemagick)など）を参照してほしい。

## `convert` の使用例

ここでは、`convert` の代表的な利用例として「画像フォーマットの変換」と「画像のリサイズ」の2つを紹介する。

### 画像フォーマット変換

`convert` のよくある使い方の1つは、画像のフォーマットを変換する、というものだ。

例えば、BMP 画像を PNG 形式に変換したいとしよう。
この場合、入力ファイルに BMP 画像の名前、出力ファイルに書き出す PNG 画像の名前を指定すると、変換ができる。
画像の加工はしないので、オペレータはつけなくてよい。

```console
$ convert ./digit.bmp ./digit.png
```

これで、BMP 画像が PNG 形式に変換される。

ImageMagick は、たくさんの画像フォーマットをサポートしている ([一覧](https://imagemagick.org/script/formats.php#supported))。

> **📔 ノート**
>
> ImageMagick は非常に多くのフォーマットに対応しているが、裏を返せば、画像の読み込み・書き出しに関係する脆弱性が生じやすい。
> このため、ImageMagick ではデフォルトで **一部のファイル形式が無効化** されている。
> 
> 例えば、PDF 形式や EPS 形式のファイルを読み書きしようとすると、下記のようにエラーになる。
> 
> ```console
> $ convert ./sunset.png ./sunset.pdf
> convert-im6.q16: attempt to perform an operation not allowed by the security policy `PDF' @ error/constitute.c/IsCoderAuthorized/408.
> ```
> 
> ImageMagick のセキュリティ設定は `policy.xml` というファイルに記載されており、これを書き換えることでエラーを回避できる。
>
> 設定の詳細については[ImageMagick公式ドキュメント](https://imagemagick.org/script/security-policy.php)、ファイル形式以外の設定項目については[クックパッド開発者ブログ](https://techlife.cookpad.com/entry/2016/05/18/180703)、ImageMagick の脆弱性については[yoya氏の考察](https://qiita.com/yoya/items/2076c1f5137d4041e3aa)が詳しい。

### 画像のリサイズ

画像のリサイズ（拡大や縮小）をするのも、`convert` を使って行われることの多い処理のひとつだ。

画像をリサイズするには `-resize` オペレータを使用する。オペレータの後ろに、縮小後のサイズを指定する。

試しに、画像を縦・横 128 ピクセルになるよう縮小してみよう。

```console
$ convert image.png -resize 128x128 thumbnail.png
```

![アスペクト比を維持して縮小した場合の、処理前後の画像](/images/lmle/8-imagemagick/convert_resize_wo_exclamation.png)

正方形ではなく、もとの縦横比を保った縮小画像が出力されてしまった。

実は、縮小後のサイズを`128x128` に指定すると「**アスペクト比を維持したまま**128×128px の枠内に収まるように画像を縮小する」という意味になってしまう。

アスペクト比を無視して128px四方に縮小したい場合、**サイズ指定の最後に `!` をつける**。
また、この場合はサイズをシングルクォート（`'`）で囲む必要がある（その理由については、べつの記事で説明したい）。

```console
$ convert image.png -resize '128x128!' thumbnail.png
```

![アスペクト比を無視して縮小した場合の、処理前後の画像](/images/lmle/8-imagemagick/convert_resize_with_exclamation.png)

これで、意図した通りに画像が縮小された。
なお、サイズ指定の方法はこの他にもいろいろある。[ImageMagick公式ドキュメント](https://imagemagick.org/script/command-line-processing.php#geometry)で詳しく説明されているので、興味のある方は参照してほしい。

## 例題

機械学習の分野で `convert` コマンドを使うケースとして、次のような課題を考えてみよう。

> **❓ 課題**
>
> 次のようなディレクトリ構造をもつ画像データセットがあるとしよう。
>
> ```
> dataset
>   |-thumbnails/
>   |-train/
>   |   |-00000000.JPG
>   |   |-00000001.JPG
>   |   |-00000002.JPG
>   |   | ...
>   |   `-00009999.JPG
>   `-val/
>       |-00010000.JPG
>       |-00010001.JPG
>       |-00010002.JPG
>       | ...
>       `-00010999.JPG
> ```
>
> `dataset/train` と `dataset/val` ディレクトリの下にあるすべての画像ファイルを縮小して、`dataset/thumbnails`ディレクトリに保存したい。
>
> 縮小した画像は幅・高さとも 256px とし、次のように `thumbnails/` の直下に保存したいとしよう。
>
> ```
> dataset
>   |-thumbnails/
>   |   |-thumb_00000000.JPG
>   |   |-thumb_00000001.JPG
>   |   | ...
>   |   `-thumb_00010999.JPG
> ```
>
> どうすればいいだろうか？

「すべての画像ファイルを縮小する」方法は次回の記事で考えるとして、まずは1枚の画像ファイルを 256px 四方に縮小する処理を考えよう。

先ほど紹介したように、 `convert` コマンドの `-resize` オペレータを使えば画像を縮小できる。
例題では幅・高さとも 256px にする必要があるので、アスペクト比を無視するために `!` を使おう。

```console
$ cd dataset/
$ convert train/00000000.JPG -resize '256x256!' thumbnails/thumbnail_00000000.JPG
```

![convert の -resize オペレータを使って縮小した結果の画像](/images/lmle/8-imagemagick/convert_resize_example.png)

もとの画像と比較すると、画像が縮小されてアスペクト比が 1:1 になっているのがわかる。

## まとめ

今回の記事では、ImageMagick を使ってコマンドライン上で画像を加工・編集する方法を学んだ。
コマンドラインを使ったコンピュータ操作は、画像のようなマルチメディアとは縁遠い印象をもつ人もいるかもしれないが、実はCLI環境でも様々なマルチメディアの処理ができる。

今回は1枚の画像に対する処理のしかたを紹介したが、 CLI のツールが真価を発揮するのは、同じ処理をたくさんのデータに対して適用するときだ。
このようなスケーラビリティの高さは、大量のデータを扱う機械学習の現場ではとても役に立つ。

次回は、このような処理を可能にする `xargs` というコマンドを紹介しよう。
`xargs` コマンドを使えば、大量の画像を1つのコマンドで操作できるだけでなく、並列処理による高速化というメリットも得られる。

それでは、次回の記事もお楽しみに！

> **🎯 まとめ**
>
> * **ImageMagick**: コマンドラインから画像処理をするためのツール。`convert` や `identify` などのコマンドを提供する。
> * **`convert`**: 画像を編集するコマンド
>   * `convert input.jpg output.png`  
>     JPEG 形式の画像（`input.jpg`）を PNG 形式（`output.png`）に変換して書き出す
>   * `convert input.jpg -rotate 30 output.png`  
>     画像を時計回りに30度回転して書き出す
>   * `convert input.jpg -resize 256x256 output.png`  
>     入力画像のアスペクト比を保ったまま、256×256px の枠内に収まるように縮小する
>   * `convert input.jpg -resize '256x256!' output.png`  
>     入力画像のアスペクト比を無視して、256×256px に縮小する

> **❓ 練習課題**
>
> * ImageMagick のドキュメントやインターネット上の記事を参照しながら、次のコマンドがどのような処理をするか、説明してみよう。
>   1. ```console
>      $ convert input.png -crop 160x90+30+30 output.png
>      ```
>   1. ```console
>      $ convert input.png -resize '50%' output.png
>      ```
> * 次の2つのコマンドについて、出力される画像にどのような違いが生じるか、考えてみよう。
>   1. ```console
>      $ convert input.jpg -blur 15x5 -bordercolor limegreen -border 5 output.png
>      ```
>   1. ```console
>      $ convert input.jpg -bordercolor limegreen -border 5 -blur 15x5 output.png
>      ```
