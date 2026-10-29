# 授業で使える OpenTofu ラボ: plan、apply、state で学ぶインフラストラクチャ・アズ・コード

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml)

インフラストラクチャ・アズ・コードを教えるための、すぐ使える **OpenTofu ラボ**です。学校が生徒一人ひとりに専用のマシンとブラウザー上のターミナルを提供する [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)から提供しています。生徒ごとに **OpenTofu** が入った Ubuntu **VM** と、Terraform 形式のファイルが入った 3 つのフォルダーが用意されます。生徒は**変数**、**出力**、**count**、**for_each** を使った小さなプロジェクトを書き、変更後の**プラン**を読み、**state**（状態）を確かめ、**壊れたプロジェクトを直します**。生徒のコンピューターには何もインストールしません。OpenTofu は無料のオープンソースです。

## 生徒が行うこと

ラボは `~/tofu-lab` に 3 つのフォルダーを置き、ホームフォルダーに 6 つのパートで何をするかを説明する `README.md` を置きます。答えは書かれていません。

1. `hello`: 最初の `tofu init`、`tofu plan`、`tofu apply` と、残される state ファイル。
2. `hello`: 変数を追加し、出力を変え、変更が更新なのか置き換えなのかを確かめる。
3. `campus`: 何もないところからプロジェクトを書く。デフォルト値つきの変数 3 つ、サーバー名ごとに `for_each` で作る `terraform_data` リソース、教室ごとに `count` で作るリソース、その両方を読む summary リソース、出力 2 つ。`tofu fmt` でファイルが変わってはいけない。
4. `campus`: サーバーのリストを変え、プランだけを見て、何が削除され、何が作られ、何がそのまま残るかを答える。
5. `hello`: `tofu state list` で state を読み、それを削除して、プランがすべてを作り直そうとするのを見る。
6. `fix`: 間違いが 3 つあるプロジェクト（型が違う値、存在しない変数、互いを永遠に待つ 2 つのリソース）を `tofu validate` で見つけて直す。直ったら `tofu apply -var pods=7` が 7 pods と報告しなければならない。

プラットフォームの基礎レッスン「OpenTofu Basics」も、このラボで進められます。

## 採点のしくみ

このラボには**チェック**があります。提出された VM を、教員が中身を読まなくても採点するスクリプトです。保存されたマシンのコピーを新しく起動して実行します。生徒の `campus` と `fix` のフォルダーを別の場所にコピーするので、生徒自身の state は影響せず、プロバイダーとプロビジョナーは受け付けません。`tofu validate` と `tofu fmt -check` を実行します。`campus` については、**誰も見たことのない値を選び**（ランダムなサイト名と、チェック独自のサーバーと教室）、plan と apply を行って state を確かめます。サーバー名ごとに 1 つ、入力がサイト名とサーバー名であること、教室が 1 から始まる番号であること、値が直接書かれたものではなくリソースを読む出力 `summary`、そして出力 `server_names` です。次にサーバーを 1 つ外して 2 つ追加し、もう一度 plan を実行して、外した 1 つだけが削除され、追加した 2 つだけが作られることを求めます。apply したあとに変更が残っていないことを確かめ、destroy します。最後に `fix` が、検証に通り、`pods=7` で apply でき、3 つのリソースを保ち、変数に従うことを確かめます。出力の 1 行目に、何が正しくないかが表示されます。

チェックがしないこと: ラボからは本物のクラウドに届かないので、本物のクラウドには一切接続しません。また、自分の VM で root 権限を持つ生徒なら、OpenTofu 自体を改ざんすることは可能です。これは授業のためのチェックであり、証明ではありません。

## ファイルの内容

`tofu-lab.lab.yaml` は、プラットフォームがそのまま実行する **VM ラボ定義**一式です。

| キー | 内容 |
| --- | --- |
| `cpus`、`memory`、`disk` | 生徒ごとの VM のサイズ: CPU 1 個、1024 MB、10 GB |
| `open` | ブラウザーで開くポート: なし。ラボはターミナルです |
| `setup` | 各生徒の VM の初回起動時に root で 1 回だけ実行されるスクリプト。Ubuntu のアーカイブからいくつかのツールをインストールし、GitHub のリリースから OpenTofu 1.13.2 をダウンロードして、固定された SHA-256 と照合してからインストールします。続いて 3 つのフォルダー、補助コマンド `tofulab`（`sudo tofulab install` と `sudo tofulab reset`）、README を書き込みます |
| `check` | 採点スクリプト。新しく起動した後、保存されたディスクのコピー上で root として実行されます。終了ステータスが結果で、`0` なら合格です |

