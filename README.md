# GitHubハンズオン(中級)
## やる事
* 複数人での開発方法
* コンフリクトの解消
* その他Tipsの紹介

## 注意点
* 以下GitHubハンズオン(初級)の内容を理解している前提で進めます
  * [GitHub-Hands-on](https://github.com/egg-system/GitHub-Hands-on)
* 初級に続き**コマンドライン**から操作をしてもらいます

## 複数人での開発方法
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

## コンフリクトの解消
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

## その他Tipsの紹介
### 修正の取り消し
#### addする前の修正を取り消す
#### addしてcommitする前の修正を取り消す
#### commitしてpushする前の修正を取り消す
#### pushした修正を取り消す
