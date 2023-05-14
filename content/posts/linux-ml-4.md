---
title: "cdコマンドの使い方、相対パスと絶対パスのちがい"
date: 2023-05-14
draft: false

series:
  - 機械学習エンジニアのためのLinux
featured_image: "/images/lmle/4-cd/hero.jpg"

---

この記事は、シリーズ [機械学習エンジニアのためのLinux](/series/機械学習エンジニアのためのlinux) の第4回です。

## `cd` コマンド

[前回の記事](/posts/linux-ml-3) で、**作業ディレクトリ**や**パス**について簡単な説明をした。
今回は、作業ディレクトリを変えるコマンドを紹介し、パスの概念について掘り下げていく。

作業ディレクトリを変更するには、**`cd` コマンド**を使う。
エクスプローラや Finder では、フォルダをダブルクリックしたり、パスバーにフォルダパスを指定したりして作業ディレクトリを変更できるが、`cd` コマンドはそれと同じ役割を担っている。

`cd` には移動先のディレクトリを指定する。基本的な使い方はこうだ。

```
cd <ディレクトリパス>
```

`<ディレクトリパス>` は、ディレクトリ名やパスに置き換えて使う。
`cd` と `<ディレクトリパス>` の間に、半角スペースが空いていることに注意してほしい。

それでは `cd` の具体的な使い方を、実例を交えて見ていこう。

### 1階層下のディレクトリに移動する

いま、ホームディレクトリを開いているとしよう。
`ls` を実行すると次のようになる。

```console
$ ls
snap          テンプレート  ドキュメント  ピクチャ      公開
ダウンロード  デスクトップ  ビデオ        ミュージック
```

この状態で「ドキュメント」ディレクトリを開きたい場合は、次のコマンドを実行する。
これは、エクスプローラや Finder でフォルダを選択して開くのと同じことだ。

```console
$ cd ドキュメント
```

これで、作業ディレクトリが切り替わった。
`pwd` コマンドで確かめてみよう。

```console
$ pwd
/home/yumemio/ドキュメント
```

エクスプローラや Finder と異なり、`cd` で作業ディレクトリを移動しただけではディレクトリの中身は表示されない。
 `ls` コマンドを実行して、「ドキュメント」ディレクトリの中身を表示しよう。
もし「ドキュメント」にファイルがない場合は、GUI上で適当なファイルを作成してからコマンドを実行してみよう。

```console
$ ls
sonnet.txt
```

### `cd -`: 直前の作業ディレクトリに戻る

直前の作業ディレクトリ（この例では `yumemio`）に戻る場合は、"`cd -`" を使う。
この機能はエクスプローラや Finder の「戻る（←）」ボタンに近いが、戻るボタンと異なり **`cd -` は直前のディレクトリ以外の履歴を保持しない**。

つまり、戻るボタンを複数回押すと、過去のディレクトリにさかのぼって戻ることができるが、`cd -` を複数回実行しても、直前のディレクトリとの間を行き来するだけ、ということだ。

試しに `cd -` を複数回実行してみると、「戻る」ボタンとの違いがわかると思う。

```console
$ pwd
/home/yumemio/ドキュメント
$ cd -
$ pwd
/home/yumemio
$ cd -
$ pwd
/home/yumemio/ドキュメント
```

### ディレクトリパスなしの `cd`: ホームディレクトリに戻る

ディレクトリパスを指定せずに `cd` を実行すると、ホームディレクトリに戻ることができる。

```console
$ pwd
/home/yumemio/ドキュメント
$ cd
$ pwd
/home/yumemio
```

### いまの作業ディレクトリより上の階層に移動する

現在の作業ディレクトリより上に移動する場合は、"`cd ..`" を使う。
**"`..`"** は**親ディレクトリ**------ 1階層上のディレクトリ ------ を示す特殊なパスだ。

`..` を使って、ホームディレクトリから上の階層に移動してみよう。

```console
$ pwd
/home/yumemio
$ cd ..
$ pwd
/home
$ cd ..
$ pwd
/
```

最後の例で、`/` というディレクトリに移動した。
このディレクトリはディレクトリ階層の最上位に位置していて、Linux システムのすべてのファイルを格納している。
このディレクトリを **ルートディレクトリ** ということもある。

このほか、"`.`" はディレクトリ自体を示すパスとなる、ということも覚えておこう。

### 2階層以上離れたディレクトリに移動する

ここまでは1階層ずつの移動を紹介したが、`cd` コマンドでは2階層以上離れたディレクトリに直接移動することも簡単にできる。

例えば、 `/` ディレクトリから「ドキュメント」ディレクトリに移動したいとしよう。

`cd home` → `cd yumemio` → `cd ドキュメント` というように3段階で移動することもできるが、次のように一気に移動することもできる。

```console
$ pwd
/
$ cd home/yumemio/ドキュメント
$ pwd
/home/yumemio/ドキュメント
```

