# GitHubハンズオン(中級)
## やる事
* Git FlowとGitHub Flowについて
* GitHub Flowを使った開発
  * 複数人での開発方法
  * コンフリクトの解消
* GitコマンドのTipsの紹介
* GitHubを使った開発の注意点やおすすめ

## 注意点
* 以下GitHubハンズオン(初級)の内容を理解している前提で進めます
  * [GitHub-Hands-on](https://github.com/egg-system/GitHub-Hands-on)
* 初級に続き**コマンドライン**から操作をしてもらいます

## Git FlowとGitHub Flow
### 概要
* 複数人で開発する際にルールに沿って行う事で円滑に開発・運用を進めることができます
  * ブランチの運用方法、リリース方法などを決めたものです
* 代表的なルールに`Git Flow`と`GitHub Flow`があります
  * 上記以外に[GitLab Flow](https://qiita.com/tlta-bkhn/items/f2950aaf00bfb6a8c30d)や[GitFeature Flow](https://developers.gnavi.co.jp/entry/GitFeatureFlow/koyama)などがありますが、ここでは上記2つを説明します

| |Git Flow|GitHub Flow|
|:-|:-|:-|
|使うブランチ|・master<br>・develop<br>・feature<br>・release<br>・hotfix|・master<br>・作業用ブランチ|
|メリット|・大規模開発向き<br>・フローが明確に決まっているので作業を把握しやすい(調整しやすい)|・Git Flowを分かりやすくシンプルにしたものなので運用しやすい<br>・何回もリリースがしやすい|
|デメリット|・複雑な運用のため使いこなすのが難しい<br>・リリース回数が多い場合に向かない|・同時に複数のリリースが重なる場合に工夫が必要<br>・確認用の環境が用意しにくい|

### ブランチの運用
* Git Flow

|ブランチ名|元となるブランチ|マージするブランチ|用途|
|:-|:-|:-|:-|
|master|-|-|リリース済みのソースコードを管理する|
|develop|master|-|開発中のソースコードを管理する|
|feature|develop|develop|機能実装やバグ修正などの開発作業を行う|
|release|develop|develop,master|リリース準備作業を行う|
|hotfix|master|develop,master|緊急の修正作業を行う|

* GitHub Flow

|ブランチ名|元となるブランチ|マージするブランチ|用途|
|:-|:-|:-|:-|
|master|-|-|リリース可能な状態のソースコードを管理する|
|作業用ブランチ|mater|master|作業者ごと、機能ごとにブランチを作成してソースコードを管理する|

### 参考
  * [【図解】git-flow、GitHub Flowを開発現場で使い始めるためにこれだけは覚えておこう](https://www.atmarkit.co.jp/ait/articles/1708/01/news015.html)
  * [GitHub Flow](https://gist.github.com/Gab-km/3705015)

## GitHub Flowを使った開発
### 複数人での開発方法
※擬似的に複数人(Aさん、Bさん)での開発を想定して作業をします。
* Aさんの作業用ブランチを作成
```
$ git checkout -b branchA master
```
* ファイルを作成
```
$ echo "my name is A" > a.html
```
* リモートにプッシュ
```
$ git add a.html
$ git commit -am "a.htmlの追加"
$ git push origin branchA
```
* 同様にBさんの作業用ブランチを作成し、リモートにプッシュ
```
$ git checkout -b branchB master
$ echo "my name is B" > b.html
$ git add b.html
$ git commit -am "b.htmlの追加"
$ git push origin branchB
```
* Bさんの作業ブランチ(branchB)でプルリクエストを作成
* 上記で作成したプルリクエストをマージ
* ローカルでmasterブランチに切り替えて、リモートの修正分を取り込む
```
$ git checkout master
$ git pull
```
* ローカルでAさんの作業ブランチに切り替え
```
$ git checkout branchA
```
* masterの修正分をbranchAに取り込む
```
$ git merge --no-ff master
```
* マージについて
  * マージコミットを発生させるオプションです
  * `--no-ff`オプションはNo fast-forwardオプション
  * 基本的に`--no-ff`オプションをつけてマージすることをおすすめします
  * ※マージの際に編集画面が開いた場合は`:wq!`で終了します
* Bさんの作成したファイルをAさんが編集(ファイルの編集は任意のエディタで問題ありません)
```
$ vim b.html
```
* ※別のエディタで編集する場合
```
Macの場合以下コマンドで今のディレクトリをファインダーで表示できます
$ open .

Windowsの場合は以下コマンドでフォルダを表示できます
$ explorer .
```
* 修正分をリモートにプッシュ
```
$ git commit -am "Bさんのファイルを修正"
$ git push origin branchA
```
* Aさんの作業ブランチ(branchA)でプルリクエストを作成し、masterにマージする

### コンフリクトの解消
※擬似的に複数人(Cさん、Dさん)での開発を想定して作業をします。
* Cさんの作業用ブランチを作成し、Bさんのファイルを修正
```
$ git checkout -b branchC master
$ vim b.html
```
* 差分を確認
```
$ git diff
-my name is B and A
+my name is B and A and C
```
* 修正分をリモートに反映
```
$ git commit -am "Cさんの修正"
$ git push origin branchC
```
* Dさんの作業用ブランチを作成し、Bさんのファイルを修正
```
$ git checkout -b branchD master
$ vim b.html
```
* 差分を確認
```
$ git diff
-my name is B and A
+my name is B and A and D
```
* 修正分をリモートに反映
```
$ git commit -am "Dさんの修正"
$ git push origin branchD
```
* Cさんの作業ブランチ(branchC)でプルリクエストを作成し、masterにマージする
* Dさんの作業ブランチ(branchD)でプルリクエストを作成しするとコンフリクトが起きてマージが出来ないことを確認
  * CさんとDさんで同じファイルを修正しているのでどちらの修正が正しい内容か判別がつかないためマージが出来ない
* ローカルでmasterブランチに切り替えて、リモートの修正分(Cさんの修正分)を取り込む
```
$ git checkout master
$ git pull
```
* masterの修正分をbranchDに取り込むとコンフリクトが発生することを確認する
```
$ git checkout branchD
$ git merge --no-ff master
```
```
Auto-merging b.html
CONFLICT (content): Merge conflict in b.html
Automatic merge failed; fix conflicts and then commit the result.
```
* コンフリクトしているファイルを編集(ファイルの編集は任意のエディタで問題ありません)
```
$ vim b.html
```
```
<<<<<<< HEAD
my name is B and A and D
=======
my name is B and A and C
>>>>>>> master
```
* コンフリクトの確認
  * Cさんの修正分とDさんの修正分が表示されているので、それを踏まえてどういった修正をするのが正しいか確認する
  * 今回は以下に修正します
  ```
  my name is B and A and C and D
  ```
* 修正が終わったらリモートにプッシュ
```
$ git commit -am "コンフリクトを解消"
$ git push origin branchD
```
* Dさんの作業ブランチ(branchD)で作成したプルリクエストがマージできるようになっていることを確認してマージする

## GitコマンドのTipsの紹介
### 修正の取り消し
作業用のブランチを作成
```
$ git checkout master
$ git pull
$ git checkout -b branchE master
```

#### 1.commitする前の修正を取り消す
* ファイルを修正
```
$ vim a.html
```
* 状態を確認
```
$ git diff
$ git status -s
```
* 修正の取り消し
```
$ git checkout -- a.html
```
* 修正がキャンセルされていることを確認
```
$ git diff
$ git status -s
```

#### 2.commitしてpushする前の修正を取り消す
* ファイルを修正
```
$ vim a.html
```
* 修正内容をcommitする
```
$ git commit -am "修正"
```
* commit内容を確認
```
$ git log
```
* 直前のコミットを取り消す
```
$ git reset --hard HEAD^
```
* `reset`オプション
  * `--hard`オプション：コミット取り消した上でワークディレクトリの内容も書き換えたい場合に使用(addしたものも取り消す場合)
    * addしたものは取り消さない場合は`--soft`を使う
  * `HEAD^`：直前のコミット
* commitが消えていることを確認
```
$ git log
```

#### 3.pushした修正を取り消す
* ファイルを修正
```
$ vim a.html
```
* 修正内容をcommitする
```
$ git commit -am "修正"
```
* リモートにプッシュ
```
$ git push origin branchE
```
* 取り消したいコミットのハッシュを確認
```
$ git log

commit f7e53bfd1bb0fcf159f0d85943a56c8b1236f5d5 (origin/branchE)
Author: hikarut <ht.hikaru.takahashi@gmail.com>
Date:   Sun Feb 17 15:11:04 2019 +0900

    修正
```
* 取り消したいコミットを指定してrevertする
```
$ git revert f7e53bfd1bb0fcf159f0d85943a56c8b1236f5d5
```
* revertした内容をリモートにpushする
```
$ git push origin branchE
```

#### 4.pushした修正を取り消す(commit自体をなかったことにする) ※非推奨
* 直前のコミットを取り消し
```
$ git reset --hard HEAD^
```
* リモートにプッシュする(エラーになる)
```
$ git push origin branchE
```
* 強制的に上書きでプッシュする
```
$ git push -f origin branchE
```
* `-f`オプション
  * `--force`の省略形
  * コンフリクトが発生する場合でも、強制上書きができる

## GitHubを使った開発の注意点やおすすめ
### コミットメッセージは分かりやすく
* コミットメッセージの書き方にルールはありませんが、どういった修正を行ったのか分かりやすく書けるととても良いです
* howを書くよりは**why**をコミットメッセージに書くと分かりやすいです
* 参考
  * [GitのコミットメッセージにEmoji Prefixを使ってテンションをあげたい話](https://tech.libinc.co.jp/entry/2018/12/05/113103)
  * [よいコミットメッセージ・よくないコミットメッセージ - Misoca開発者ブログ](https://tech.misoca.jp/entry/2017/03/13/132014)
  * [gitにおけるコミットログ/メッセージ例文集100](https://anond.hatelabo.jp/20160725092419)
  * [Gitコミットメッセージの7大原則](http://rochefort.hatenablog.com/entry/2015/09/05/090000)

### プルリクエストは小さく細かく
* プルリクエストは誰かにレビューしてもらう為のものなので自分以外の人が修正点を確認するということを意識しましょう
* 修正したコードやファイルが多いとレビューが大変なので、なるべく細かくプルリクエストを作成することをお勧めします
* 参考
  * [gitとプルリクエストに関して思うことまとめ](https://qiita.com/doilux/items/b5a9abd95ac91e848a5f)

### プルリクエスト作成にはテンプレート機能を使おう
* プルリクエストはよく使うので、フォーマットをテンプレート化すると毎回同じことを書かなくて良いので便利です
* 参考
  * [GitHubのIssue・Pull Requestのテンプレート機能を使おう](https://qiita.com/nyamogera/items/3fe6985b45fbd5377184)