## プラットフォームで使う

**クラスの課題として配布するには:**

1. モジュールで課題を追加し、**Ubuntu VM ラボ**のカードを選びます。
2. OpenTofu のサンプルを押すか、`tofu-lab.lab.yaml` の内容を貼り付けます。サンプルを使うとテキストが置き換わりますが、元に戻すで自分のテキストに戻せます。
3. 公開して、まず自分用のコピーを試します。初回の起動は OpenTofu のダウンロードのため 1、2 分かかります。生徒ごとに専用の VM が用意され、セッションをまたいで保たれます。
4. ほかの課題と同じように配布します。提出されたら、クラス一覧の生徒の行でチェックを実行します。保存された生徒のマシンのコピーが起動し（保存し直されません）、合格したかどうかと出力が表示されます。

詳しくはヘルプマニュアルの[このラボのページ](https://mitsurutech.co.uk/ja/help/lab-tofu-lab)、[Ubuntu VM ラボのガイド](https://mitsurutech.co.uk/ja/help/labs)、[サンプルラボのページ](https://mitsurutech.co.uk/ja/help/sample-labs)をご覧ください。

学校のアカウントはまだですか。[最初の 1 年間は無料](https://mitsurutech.co.uk/ja/pricing)で、生徒 50 人まで利用できます。[登録](https://mitsurutech.co.uk/ja/signup)にはメールアドレスだけが必要です。

## ファイルの検証

push のたびに `.github/workflows/check.yml` が、ファイルが上記の 5 つのキーを持つ有効な YAML であること、`setup` と `check` がそれぞれプラットフォームの 16 KB の上限に収まっていることを確認します。VM は起動しません。それはプラットフォームのマシンで行われます。

## ラボでできないこと

VM から到達できるのは Ubuntu のパッケージアーカイブ、PyPI、npm、GitHub だけで、それ以外には接続できません。そのため、**プロバイダーはダウンロードできず、本物は何も作られません**。AWS や Docker などのプロバイダーは、VM から届かないレジストリにあります。ラボでは OpenTofu に組み込まれた唯一のリソース `terraform_data` を使います。これは値を保存して返すだけですが、変数、出力、`count`、`for_each`、依存関係、plan、apply、destroy、state は本物です。生徒の README にはプロバイダーのリソースブロックが読むための文章として載っているだけで、ラボがそれを実行することはありません。OpenTofu 自体は GitHub からダウンロードされますが、生徒の VM が GitHub に届くのは無料アカウントがある場合だけです。ビジターの VM ではインストールできず、その場合チェックは OpenTofu がないと表示します。

## このラボについて

[Mitsuru Technologies](https://mitsurutech.co.uk/ja) が [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)のために作りました。学校向けに、Python、Java、Go、Node、SQL、Linux をブラウザーで学べ、コンテナーラボ、VM ラボ、GitHub からデプロイする Web アプリ、自動採点のクイズも備えています。アカウントなしでコードを試せる[プレイグラウンド](https://mitsurutech.co.uk/ja/playground)もあります。

同じ提供元のほかのラボ: [lab-netlab](https://github.com/mitsurutech/lab-netlab)、[lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop)、[lab-dvwa](https://github.com/mitsurutech/lab-dvwa)、[lab-webgoat](https://github.com/mitsurutech/lab-webgoat)、[lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin)、[lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea)、[lab-sample](https://github.com/mitsurutech/lab-sample)、[lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab)、[lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab)、[lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab)、[lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab)、[lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab)、[lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles)、[lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics)。

## ライセンス

この作品は [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) でライセンスされています。Mitsuru Technologies Ltd のクレジットを表示し、商用に使わず、これをもとに作ったものを同じライセンスで共有するなら、学習と授業のために共有・改変できます。全文は `LICENSE` ファイルにあります。

[OpenTofu](https://opentofu.org)（MPL-2.0）は VM の起動時にプロジェクトの GitHub リリースからダウンロードされます。ここでは何も配布していません。
