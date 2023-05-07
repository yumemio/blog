---

title: "VirtualBox でUbuntu 仮想環境を構築する"
date: 2023-05-04
draft: false

series:
  - 機械学習エンジニアのためのLinux
featured_image: "/images/lmle/2-installation/hero.png"

---

この記事は、シリーズ [機械学習エンジニアのためのLinux](/series/機械学習エンジニアのためのlinux) の第2回です。

## Linux 環境を準備しよう

このシリーズでは機械学習エンジニアに役立つ Linux の使い方を紹介していくが、読者のあなたには、ぜひ実際に Linux 上で作業しながらこのシリーズを読み進めていただきたい。

Linux を PC にインストールする場合、3通りの方法がある。

1. PC 上のデータを削除し、Linux を**クリーンインストール**
1. PC 上のデータを残したまま、Linux を**追加でインストール**
1. **仮想環境**に Linux をインストール

データを消去しても構わないような、使っていない PC が手元にある場合は
「クリーンインストール」をおすすめしたい。

2番目の「Linux を追加でインストールする」方法は、あまり推奨できない。
というのも、初心者にとってはディスクパーティションの操作が難しいため、誤操作によるデータの喪失リスクがあるからだ。
また、将来的に Linux を削除する場合もパーティション操作が必要になるなど、
いろいろと手間がかかる。

そこで、Linux マシンを持っていない読者には、**仮想環境** を使って既存の OS を残したまま Linux を試してみることをおすすめする。

仮想環境とは、専用のソフトウェアを使って既存の OS とは別の OS を同時に動かす技術だ。

![WindowsのVirtualBox内でUbuntuを動作させる様子](/images/lmle/2-installation/ubuntu-installation-in-windows-virtualbox.png)

仮想環境を実現するソフトウェアは、商用・非商用も含めていろいろあるが、ここではフリーで利用可能な
「**VirtualBox**（バーチャルボックス）」を使って、
Linux ディストリビューションのひとつ「**Ubuntu**（ウブントゥ）」をインストールする手順を説明しよう。

## VirtualBox を使って Ubuntu 仮想環境を構築する

### VirtualBox のインストール

まず、仮想化ソフトウェア VirtualBox をインストールしよう。