> 📔 **ノート: タブ補完**
>
> たいていの環境には**タブ補完機能**が備わっており、これを使うと、さらに高速にディレクトリ間を移動できる。
>
> タブ補完の使い方を説明しよう。
> `cd` コマンドの場合、まずディレクトリ名の一部を入力する。
>
> ```console
> $ cd home/yu
> ``` 
>
> この状態で `Tab` キーを押すと、`home` ディレクトリの直下に `yu` で始まる名前のディレクトリがあるか検索される。
> そして、該当するディレクトリが1個しかなければ、その名前が補完される。
>
> ```console
> $ cd home/yumemio/
> ``` 
>
> `yu` で始まるディレクトリがない場合や、複数該当する場合は、なにも表示されない。
> この場合は `Tab` キーをもう一度押してみよう。
> その名前で始まるディレクトリがなければ、表示は変化しない。
> もし複数の補完候補があるならば、その候補が一覧表示される。
>
> たとえば次の例では、`yukak` と `yumemio` という2つの補完候補が示されている。
>
> ```console
> $ cd home/yu
> yukak/  yumemio/
> ```
>
> 続きを入力して、ディレクトリが1個に絞り込めるようになったら、再度タブ補完を実行すればいい。
> 上の例では、 `yum` まで入力して再度 `Tab` キーを押すと、`yumemio/` が補完される。

親ディレクトリを表す "`..`" を使うと、さらに自在に移動することができる。
例えば、`/home/yumemio/ドキュメント` から `/home/yumemio/ダウンロード` に移動する場合は、次のようにできる。

```console
$ pwd
/home/yumemio/ドキュメント
$ cd ../ダウンロード
$ pwd
/home/yumemio/ダウンロード
```

なお、環境によっては "`...`" で2階層上、 "`....`" で3階層上...のように移動することもできるが、この機能をもたない環境も多い。
詳しくは、別の記事で紹介しよう。

### 絶対パスを使った移動

`..` を繰り返し使えばルートディレクトリ (`/`) まで遡ることができるので、理論上はどんな場所のディレクトリも開くことができる。
しかし、今の作業ディレクトリと全く違う場所にあるディレクトリを直接開きたい場合、この方法は不便だ。

例えば、次のような移動を考えてみよう。

* 移動前のディレクトリ: `/home/yumemio/ドキュメント/blog/posts`
* 移動後のディレクトリ: `/usr/share/nginx/html`

この場合、次のようなコマンドで移動することもできるが、パスが長くなってしまう。

```console
$ cd ../../../../usr/share/nginx/html
```

作業ディレクトリから遠く離れた場所へ移動する場合は、ルートディレクトリからたどったパスを指定するほうが便利だ。
このやり方は、エクスプローラや Finder のパスバーに直接フォルダパスを貼り付けて開く操作に近い。

例えば、先ほどの移動は、次のような短いコマンドに書き直せる。

```console
$ cd /usr/share/nginx/html
```

## 相対パスと絶対パス

ここまで、`cd` コマンドにいろいろな形式でディレクトリパスを指定する例を見てきた。

最後の例で見たような、ルートディレクトリからたどったパスのことを**絶対パス** (absolute path) という。
これに対して、現在の作業ディレクトリからたどったパスのことを**相対パス** (relative path) という。

Linux でディレクトリやファイルの場所を指定する場合、基本的には相対パスと絶対パスのどちらを使ってもよい。
近くのディレクトリに移動する場合は相対パス、遠くに移動するときは絶対パス、というように使い分けよう。

下の図に、ホームディレクトリ (`yumemio`) を作業ディレクトリとした場合の、相対パスと絶対パスの違いを示したので、参考にしてほしい。

![ホームディレクトリから"/"、"ドキュメント"、"/var/www" の各ディレクトリへの絶対パスと相対パス](/images/lmle/4-cd/files-home-dir-tree.png)


## おわりに

今回は `cd` コマンドの使い方や、相対パスと絶対パスの違いについて紹介した。

ここまでの連載で、コマンドラインの基本的な操作方法をエクスプローラや Finder と比較しながら学んできたが、
こんな風に思った読者もいるのではないだろうか。

> **フォルダを開いたり移動したりするだけなら、エクスプローラ/Finderでもできるじゃん。これのどこがすごいんだよ！**

ごもっともである。

というわけで、次回以降は Linux コマンドラインの真価を引き出す「コマンドの組み合わせ（パイプ）」や「入出力リダイレクト」などの機能を取り上げたいと思う。
これらの機能を使えば、GUIツールでは難しい、あるいは不可能な作業を、簡単かつ効率的に実行することができる。

次回以降の連載で、これらのテクニックを具体例を交えながら解説していく。お楽しみに！


> **🎯 まとめ**
>
> * **`cd` コマンド**: 作業ディレクトリ（今いるディレクトリ）を変更するコマンド
>   - **`cd`**: ホームディレクトリに戻る
>   - **`cd -`**: 直前の作業ディレクトリに戻る
>   - **`cd ..`**: 1階層上のディレクトリに移動する
>   - **`cd ../..`**: 2階層上のディレクトリに移動する
>   - **`cd /var/www`**: `/var/www` ディレクトリに移動する
> * 相対パスと絶対パス
>   - **相対パス**: 作業ディレクトリから目的のファイル（ディレクトリ）までのパス
>   - **絶対パス**: ルートディレクトリから目的のファイル（ディレクトリ）までのパス

> **❓ 練習課題**
>
> * 次のようなディレクトリ構造があるとしよう。
>
>   ![ディレクトリ構造図](/images/lmle/4-cd/dirtree-drill.png)
>
>   ユーザ `chiyuris` で作業をしていて、作業ディレクトリが `mnist` のとき、下記のディレクトリに移動するにはどのようなコマンドを使えばいいか、考えてみよう。
>   * `train`
>   * `chiyuris` の下の `datasets`
>   * `chiyuris`
>   * ルートディレクトリ
>   * `backup` の下の `datasets`