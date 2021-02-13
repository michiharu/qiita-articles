- Python
- Pipenv
- VSCode

# はじめに

この記事は「Python + Pipenv + VSCode」の開発環境構築のための記事です。

Python歴１年のエンジニアが書いているのでPythonの文化のようなものを汲み取れていないかもしれませんが、その点はご注意ください。

# モチベーション

ソースコードをチームで管理する場合にはパッケージ管理が必要となります。同じ動作環境を整えることはコミュニケーションコストを下げます。

そんな当たり前のことを。。と思いますよね。

ほぼアプリ開発しかやったことがないので私にとって、パッケージ管理は当たり前なのですが、Pythonの用途はアプリ開発に限定されずデータサイエンスやちょっとした作業の自動化にも使われるため、パッケージ管理についての必要性の認識が開発者ごとに違うことに気がつきました。

また、定番のパッケージマネージャーがPython界には存在しない（と私には思える）ことも、開発者ごとにパッケージ管理のイメージのばらつきの原因になっていると思います。

javascriptの場合はnpmやyarnがあり、パッケージ管理を前提としてコードを書き始めることはjavascriptの文化になっていると思います。一方Pythonには有名なパッケージマネージャーとしてpipが存在ますが、npmを使ってきた私にはpipではとても貧弱に感じます。

私は最近、pipenvをチームに導入したところ上手く機能することが分かったので、pipenvを使ったPythonプロジェクトの環境構築方法を書きます。

# この記事で紹介するプロジェクト構成のソース
すぐにpipenv構成のベースプロジェクトが欲しい人はこちらgithubに置いています。

