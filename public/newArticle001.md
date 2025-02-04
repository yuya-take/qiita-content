---
title: uvを使った環境構築
tags:
  - Python
  - Rust
  - Ruff
  - uv(astral-sh)
private: false
updated_at: '2025-02-04T23:10:25+09:00'
id: 41c8153530d5542bee16
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

Pythonの環境構築には、pip + venv + pyenv や pyenv + poetry など、さまざまな組み合わせがあり、正直めんどくさいと感じることが多いです。uvがオールインワンツールとして発表されてから、私は基本uvしか使わなくなりました。今回はその uv を使ったpythonの環境構築の仕方を見ていこうと思います。

https://docs.astral.sh/uv/

## 環境

| 項目  | 詳細 |
|------|------|
| **OS** | macOS 15.2 |
| **CPU** | Apple M4 Pro |
| **メモリ** | 24 GB |
| **GPU** | Apple M4 Pro (16コア) |

<br>

| 項目  | 詳細 |
|------|------|
| **バージョン管理ツール** | uv |
| **uvのバージョン** | 0.5.23 |

## 準備

必要なものは「uv」それだけ！[こちら](https://docs.astral.sh/uv/getting-started/installation/)の様にインストールします。

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

複数のツールを組み合わせるより簡単ですね！

## プロジェクトの基本構築

### プロジェクトの作成

uvは適当にコマンド打っておけば必要なものが用意できます。まずは以下のコマンドでプロジェクトを作成します。

```bash
uv init test-project

Initialized project `test-project` at `/Users/hoge/work/fuga/test-project`
```

すると、以下の様な構成でフォルダができると思います。

```bash
[0] .
├── [1] .gitignore
├── [2] .python-version
├── [3] README.md
├── [4] hello.py
└── [5] pyproject.toml
```

とりあえすデフォルトでできた`hello.py`を動かしてみよう。`uv run`コマンドで動かせる。

```bash
uv run hello.py

Using CPython 3.12.8
Creating virtual environment at: .venv
Hello from test-project!
```

上の実行を見る通り実行時に`.venv`を勝手に作ってくれる。

### pythonバージョンの変更

次にpythonバージョンを変えてみる。私のデフォルトは3.12だったので3.13にしてみる
バージョンを変える時はuvにインストールされているpythonバージョンを確認して、必要であればインストールしてからピン留めする必要がある。


```bash
# インストール済みのpythonを確認する
uv python list --only-installed

cpython-3.12.8-macos-aarch64-none                 /Users/hoge/.local/share/uv/python/cpython-3.12.8-macos-aarch64-none/bin/python3.12

# 3.9をインストールする
uv python install 3.13

Installed Python 3.13.1 in 1.30s
 + cpython-3.13.1-macos-aarch64-none

# 3.9でピン留めする
uv python pin 3.13 # これで.python-versionの中が勝手に書き換わる

Updated `.python-version` from `3.12` -> `3.13`
```

これでもう一度実行してみると。

```bash
uv run hello.py

\Using CPython 3.13.1
Removed virtual environment at: .venv
Creating virtual environment at: .venv
Hello from test-project!
```

上の実行を見る通りpythonバージョンが変われば勝手に仮想環境も作り直してくれる。

### ライブラリのインストール

次にライブラリをインストールする。適当にnumpyとruffをインストールしてみる。ruffはフォーマッタ&リンタで開発時しか使わないので`--dev`オプションをつけることで差別化できる。
インストールする際は`uv add`コマンドを使用する。

```bash
uv add numpy

Resolved 2 packages in 98ms
Prepared 1 package in 167ms
Installed 1 package in 8ms
 + numpy==2.2.2

uv add ruff --dev

Resolved 3 packages in 5ms
Installed 1 package in 2ms
 + ruff==0.9.4
```

今までpip使ってた人からするととんでもなく早くインストールが終わる。
ライブラリがインストールされると勝手に`pyproject.tomlが変わる。

```toml:pyproject.toml
[project]
name = "test-project"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.12"
dependencies = [
    "numpy>=2.2.2",
]

[dependency-groups]
dev = [
    "ruff>=0.9.4",
]
```

ついでに`hello.py`にnumpyを使ってみる。

```python:hello.py
import numpy as np

print(np.array([1, 2, 3]))
```

実行してみる。

```bash
uv run hello.py

[1 2 3]
```

ruffについては使わないが、uvと同じ会社のツールでとても使いやすい。今回は逸れるので割愛します。

## 最後に

uvは環境構築を楽にしてくれるツールです！特にチーム開発で途中参入者からすれば、簡単に環境構築できコーディングに集中できる最高のツールなのでは。
今回紹介したのはほんの一部で、uvのヘルプを見れば他にもいろいろな機能があります。cargoに似ているのでRustの開発者にもおすすめで、workspace機能を使いこなせばさらに便利になると思います。
まだまだアップデートもしていくみたいなので今後も楽しみです。

## おまけ

uvとの比較として、pip + venv + pyenv での環境構築方法を書いておきます。

```bash
$ mkdir test-project && cd test-project
$ pyenv install 3.11.0
Downloading Python-3.11.0.tar.xz...
-> https://www.python.org/ftp/python/3.11.0/Python-3.11.0.tar.xz
Installed Python-3.11.0 to /home/fuga/.pyenv/versions/3.11.0
$ ls -a
.  ..  .python-version
$ python -V
Python 3.11.0
$ python -m venv .venv
$ source .venv/bin/activate
```
