---
title: "Hello Linux!: ターミナル、lsコマンド、pwdコマンド"
date: 2023-05-07
draft: false

series:
  - 機械学習エンジニアのためのLinux
featured_image: "/images/lmle/3-ls/hero.jpg"

---

この記事は、シリーズ [機械学習エンジニアのためのLinux](/series/機械学習エンジニアのためのlinux) の第3回です。

## ターミナル

コマンドラインを用いた Linux の操作に欠かせないのが、「**ターミナル**」だ。

ターミナルは、**コマンドを入力してコンピュータを操作できるアプリケーション** だ。
Windows や Mac OS の GUI からファイルを表示・編集したりインターネットにアクセスできるのと同様に、
ターミナルからもファイルやフォルダの管理、メールの送受信など、様々な作業ができる。

> ### 🌴コラム: ターミナルの語源
>
> テキストベースでコンピュータを操作するソフトウェアが「ターミナル」と呼ばれているのには、歴史的な背景がある。
>
> 文字入力だけでコンピュータを操作する**CLI**は、少なくとも1960年代にさかのぼる。
> これは、GUIを搭載したコンピュータが登場する20年以上前のことだ。
> 現代の私たちはそれぞれ PC やスマートフォンを所有できるが、初期のコンピュータは高価で大型だったので、個人が1台ずつ使うのではなく、大きなコンピュータを組織ごとに1台（あるいは数台）買ってみんなで共有する、という使い方が一般的だった。
>
> このときに登場したのが、「**ターミナル（端末）**」とよばれる機械だ。
> 物理的な機械としてのターミナルは、次の写真のような形をしている。
> 
> ![VT100ターミナル (Author: Jason Scott)](/images/lmle/3-ls/DEC_VT100_terminal_cc-by_Jason_Scott.jpg)
>
> （Author: Jason Scott, CC-BY, from [Wikimedia Commons](https://en.wikipedia.org/wiki/File:DEC_VT100_terminal.jpg)）
> 
> ターミナルはディスプレイとキーボードを備えたデバイスで、現代のパソコンと同じような見た目をしているが、計算やファイル処理の機能はない。
> 実際の計算や処理をしているのは大きいマシン（**メインフレーム**）のほうで、ターミナルはユーザがタイプした内容をメインフレームに送信したり、メインフレームの計算結果を画面に表示したりする、いわば入出力の役割だけを担う。
> そのぶんターミナルは安いので、企業や大学では1台のメインフレームに多数のターミナルを接続して、複数のユーザが同時に作業できるようにしていた。
>
> つまり、各ユーザがメインフレームにアクセスするための端末（ターミナル）として機能するので、「ターミナル」という名前がついたわけだ。
> 
> その後の技術発展によってコンピュータ自体が安くなり、1人1台のコンピュータが持てる時代になった。
> このため、メインフレームにターミナルをいくつも接続する、という使い方はあまり見られなくなった。
> しかし、CLIでコンピュータを操作する環境を「ターミナル」と呼ぶ習慣は残り続けた。
>
> そういうわけで、CLI 環境を提供するアプリは今でも「ターミナル」と呼ばれている。

それでは早速 Linux マシンを起動して、ターミナルを開いてみよう。ここでは Ubuntu を例に説明する。
手元に Linux 環境がない場合は、[前回の記事](/posts/linux-ml-2) を参照して、Ubuntu の仮想マシンを作ってほしい。

Ubuntu の場合、画面の左下にある ![3個x3個の点](/images/lmle/3-ls/ubuntu-apps-launcher-icon.png) アイコンをクリックして、アプリランチャーを開いて...

![デスクトップ右下の「アプリランチャー」アイコン](/images/lmle/3-ls/ubuntu-desktop-apps-launcher.png)

「端末」と検索すると、ターミナルが出てくる。

![アプリランチャーで「端末」を検索した画面](/images/lmle/3-ls/ubuntu-apps-terminal.png)

「端末」アプリを起動すると、次のようにほとんど真っ黒な画面が表示されるはずだ（環境によっては白い背景に黒文字など、色合いが異なる場合もある）。

![Ubuntu ターミナルの画面](/images/lmle/3-ls/ubuntu-terminal.png)

まずは、この画面から見ていこう。

## プロンプト

開いた画面をよく見ると、左上になにか文字が表示されていると思う。
私の Ubuntu 環境では、このように表示される。

```bash
yumemio@vanilla:~$ 
```

表示される文字は環境によって異なるが、ほとんどの場合、最後がドル記号（`$`）かシャープ（`#`）で終わっているはずだ。

この文字は、「ユーザが入力するのを待っている」という状態を表している。
ユーザの行動を促す（prompt）という意味で、この文字を「**プロンプト**」という。

プロンプトには、ユーザ名やLinuxマシンの名前などの情報がコンパクトに表示されている。
たとえば、例として挙げたプロンプトからは次のことがわかる:

* ユーザ名は `yumemio`
* Linux マシンの名前（ホスト名）は `vanilla`
* いま開いているフォルダは「`~`（ホームディレクトリ）」

ターミナルの基本的な使い方は、Windows の「メモ帳」や Mac の「テキストエディット」のようなテキストエディタと少し似ている。

1. 文字を入力すると、打った文字が表示される。
2. 左右の矢印キーでカーソルを動かせる。
3. 入力を間違えたときは、BackSpace キーで文字を取り消せる。

ただし、いくつか重要な違いもある。

1. **編集できる行**: テキストエディタは複数行を編集できるが、ターミナルでは基本的にプロンプトが表示されている行しか編集できない。他の行に表示された過去のコマンドや、コマンドの実行結果は編集できない。
2. **Enter を押したときの処理**: Enter キーを押すと単純に改行されるだけでなく、入力したコマンドが実行され、実行結果が表示される。
3. 自分の打った文字は BackSpace で削除できるが、プロンプトは削除できない。

![ターミナルとテキストエディタの比較](/images/lmle/3-ls/terminal-vs-text-editor.png)

それでは、実際にコマンドを入力してみよう。

## `ls` コマンド

最初のコマンドは `ls` だ。
キーボードで `ls` と打ってみよう。
次のように、`$` (または `#`) 記号の後ろに `ls` と表示されるはずだ。

```bash
$ ls
```

この状態で Enter を押すと、`ls` コマンドの処理が実行される。
表示される内容は環境によって異なるが、私の環境では次のように表示された。
あなたの環境では、どうだろうか。

```bash
$ ls
snap          テンプレート  ドキュメント  ピクチャ      公開
ダウンロード  デスクトップ  ビデオ        ミュージック
$
```

> **📔 ノート**
>
> Linux の解説書やインターネット上の Q&A サイトなどでは、ユーザが実行するコマンドの前にプロンプト記号（`$` または `#`）をつけて、コマンドの出力と区別することが多い。
> この記事でも、その慣例に従っている。
>
> 注意してほしいのは、実際にコマンドを実行するときは、**プロンプト記号の後ろの部分だけを入力する** ということだ。
>
> たとえば、解説書や Q&A に、下記のコマンドが示されていたとしよう。
>
> ```bash
> $ ls
> ```
>
> このコマンドを実行するときは、最初の `$` を除いた `ls` だけを入力して Enter を押せばいい。

`ls` は、**ディレクトリの中身を表示する**コマンドだ。
「ディレクトリ」というと小難しそうだが、Windows や Mac でいうところの「フォルダ」と同じ意味だ。

読者のあなたは、Windows のエクスプローラや Mac OS の Finder でフォルダの中身を見る操作をした経験があると思う。
それと同じように、Linux のコマンドラインでは `ls` を使って、ディレクトリ内のファイルや別のディレクトリを見ることができる。

![GUI でディレクトリを開く様子と、ls コマンドでディレクトリの内容を表示する様子を並べたスクリーンショット](/images/lmle/3-ls/ls-vs-files.png)

さきほどの例では、`ls` コマンドを実行したことで、**作業ディレクトリ**の中にあるファイルやディレクトリが表示された。

## 作業ディレクトリ

「**作業ディレクトリ**」とは、ユーザが「今いる」ディレクトリのことだ。
作業ディレクトリは、ファイルやディレクトリを操作するときの基準となる場所になる。

エクスプローラや Finder を起動すると、ユーザは自分のユーザ名がついたフォルダや「ドキュメント」フォルダなど、どこかのフォルダを開いた状態でスタートする。
そして、フォルダ内にあるファイルや別のフォルダをダブルクリックすると、そのファイルを開いたり、別のフォルダに「移動」して中身を見ることができる。

これと同じように、ターミナルを開くとユーザはどこかのディレクトリを開いた状態になる。
大抵は**ホームディレクトリ**という、ユーザ名のついたディレクトリが開かれる。
この、ユーザがいま開いているディレクトリのことを「作業ディレクトリ」という。

先ほど示した `ls` コマンドの実行結果は、ユーザがいる作業ディレクトリ（ホームディレクトリ）の中身を表示したものだ。
つまり、私のホームディレクトリには `ダウンロード` や `デスクトップ`、`ドキュメント` などのディレクトリがある、ということになる。

## `pwd` コマンド: 作業ディレクトリを表示する

では、自分がいるディレクトリ、つまり作業ディレクトリがどこなのかを知るにはどうすればいいだろうか。

エクスプローラや Finder では、自分のいるフォルダがウィンドウ内に表示される。
これと同様に、Linux では `pwd` というコマンドを使って、作業ディレクトリを表示することができる。
実際に `pwd` コマンドを実行してみよう。

```bash
$ pwd
/home/yumemio
```

`pwd` コマンドを使うと、作業ディレクトリの場所が**パス**として表示される。
Windows や Mac でも、フォルダやファイルの場所を `\` や `/` で区切ったパスを使って表すことがあると思う。

* Windows のファイルパス
    ```
    C:\Users\yumemio\ドキュメント\制御工学特論1\中間レポート.docx
    ```
* Mac のファイルパス
    ```
    /Users/yumemio/Documents/総務/ISMS/監査報告書_2022.pdf
    ```

Linux では、Mac と同様にディレクトリ名ごとに `/` で区切ったパスを使う。

* Linux のファイルパス
    ```
    /home/yumemio/ドキュメント/ブログ/images/title.png
    ```

先ほど実行した例では、カレントディレクトリの場所は `/home/yumemio` と表示されていた。
つまり、私はいま `home` ディレクトリの中にある `yumemio` を開いている、ということになる。

![/home/yumemio のディレクトリツリーを、ファイル管理ソフトで表示した様子](/images/lmle/3-ls/files-dirtree-yumemio.png)

## おわりに

ここまでで `ls` と `pwd` という2つのコマンドを学んだ。
また、作業ディレクトリやホームディレクトリなどの概念を、Windows や Mac などのOSと比較しながら紹介した。

次の記事「ファイルパス」では、パスについて詳しく紹介する。
パスを正しく理解することは、コマンドライン操作を使いこなすうえで欠かせないので、ぜひ読んでほしい。

> **🎯 まとめ**
>
> * **ターミナル**: 文字を使ってコンピュータを操作するアプリケーション
> * **ディレクトリ**: ファイルをまとめて整理するもの。Windows や Mac OS の「フォルダ」と同じ意味
> * **`ls` コマンド**: ディレクトリの中身を表示するコマンド
> * **作業ディレクトリ**: ユーザが「今いる」ディレクトリ。エクスプローラや Finder で「いま開いている」フォルダと同じ意味をもつ。
> * **ホームディレクトリ**: ターミナルを開いたときに初期状態で開かれる、ユーザ名のついたディレクトリ。
> * **`pwd` コマンド**: 作業ディレクトリを表示するコマンド

> **❓ 練習課題**
>
> * Linux マシンを使って、GUI 上での操作がターミナルでのコマンド実行結果に反映されることを確かめよう。
>   1. ファイル管理ソフト（Ubuntu なら「ファイル」アプリ）を立ち上げる。
>   1. ホームディレクトリを開いて、`テスト` という名前のディレクトリを作成する。
>   1. ターミナルを開いて `ls` コマンドを実行し、「テスト」ディレクトリが表示されることを確かめる。
> * 次のコマンドや概念について、それぞれ Windows や Mac のどのような機能・概念に相当するか、説明してみよう。
>   * `ls` コマンド
>   * `pwd` コマンド
>   * ホームディレクトリ