---
layout: post
title: TermuxでJupyterLabを動かす方法とその感想
date: 2024-04-05 18:46 +0900
tags:
  - termux
  - android
  - jupyterlab
  - linux
---
## JupyterLabとは
JupyterLabはコンソールからサーバーを立ち上げ、Webブラウザからノートの執筆やPythonなどコードを逐次実行し都度確認できる実験および開発環境です。デモコードのデバックや言語仕様を探る時に重宝します。またコードはipynbファイルに保存されるので対話型環境における途中式を簡便に保存可能でいつでも編集・参照できます。

## インストール
依存関係を一括インストールします。念の為build-essentialも追加します。
```bash
pkg install libzmq binutils build-essential python python-pip python-dev libzmq-dev libcrypt-dev libcrypt zlib pkg-config
```
ついでに`pkg upgrade`でパッケージをアップグレードしておきます。

ここまでの作業で依存関係を整えたので後は
```bash
pip install jupyterlab -U
```

と叩けばインストール完了。

**注意:スマホのシステム要件や通信環境によってインストールがタイムアウトする場合があります。タイムアウトを防ぐには以下の変数と共にコマンドを実行してください**
```bash
PIP_DEFAULT_TIMEOUT=10 pip install jupyterlab
```

## 起動
環境が構築できたので`jupyter lab`コマンドを実行し実体を立ち上げます。![](https://raw.githubusercontent.com/SakuraSystemz/blog/main/IMG/2024-04-05/termux-jupyter.jpg)

JupyterLabは`jupyterlab /path/your/dir`というコマンドライン構文で任意のディレクトリを指定して立ち上げます。ディレクトリが指定されていない場合、JupyterLabはその直下をプロジェクトディレクトリとして扱います。

私の場合noteディレクトリを指定しています。ノート作成できる他、Markdownプレビュー機能も付いいるので数式や化学式もきれいに表示されます。
![](https://raw.githubusercontent.com/SakuraSystemz/blog/main/IMG/2024-04-05/termux-jupyter-markdown-preview.jpg)

またWebブラウザからTerminalとしてTermuxにセッションにバインドする事ができます。個人的にこの機能が非常にありがたいです。Webページも併読できる為ssh接続で接続したサーバーのトラブルシューティングや、作業中にStackOverflowを見ながら修正できるので私としては一番使う機能です。これに関してはTerminalに限った話ではありませんが。
![](https://raw.githubusercontent.com/SakuraSystemz/blog/main/IMG/2024-04-05/termux-jupyter-terminal-kali.jpg)

## その他
JupyterLabはカーネルという単位で多様なプログラミング言語をサポートしています。私はRustやっているので老舗のevcxr_jupyterカーネルをインストールしました。その手順についても記しておきます。

### rustパッケージをインストール
evcxr_replとevcxr_jupyterをインストールするにあたって、cargoとrustコンパイラを揃えます。以下を入力しインストールします。
```bash
pkg install rust
```
インストールが完了次第`cargo install evcxr_jupyter evcxr_repl`を続けて実行します。その後`evcxr_jupyter --install`でカーネルをインストール。これ初期設定は完了。JupyterLabの再起動後、Rustカーネルが反映されます。
![](https://raw.githubusercontent.com/SakuraSystemz/blog/main/IMG/2024-04-05/jupyter-rust.jpg)

あとは思い思いにコードを書くだけです。
![](https://raw.githubusercontent.com/SakuraSystemz/blog/main/IMG/2024-04-05/rust-kernel-demo.jpg)

なおevcxrカーネルとはいえevcxr由来のコマンドを使うことができます。試しに`:help`と入力してみてください。
![](https://raw.githubusercontent.com/SakuraSystemz/blog/main/IMG/2024-04-05/jupyterlab-evcxr-command.jpg)
## 最後に
JupyterLabは多様なカーネルをサポートしていますが、その全てがスマホ環境で動作するとは限りません。例えばGo言語の[gophernotes](https://github.com/gopherdata/gophernotes)カーネルはTermuxではうまく動きませんし、[gonb](https://github.com/janpfeifer/gonb)はAndroid環境をサポートしていません。またC言語カーネルとして有名な[xeus-cling](https://github.com/jupyter-xeus/xeus-cling)はAnacondaが必須なのでTermuxではインストールできません(テストしていませんがProot環境なら動くかもしれません)。

以上の通りすべての言語が動くとは限りませんが、ノートやREPLがブラウザで完結できるのは大変な利便かと思います。私自身勉強の面で大いに助かっています。