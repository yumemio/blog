---
title: "[Colab, Jupyter] シェルコマンドや %env の中で変数を使う"
date: 2023-07-30
draft: false

featured_image: "/images/post/jupyter-variables/hero.jpg"

---

## TL;DR

Colab や Jupyter Notebook の**シェルコマンド・`%env` マジック**で、**Python 変数と環境変数**を正しく扱う方法を紹介します。

説明はいいから概要だけ知りたい、という多忙なエンジニア/データサイエンティストの皆さんは、次の表をご覧ください。

### 表: Colab / Jupyter におけるPython変数・環境変数のアクセス方法

|                                | Python の変数にアクセス | 環境変数にアクセス                  |
|--------------------------------|-------------------------|-------------------------------------|
| シェルコマンド内で  | `!echo {py_var}`{{< br >}}`!echo $py_var`{{< sup >}}†{{< /sup >}} | `!echo $$PATH`                            |
| `%env` コマンド内で    | `%env key={py_var}`{{< br >}}`%env key=$py_var`  | `%env key={{< br >}}{os.environ['PATH']}`              |
| Python のセル内で              | `py_var`                | `os.environ['PATH']`                |

> † **個人的には非推奨**。`py_var` が Python 変数として定義されていない場合、`$py_var` がシェルの変数と解釈されて、紛らわしいことになる。  

再設定頻度が高い環境変数2強の `PATH` と `PYTHONPATH` （個人の感想です）は、次のように設定するとよいです。

* `PATH` の設定
    ```python
    import os
    %env PATH=/path/to/my/bin:{os.environ['PATH']}
    ```
* `PYTHONPATH` の設定（`%env PYTHONPATH` は使わない）
    ```python
    import sys
    sys.path.append('/path/to/my/pylib')
    ```