[PythonProjectStarterKit](https://github.com/michiharu/PythonProjectStarterKit) 

グローバルなpipへのPipenvのインストールが完了されている方は、上記リポジトリのクローンから単体テストの実行までをすぐに試せます。導入方法は次の通りです。

```
git clone https://github.com/michiharu/PythonProjectStarterKit.git
cd PythonProjectStarterKit
pipenv install --dev --pre
pipenv run test
```

# Pythonについて

## Vanilla vs Anaconda

標準インストーラーのPythonを"Vanilla Python"というらしいです。Pythonは[公式ページのインストーラー](https://www.python.org/downloads/)を使ってインストールすることを前提に話を進めます。

Anacondaは便利なのですが、便利さゆえに実行環境についての理解が進まないというのが私の意見です。アプリ開発者であればPythonの実行環境の最小構成を理解しておく必要があると思います。

アプリ開発にはVanilla、データサイエンスにはAnacondaと心得ておくと良さそうです。Pythonの環境構築については以下の記事が大変参考になりました。

[Python 環境構築いろいろ - Windows](https://qiita.com/yniji/items/b1b20211846a5a9f189b)

この記事を読んでPython環境の再構築をする場合には、一旦これまで使ってきたライブラリーをテキストファイルに出力しておくことをお勧めします。

```
pip freeze > requirememts.txt
```

## Python環境構築

#### 標準インストーラーのダウンロード

 [Python公式ページ](https://www.python.org/downloads/release/python-383/) から、「Files > Windows x86-64 web-based installer」を選んで、
インストーラーをダウンロードしてください。

#### インストール

インストーラーを実行すると最初に「システム環境変数にpythonへのPathを追加するか？」というチェックボックスがあるので、
必ずチェックしてからインストールしましょう。

#### 確認
コマンドプロンプトを開いて以下を実行します。

```
python --version
```

# pipenvの環境構築

以下、pipenvを使うための手順を説明します。

## インストール
次のコマンドで使えるようにします。

```
pip install pipenv
```

インストールが完了したら`pipenv`とコマンドを叩くと、pipenvで実行可能なコマンド一覧が紹介されます。

※ユーザー権限でインストールする`--user`オプションを紹介している記事があります。オプションを付けてユーザー環境変数にPathを通すことで使えるようになる、という記事を見かけるのですが、何度か試しても`--user`オプションでインストールした場合はシステム環境変数にPathを通さないとpipenvコマンドを使えるようになりませんでした。Python自体がデフォルトでユーザー権限でインストールされているので、pip install pipenvもユーザー権限でインストールされていると理解しています。つまり、`--user`オプションはPythonをシステム全体で使えるようインストールしているケースで有効なオプションではないか、と推測しています。正しい知識をお持ちの方はコメント下さい。

## システム環境変数の設定

pipenvのデフォルトの挙動は、プロジェクトディレクトリとは別の、USERディレクトリの`.virtualenvs`に仮想環境リソースを作成します。
これは仮想環境のLibの確認の際などですぐアクセスできず不便なので、システム環境変数の設定にてプロジェクト内に仮想環境を作成する設定を有効化します。

```
PIPENV_VENV_IN_PROJECT=true
```

以上がプロジェクトを作る準備としての環境構築でした。

# 新規プロジェクト作成方法

## .vscodeの設定

#### 注意
`.vscode`の設定を行うとVSCodeから「～をインストールしますか？」というダイアログが表示されることがあります。このダイアログからインストールすることはお勧めしません。

理由は後述するパッケージ追加方法でなければ、自動で実行されるスクリプトはpipenvではなくpipを使用してグローバルにインストールされるため、Pipfileが更新されません。LinterやFormatterはpipenvでインストールしましょう。

#### `settings.json`ファイルの記述
vscodeでLinterやFormatterの設定を共有するため、`.vscode/settings.json`に次の設定が必要です。

```
{
  "python.venvPath": ".venv",
  "python.pythonPath": ".venv\\Scripts\\python.exe",
  "python.envFile": "${workspaceFolder}/.env",
  "python.linting.enabled": true,
  "python.linting.pylintEnabled": false,
  "python.linting.flake8Enabled": true,
  "python.linting.mypyEnabled": true,
  "python.formatting.provider": "black",
  "python.linting.flake8Args": ["--ignore=E501,W503"],
  "editor.formatOnSave": true
}
```

上記の設定で可能になることは以下の通りです。

* pythonをプロジェクトごとの仮想環境で実行すること
* .envファイルを配置することで、python実行時の環境変数を追加することが出来る
* プロジェクトごとのLinterやFormatterの適用

#### `.env`ファイルについて
python実行時の環境変数を追加するため、.envファイルには以下を記述します。
配置場所はプロジェクト直下です。

```
PYTHONPATH=".\src;"
```
PYTHONPATHは、python実行時にモジュールを探す場所を追加するものです。セミコロン区切りで複数記述することが出来ます。

#### blackについて
Formatterはblackを選択しました。
blackは、自ら「妥協のないPython Formatterです。」(Black is the uncompromising Python code formatter.)と謳っています。
曰く、

> フォーマットについての議論を尽くした設定になっているので、そのまま使ってね。
> もうフォーマットで悩むのやめよう？その方が時間の節約になるし、もっと大切なことにエネルギーが注げるよ。

とのことです。

そのためflake8とルールが競合する場合はblackを優先させることとします。つまりflake8に競合するルールを無視させます。

`python.linting.flake8Args`にはblackとのルール競合を避けるため、flake8の一部のルールを無視する設定を行っています。
例えば、`--ignore=E501`は、flake8の１行最大79文字ルールを無視するための設定です（blackは１行最大88文字）。ここでサンプルとして設定した他にもblackと競合してしまうルールがいくつかあったと思いますが、フォーマット関連でflake8のエラーが出たら都度追加していってください。

## pipenvの導入
以下のコマンドを実行します。Pythonのバージョンはより細かく設定できます。該当バージョンのPythonがインストールされていない場合はエラーになりますので、その場合は標準インストーラーからインストールをお願いします。

```
pipenv --python 3
```

コマンドを実行するとプロジェクトのディレクトリに以下のファイルが追加されます。

* .venv
* Pipfile
* Pipfile.lock

## パッケージ追加
### 実行時に使用されるパーッケージの場合

```
pipenv install [package]
```

### Linterやテストツールなど開発時に使用されるパッケージの場合

```
pipenv install --dev [package]
pipenv install --dev mypy flake8 black isort --pre
```

`--dev`は開発用パッケージとして管理するためのオプションです。`--pre`はプレリリースされているパッケージのインストールを許可するオプションです。blackは"NOTE: This is a beta product"と公式ページに記述していて、preオプションがないとPipfile.lockの更新に失敗した旨のエラーとなります。


## Clone したプロジェクト
Cloneしてきたプロジェクトに`Pipfile`がある場合は以下のコマンドで、`.venv`を生成し、そこ必要モジュールをインストールします。

```
// Pipfileの[packages]に記述されたパッケージを追加
pipenv install

// Pipfileの[dev-packages]に記述されたパッケージを追加
pipenv install --dev --pre
```

## requirements.txtで管理していたプロジェクトでpipenvを有効化
引数rを使うと`requirements.txt`からパッケージをインストールできます。

```
pipenv install -r ./requirements.txt
```

## Pythonプロジェクトの標準的なディレクトリ構成

ソースコードを`src`ディレクトリに配置するのは言語を問わない文化だと思います。
src以下のディレクトリ構成は以下のようにします。

```
src
├── package
│   ├── foo_module.py
│   ├──   :
│   └── xxx.py
└── tests
    ├── __init__.py
    ├── test_foo_module.py
    ├──   :
    └── test_xxx.py
```

test_foo_modules.pyからfoo_module.pyをインポートするには以下のように記述します。
※srcにPythonのpathが通っている前提です。

```
import package.foo_module
```

## 実行・スクリプト登録

テストは以下のコマンドで実行できます。

```
// 一括実行の場合
pipenv run python -m unittest discover src -v
```

testsディレクトリには`__init__.py`を配置しないと上記コマンドではunittestからテストコードが探せないためエラーが発生します。

また、よく使うコマンドはスクリプト登録することが可能です。Pipfileに以下の項目を追加します。

```
[scripts]
test = "python -m unittest discover tests -v"
isort = "python -m isort -m 3 ."
```

この設定により、以下のコマンドでテストが可能になります。

```
pipenv run test
```

２つ目に追加したisortは、importに特化したフォーマッターです。これまでに紹介しているblackはimportには触れないので、importのフォーマットはisortに任せます。

```
pipenv run isort
```

Pythonプロジェクトの環境構築手順については以上です。