1. VirtualBox の公式 Web サイト (https://www.virtualbox.org/) にアクセスする。
1. 「Download」リンクをクリックして、ダウンロードページを開く。
![VirtualBox 公式 Web サイトのメイン画面](/images/lmle/2-installation/virtualbox-main.png)
1. 「VirtualBox ... platform packages」の下にあるリンクから、PC の OS に対応するインストーラをダウンロードする。
![VirtualBox 公式 Web サイトのダウンロード画面](/images/lmle/2-installation/virtualbox-download.png)
1. インストーラを起動し、指示に沿ってインストールしていく。

> 📔 **ノート**
>
> インストーラの起動時に、Windows では次のようなエラーが出る場合がある。
> 
> "Oracle VM VirtualBox 7.0.6 needs the Microsoft Visual C++ 2019 Redistributable Package being installed first."
> 
> ![Oracle VM エラー画面](/images/lmle/2-installation/virtualbox-cpp-error.png)
> 
> この場合は、必要なパッケージを Microsoft のホームページからインストールしよう（[参考リンク](https://www.reddit.com/r/virtualbox/comments/10h5rhg/comment/j56neqg)）。
> 
> 1. Microsoft の「Visual C++ 2019 再頒布可能パッケージ」ダウンロードページ(https://learn.microsoft.com/ja-jp/cpp/windows/latest-supported-vc-redist) にアクセスする。
> 1. 「Visual Studio 2015、2017、2019、および 2022」という項目の下にある表から **X64** に対応するリンクをクリックして、インストーラをダウンロードする。
> 1. インストーラを起動して、パッケージをインストールする。
> 
> PC を再起動して VirtualBox のインストーラを再度立ち上げると、正しくインストールできるはずだ。

### VirtualBox の日本語化

インストールのついでに、 VirtualBox の言語設定を日本語にしておこう。

1. インストールした VirtualBox を起動する。
1. 「File」メニュー->「Preferences」を選択して、環境設定画面を開く。
![VirtualBoxの環境設定画面を開く](/images/lmle/2-installation/vb-preferences.png)
1. 「Language」を「日本語」に設定して「OK」ボタンを押す。
![VirtualBoxの言語設定画面](/images/lmle/2-installation/vb-language.png)

これで、メニューなどが日本語で表示される（ところどころ、未翻訳の箇所もあるが...）。

### Ubuntu のダウンロード

次に、Ubuntu のインストーラをダウンロードしよう。

1. Ubuntu の公式 Web サイト (https://jp.ubuntu.com/download) にアクセスする。
1. 「ダウンロード」リンクから、「Ubuntu を入手する」ページを開く。
1. 「Ubuntu Desktop」のLTS版をダウンロードする。
ディスクイメージ（**.iso**）形式のファイルがダウンロードされる。

### 仮想マシンの作成

続いて、VirtualBox 上で Ubuntu の仮想マシンを作成しよう。

1. VirtualBox を起動し「仮想マシン」メニュー→「![新規アイコン](/images/lmle/2-installation/vb_new_icon.png)新規」を選択する。
1. ウィザードが開く。画面下のボタンで、「ガイド付きモード」と「エキスパートモード」を切り替えられる。
今回は「ガイド付きモード」を使おう。
1. 仮想マシンの初期設定をしよう。
    1. 仮想マシンの名前: お好みの名前をつける（`MyUbuntu` など）。
    1. ISOイメージ: ダウンロードした Ubuntu のディスクイメージを選択する。
    1. 「Skip unattended installation」にチェックを入れる。  
入力が終わったら「次へ」ボタンをクリックしよう。
![ガイド付きモード画面](/images/lmle/2-installation/vb_new_guided.png)
1. ハードウェアの設定画面が開く。
仮想マシンに割り当てるメモリとプロセッサの数を選択できる。
お好みで調整して「次へ」をクリックしよう。
![ハードウェアの設定画面](/images/lmle/2-installation/vb_new_hardware.png)
1. 仮想ハードディスクの設定画面が開く。
ここでは Ubuntu に割り当てるディスク容量を調整できる。
PC 自体のディスク容量によるが、20GB〜25GBほどあれば演習用には十分だ。
設定したら「次へ」をクリックする。
![仮想ハードディスクの設定画面](/images/lmle/2-installation/vb_new_hard_drive.png)
1. 設定を確認して「完了」ボタンを押す。
![設定確認画面](/images/lmle/2-installation/vb_new_summary.png)

デフォルトでは画面サイズの設定に不具合があるので、ディスプレイの設定を修正して、解像度を調整できるようにしよう ([参考リンク](https://superuser.com/a/1591205)）。

1. VirtualBox のメイン画面で、作成した仮想マシンを選択し、「ディスプレイ」の下にある「グラフィックスコントローラ」をクリックする。  
![VirtualBox のメイン画面。仮想マシンを選択したところ](/images/lmle/2-installation/vb_main_graphics.png)
1. グラフィックスコントローラの選択画面が開く。デフォルトは「VMSVGA」だが、これを「**VBoxVGA**」に変更する。  
![VirtualBox の「グラフィックスコントローラ」選択画面](/images/lmle/2-installation/vb_graphics_popup.png)

これで Ubuntu を仮想マシンにインストールする準備ができた。

### 仮想マシンに Ubuntu をインストール

次に、仮想マシンを起動してみよう。

1. VirtualBox のメイン画面で、作成した仮想マシンを選択する。
1. 「起動 ![→ボタン](/images/lmle/2-installation/vb_start.png)」ボタンを押すと、仮想マシンが起動する。 

> **📔 ノート**
>
> 仮想マシンの操作には、少し慣れが必要だ。
> というのも、普段使っているOS（「**ホストOS**」という）と、仮想マシン内のOSとの間で、マウスやキーボードを適宜切り替える必要があるからだ。
> 
> 仮想マシン内を操作している間、あなたがキーボードやマウスで入力した内容は仮想マシンの Ubuntu に転送される。
> つまり、仮想マシンの操作中はホストOSの操作ができない。
> 仮想マシンを起動したまま、ホストOS側でなにか作業をしたい場合は、この状態から脱出する必要がある。
> 
> * **ホストOS→仮想マシンへの切り替え**: 仮想マシンが起動している状態で、画面上の仮想マシン内をクリックする。すると、マウスやキーボードの操作が仮想マシンに切り替わり、仮想マシン内の操作ができる。
> * **仮想マシン→ホストOSへの切り替え**: ホストOSを操作したいときは、キーボードの「右`Ctrl`キー」を押す。これで、仮想マシンからホストOSに操作を戻すことができる。
> 
> VirtualBox の画面右下にあるインジケータ（![下向き矢印のアイコン](/images/lmle/2-installation/vb_input_capture.png)）を見れば、仮想マシンとホストOSのどちらを操作しているのかを確認できる。
> 実際に何回かホストOSと仮想マシンの操作を切り替えながら、慣れていってほしい。

続いて、仮想マシンに Ubuntu をインストールしよう。

1. 「GNU GRUB」のメニューが表示されるので、仮想マシン内をクリックして Ubuntu の操作に切り替える。
一番上の「Try or install Ubuntu」をキーボードの矢印キーで選択して、Enter キーを押そう。
![GRUBメニュー画面](/images/lmle/2-installation/grub.png)
1. しばらく待つと、Ubuntu インストーラが起動する。
画面サイズが小さすぎる場合は、VirtualBox メニューの「表示」→「仮想スクリーン1」でサイズを調整しよう。
![ようこそ画面（デフォルトでは英語）](/images/lmle/2-installation/installer_welcome_en.png)
1. 画面左のメニューを使って言語を日本語に設定し、「Ubuntu をインストール」を選択する。
![ようこそ画面（言語として日本語を指定した後）](/images/lmle/2-installation/installer_welcome_ja.png)
1. キーボードレイアウトの設定画面に移る。
一般的なキーボード（日本語106/109Aキーボード）を使っている場合は「Japanese (OADG 109A)」を選択して、「続ける」をクリックする。
![キーボードレイアウトの設定画面](/images/lmle/2-installation/installer_keyboard.png)
1. ソフトウェアのインストールに関する設定画面に移る。
デフォルトの設定のまま「続ける」をクリックする。
![ソフトウェアの設定画面](/images/lmle/2-installation/installer_software.png)
1. 「インストールの種類」画面に移る。「ディスクを削除して Ubuntu をインストール」を選択する。
「ファイルがすべて削除されます」と表示されるが、仮想マシンのディスクがフォーマットされるだけで、仮想マシンの外側にあるホストOSには影響しないから、安心してほしい。
![インストール先ディスクの設定画面](/images/lmle/2-installation/installer_disk.png)

これで、インストールが始まる。

処理のあいだにログイン用のアカウントを設定できるので、画面の指示に従って設定しよう。
![ユーザアカウント設定画面](/images/lmle/2-installation/installer_user.png)

インストールが完了すると、自動的に再起動する。

> Please remove the installation medium, then press Enter.

というメッセージが表示されるので、Enterキーを押して処理を続けよう。

起動すると、ログイン画面が表示されるので、設定したアカウントでログインしよう。
![Ubuntu のログイン画面](/images/lmle/2-installation/ubuntu_login.png)

> 📔 **ノート**
>
> インストール終了後、しばらく待っても仮想マシンが起動しない場合は、グラフィックスコントローラの設定を「VMSVGA」に戻してみよう。
> 
> まず、仮想マシンの電源を切る（ホストOSの操作に切り替えて、「仮想マシン」メニュー→「ACPIシャットダウン」をクリック）。
>
> その後、VirtualBox のメイン画面から仮想マシンを選択し、「グラフィックスコントローラ」をクリックして「VMSVGA」に設定してから、仮想マシンを再び起動すると、正しく起動するはずだ。
> ![VirtualBox のメイン画面。仮想マシンを選択したところ](/images/lmle/2-installation/vb_main_graphics.png)
>
> この設定をすると、画面サイズが小さくなってしまう場合がある。
> サイズを調整するときは、次の手順を実行しよう。
>
> 1. Ubuntu にログインし、画面右上の管理メニューから「設定」アプリを開く。
> ![Ubuntu の画面右上の管理メニューにある「設定」を示したスクリーンショット](/images/lmle/2-installation/ubuntu_config.png)
>
> 1. 「ディスプレイ」タブを開いて、「解像度」の設定を変更する。
> ![設定アプリのディスプレイタブを開いて、解像度の項目を一覧表示したスクリーンショット](/images/lmle/2-installation/ubuntu_config_display_resolution.png)


これで、仮想マシン上で Ubuntu が使えるようになった。お疲れ様でした！
![Ubuntu のデスクトップ](/images/lmle/2-installation/ubuntu_logged_in.png)

次回以降は、いよいよ Linux コマンドラインの学習に入っていく。

コマンドライン操作はハードルが高いと思われがちだけど、いくつかのコマンドを覚えれば活用の幅がぐっと広がる、という魅力をもっている。
少しずつ覚えていきながら、Linuxのスキルを一緒に磨いていこう！