この記事を Jupyter Notebook に変換したものを [GitHub Gist](https://gist.github.com/yumemio/78aa742bbcaabe6cc2930910e5e0e070) で公開しています。
Colab 環境でコードを動かしてみたい場合にご活用ください。

## まえがき

Jupyter Notebook、皆さんは使ってますか？

Python などのコードと Markdown で書いた文章を1つのドキュメント形式で扱えるツールです。
[Google Colabolatory](https://colab.research.google.com) のドキュメント形式としても採用されているので、見たことがある人も多いと思います。

ところで、Jupyter Notebook の中で環境変数をセットしたり、シェルコマンド内で変数展開したりする場合、注意すべきポイントがあります。

例えば...

* 環境変数を設定するときは、独自のコマンド（`%env`コマンド）を使う
* 環境変数の設定時に Python の変数を利用できる
* Python 変数をシェルコマンド内で使うことができる（しかも記法が2通りある）

などなど。

この記事では、素の Python とも UNIX シェルとも違う、Jupyter Notebook 独特の変数操作についてまとめます。

## はじめに - 用語の整理

Colab やら Jupyter やら、いろいろな用語が出てくるので、ざっくり整理しておきましょう。
そんなの知ってるわい、って人は読み飛ばしてください。

* **Colab**: Python コードを実行できる仮想サーバ。Google が運営。
* **Jupyter Notebook**: コードと Markdown テキストをまとめたファイル（ノートブック）を編集できる**Web アプリ**
* **IPython**: Python シェル (`python3` コマンドで出てくる対話的なやつ) の高機能版

IPython を Web UI で操作できるようにしたのが Jupyter Notebook で、それを Google がホスティングしてくれるのが Colab。 
...と考えれば、この記事を読むうえでは問題ありません。

記事のなかで「IPython では云々」という説明が登場しますが、これは IPython が裏で動いている Jupyter Notebook や、Jupyter Notebook をベースにしている Colab にも当てはまります。

細かいことを言えば、Jupyter Notebook のカーネルは Python 以外に R や Julia も選べるんですが、それは今回は考慮しない、ということで...。

## 環境変数を定義するには `%env` を使う

Jupyter Notebook や Colab を使い始めてしばらく経つと、「環境変数をどうやって定義するのか」という問題が出てきます。

普通は `bash` などのUNIXシェルを立ち上げて...

```console
$ export MYAPP_SECRET=/var/lib/myapp/secret.json
``` 

などと定義すればいいのですが、
ノートブックのセルに書けるのは基本的に Python のコードなので、困るわけです。

[IPython のドキュメンテーション](https://ipython.readthedocs.io/en/stable/interactive/reference.html#system-shell-access)を調べると、`!ls` のように `!` を先頭につけるとシェルコマンドを実行できる、ということがわかります。 
そうすると、`!export my_var=value` で環境変数を定義できそうな気がしますが、これは**誤り**。

[%env](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-env) というマジックコマンド（`%` で始まる IPython の特殊コマンド）を使う必要があります。

> 👍 環境変数を設定するには `%env name=value` を使う（引用符は**不要**）  
>    (`%env name value` もOK)
> 
> 🚫 `!export` では環境変数をセットできない
> 
> 🚫 `!name=value` でシェル変数をセットすることもできない

```python
# ✅ 環境変数を定義するには %env を使う
%env my_config=/content/config.yaml
!env | grep my_config
# （出力）→ my_config=/content/config.yaml

# ❌ !export はだめ
!export my_images="/content/train/"
!env | grep my_images
# → （空）

# ❌ !<name>=<value> もだめ
!my_json="/content/labels.json"
!env | grep my_json
# → （空）
```

なぜ `!export` や `!<name>=<value>` がダメかというと、`!cmd` に書いたコマンドは個別のサブシェル内で実行されるためです。

つまり `!export myvar=value` と定義しても、`export` コマンドの実行が終わると同時にサブシェルが終了してしまうので、設定が引き継がれないわけですね。

## 変数を使う

せっかく定義した変数も、使い方が分からなければ意味がありません。

というわけで、今度は変数展開について見ていきましょう。

### Python 変数展開機能

IPython は、シェルコマンド（`!cmd`）や `%env` マジック内で **Python の変数を展開する** という便利機能をもっています。

書き方は2通りで、`$my_var` と `{my_var}` があります。

...と説明するよりも、実例を見た方が早いですね。

```python
# Python の変数を定義
my_var = "gs://foo/bar"

# {my_var} や $my_var で展開できる
!echo "秘密のファイル: {my_var}"
# → 秘密のファイル: gs://foo/bar
!echo "秘密のファイル: $my_var"
# → 秘密のファイル: gs://foo/bar
```

`{my_var}` 記法の場合、`{my_secret['height']}` や `{129.3 / 1.293 ** 2}` のように式を書くことも可能です。

（`$my_var` では複雑な式の評価はできず、変数の展開しかできない）

> 📔 **シェルコマンドでは、`$my_var` が環境変数として展開されることもある**
> 
> `$my_var` 記法は `bash` のパラメタ展開と被るじゃん、と思ったあなたは鋭い。
> 
> シェルコマンドでは、`my_var` が Python 変数と環境変数の両方で定義されている場合、Python 変数が優先されます。
> 
> Python 側で未定義の変数のみ、環境変数として展開されます。
> 
> ```python
> %env runtime=bash
> !echo "$runtime の勝ち"
> # → bash の勝ち
> 
> runtime = "Python"
> !echo "$runtime の勝ち"
> # → Python の勝ち
> 
> # %env を再定義しても Python 変数が優先
> %env runtime=bash
> !echo "$runtime の勝ち"
> # → Python の勝ち
> ```
> 
> このように、`$my_var` 記法は Python 変数と環境変数のどちらなのか見た目ではわからない、という欠点があります。
> 
> `{my_var}` はこのような曖昧さがないので、個人的には `{my_var}` 記法がオススメです。

### シェルコマンドで環境変数にアクセス: `$$MY_VAR`

シェルコマンドで環境変数にアクセスしたい場合、`$$PATH` のように `$` をエスケープするとよいです。

こうすると、たとえ `PATH` という名前の Python 変数があったとしても、必ず環境変数の `PATH` が呼び出されます。

（そんな紛らわしい変数名をつける人がいるのか、という点は置いておいて...）

```python
PATH = "/path/to/my/secret/video.m4a" # まぎらわしい変数

# ❌ $PATH は Python 変数として展開される場合がある
!echo $PATH
# → /path/to/my/secret/video.m4a

# ✅ $$PATH は必ず環境変数として展開される
!echo $$PATH
# → /opt/bin:/usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/local/sbin:...
```

ちなみに、`${my_var}` のように書くと期待通りに動かないので、避けるのが賢明です。

（Jupyter が `{my_var}` を Python 変数として展開した後に、シェルが `$my_varの値` を展開しようとする、いわゆる「変数名が変数」状態になる）

### `%env` マジックで環境変数にアクセス: `{os.environ['MY_VAR']}`

`%env` マジック内では動作が少し異なり、 `$$PATH` も `$$SHELL` も環境変数としては展開されません。

`%env` の定義時に環境変数を使いたい場合、 Python の `os.environ` から取得するのがベストです。

例えば `MYAPP_ROOT` という環境変数を参照して `MYAPP_LIB` を定義したい場合、次のようにするとうまくいきます。

```python
# MYAPP_ROOT の定義
%env MYAPP_ROOT=/path/to/my_app

# MYAPP_LIB の定義
import os
%env MYAPP_LIB={os.environ['MYAPP_ROOT']}/lib
```

### よくあるケース: `PATH` を通す

この手法を使う典型例が、`PATH` にディレクトリを追加してコマンドを呼び出しやすくする、というケース。
いわゆる「[パスを通す](https://linuc.org/study/knowledge/349/)」ってやつですね。

`bash` だと、次のようなコマンドですが...

```console
$ export PATH=$PATH:/path/to/my/bin
```

Jupyter Notebook でこのコマンドに相当するのが、`os.environ['PATH']` を使った次の書き方です。

```python
import os
%env PATH={os.environ['PATH']}:/path/to/my/bin
```

### `PYTHONPATH` を変更するときは `sys.path.append()` を使う

パスつながりで、Python のライブラリ検索時に使う `PYTHONPATH` についても一言。

**`PYTHONPATH` を `%env` で再定義しても Jupyter セッションは追加したパスを見てくれません**。

`PYTHONPATH` は Jupyter Notebook の立ち上げ時に読み出されるためです。

`PYTHONPATH` にパスを追加するときは、`sys.path.append()` を使いましょう。

```python
# /content/my_lib に inquisitor モジュールを置く
!mkdir -p /content/my_lib
script = """def inquisite():
    print('まさかの時のスペイン宗教裁判！')"""
!echo "{script}" > /content/my_lib/inquisitor.py

# ❌ PYTHONPATH は、Jupyter セッション内で再定義しても意味がない
import os
%env PYTHONPATH=/content/my_lib:{os.environ['PYTHONPATH']}
import inquisitor
inquisitor.inquisite()
# → ModuleNotFoundError: No module named 'inquisitor'

# ✅ sys.path.append() を使う
import sys
sys.path.append("/content/my_lib/")
import inquisitor
inquisitor.inquisite()
# → まさかの時のスペイン宗教裁判！
```

### 未展開の変数が1つでもあれば、展開はされない

余談ですが、**展開できない（未定義の）変数が1個でもあれば、同じ行内にあるすべての変数が展開されません**。

```python
superhero = "バットマン"
villain = "ジョーカー"

# ❌ sidekick が未定義なので、どの変数も展開されない
!echo "{superhero} が {sidekick} の助けを借りて {villain} と戦う物語"
# → {superhero} が {sidekick} の助けを借りて {villain} と戦う物語
```

「`!xargs -I{}`」 の `{}` とか、 「`# このAPIは高額なので注意 $$$`」 というコメントの `$$$` も対象なので、ご注意を。


## まとめ

...という諸々の仕様をまとめたのが、冒頭の表になります。

せっかくなので再掲しましょう。

|                                | Python の変数にアクセス | 環境変数にアクセス                  |
|--------------------------------|-------------------------|-------------------------------------|
| シェルコマンド内で  | `!echo {py_var}`{{< br >}}`!echo $py_var`{{< sup >}}†{{< /sup >}} | `!echo $$PATH`                            |
| `%env` コマンド内で    | `%env key={py_var}`{{< br >}}`%env key=$py_var`  | `%env key={{< br >}}{os.environ['PATH']}`              |
| Python のセル内で              | `py_var`                | `os.environ['PATH']`                |

> † **個人的には非推奨**。`py_var` が Python 変数として定義されていない場合、`$py_var` がシェルの変数と解釈されて、紛らわしいことになる。  

### `PATH` の設定

```python
import os
%env PATH=/path/to/my/bin:{os.environ['PATH']}
```

### `PYTHONPATH` の設定（`%env PYTHONPATH` は使わない）

```python
import sys
sys.path.append('/path/to/my/pylib')
```
