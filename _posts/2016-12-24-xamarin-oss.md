---
title: "Xamarin.Forms または私は如何にして心配するのをやめてバグを愛するようになったか"
date: 2016-12-24 15:00:00 +0900
layout: post
---
この記事はQiitaで公開されている _初心者向けXamarin Advent Calendar その2_ の16日の記事 __である__ .

[[初心者さん・学生さん大歓迎！] Xamarin その2 Advent Calendar 2016 - Qiita](http://qiita.com/advent-calendar/2016/xamarin-welcome)

[1週間遅れの公開となってしまった]({{site.url}}/advent). 待っていた方がいたら申し訳ない.

初心者向けAdvent Calendarであるものの, この記事ではXamarin.Formsをすでに使ったことがある方を対象とする.
そのため, Xamarin.Formsは何か, といったことは解説しないのでご了承願いたい.

また, 私はWindowsを使う. ほかの環境のユーザーは適宜読みかえてほしい.

なお, これを初心者向けAdvent Calendarに登録したのは, 私自身が初心者だからだ. 8月頃に1,
2週間ほど使い, それっきりである. 詳しい方から見れば改善点があるかもしれないので,
その際は遠慮なく伝えてほしい.

# 思った通り動かない! もしかしてバグ?
これまでXamarin.Formsを使ってきて, 思った通り動かない, バグかも,
といった挙動に遭遇したことはないだろうか. なければそれに越したことはないのだが,
私の場合そのたった数週間のXamarin.Forms体験の中でぶち当たってしまった.
[私はキレた]({{site.url}}/xamarin).

# Xamarin love OSS!
そんなときあなたならどうするだろうか? ここで注目したいのが, Xamarin.Formsはオープンソースであり,
ソースコードがMITライセンスの下で公開されているということだ.

[GitHub - xamarin/Xamarin.Forms: Xamarin.Forms official home](https://github.com/xamarin/Xamarin.Forms)

もちろん, 諦めるのも選択肢の一つではある. バグ修正には手間がかかり,
チームで開発しているのなら修正を共有する必要がある. だが, Xamarin.Formsは自分で直せる.
時間があるのなら一度バグ修正に挑戦してみるのはどうだろうか.
使っているプロダクトの内部を知ることは多少なりとも意味はあるし, 公開されたバグ修正によって,
いざというときは自分で直せるだけの能力があるということも示せるだろう.

# バグレポートをしよう
時間がないという場合でも, 最低限バグレポートすることをお勧めする. Xamarin.Formsのバグは
__Bugzilla__ で管理されている. Bugzillaは, OSSでよく使われているバグトラッカーである.
Webのインターフェイスがあり, そこでバグの閲覧, 報告ができる.

[https://bugzilla.xamarin.com/](https://bugzilla.xamarin.com/)

![Bugzillaの画像]({{site.url}}/assets/2016-12-24-bugzilla.png)

バグを報告する前に, 既に報告されていないか確認しよう. バグレポートは英語だが,
日本の中学校で確実に学んだのなら問題ないはずだ.

# バグを直そう
さて, バグを直そう. でも, その前に準備.

## 準備
### Xamarin Studioをインストールする
Xamarin.FormsにはUnitTestsとUITestsの2つのテストの種類がある.
残念だが, Visual StudioでUnitTestsのテストを実行する方法を確認できなかった. 仕方がないので,
Xamarin Studioをインストールする. しかし, Windowsユーザーにとってはこれが結構ややこしい.
なお, UITestsは環境依存であり, WindowsのテストにはVisual Studioが必要だ.

まず, [Xamarinの公式サイト](https://www.xamarin.com/)に向かう. 右上に'Sign in'とあるので,
これを選択. __'Products'とかは罠なので無視する__. もれなくVisual Studioをインストールさせられてしまう.

![Xamarinの公式サイトのトップページ]({{site.url}}/assets/2016-12-24-web-top.png)

'Sign in'のページに移ったら, 左下に'Create a new account'とあるのでここからアカウントを作成する.

![XamarinのSing inのページ]({{site.url}}/assets/2016-12-24-web-signin.png)

アカウントを作成し, サインインしたら'Dashboard'に向かう.

![サインイン後のXamarinのトップページ]({{site.url}}/assets/2016-12-24-web-top-signedin.png)

'Dashboard'から'Downloads'に向かい, 'View all versions'を選ぶ.
__大きい'Download Xamarin'のボタンは罠だ. 回避せよ.__

![XamarinのDownloadsのページ]({{site.url}}/assets/2016-12-24-web-downloads.png)

すると各OSのプルダウンが現れるので, Windowsを選択する. そして, 'Product Versions'から選ぶ.
__'Universal Installer'も罠だ. 'Download (Recommended)'とか言ってるが無視すべし.__

![すべてのバージョンを表示したXamarinのDownloadsのページ]({{site.url}}/assets/2016-12-24-web-downloads-all.png)

インストーラーを起動させると, Gtk#などを求めてくるようだ. 適宜インストールし, 環境を整えよう.
WindowsのGtk#はMonoのページからダウンロードできる.

[Download \| Mono](http://www.mono-project.com/download/#download-win)

### ソースコードをダウンロードする
次にソースコードをダウンロードしてくる. Xamarin Studioを起動し, 『バージョン管理(N)』から,
『チェックアウト(H)…』を選ぶ. リポジトリの種類はGit, URLは
`https://github.com/xamarin/Xamarin.Forms.git` である. それぞれ指定したら,
『チェックアウト』する.

## バグ修正
ようやくバグ修正に取り掛かれる. ソースコードを読むなり, `Debug.WriteLine`デバッグするなり,
テキトーな方法 (?) でバグを特定しよう. Xamarin.Formsのコードは読みやすいので,
簡単に特定できるはずだ. 特定したら, 状況に合わせてテストコードを追加する.
`.UnitTests`や`.UITests`とあるプロジェクトがそれである. テストコードを追加したら,
テストを行い, 現時点でテストが通らないことを確認, 修正後に再度テストを実行して問題が解決していることを確認しよう.
また, その修正によって既存のテストが壊れないことを確認しよう.

Xamarin.Formsは[.NET Foundationのコーディング規約](https://github.com/dotnet/corefx/blob/master/Documentation/coding-guidelines/coding-style.md)に従う.

変更が完了したら, コミットを作成しよう.

# 使ってみる
実は, この項は完成していない. テストだけでなく, 実際に問題が発生したソフトウェアで修正したXamarin.Formsを使ってみるということが修正の検証には有用だし,
当然, バグ修正するときの最終目標のはずだ. しかしなぜか, この記事を書くために試したビルドが通らなかった.
これではより詳細な情報はかけない. 以下に最低限の情報を記しておく.

まず第一に, ビルドにはVisual Studioを使うこと. そうしなければWindowsのビルドができない.

次に, ドキュメントの生成のために直下にある`Makefile`を用いる, あるいは参考にすること.
Windowsではそのままでは使えない. コマンドは少ないので手で打ってもよいし,
修正を加えてCygwinやMSYSなどを用いてもよい. Windows Subsystem for Linuxを使えば全くの変更なしでも使えるだろう.

最後に, nupkgを生成するために`.nuspec`ディレクトリ以下のspecを用いること.

以上が特に注意すべき点である. 私の環境では, `Xamarin.Forms.Android`のビルドで失敗してしまった.
Xamarin.Androidのアセンブリ参照が狂っているのではないかと疑っているが, 解決していない.
解決策が思い浮かんだ方がいたらぜひ教えてほしい.

# コントリビュートする
コントリビュートする前に, __'Contribution License Agreement'__ (CLA)
に署名する必要がある. 内容は当り障りのないことだが, 短いので目を通しておくとよい.

[https://cla2.dotnetfoundation.org/](https://cla2.dotnetfoundation.org/)

CLAを署名, 提出したら準備は完了だ.

Xamarin.FormsのコントリビューションはGitHubのPull Request機能を用いる.
GitHub上でXamarin.Formsをフォークし, それに変更をプッシュ, そしてPull Requestを発行しよう.
あとはレビューが行われ, 適当と判断されたらマージされる.

# 最後に
ビルドが通らなかったのは非常に惜しい. それができないことには,
NuGetにパッケージをアップロードしたり, NuGet.Serverを使ったりしてチームと修正を共有するとかいうことも書けない.
クリスマスはぼっちなので, ビルドを直す方法をじっくり考えたいと思う.

最後に, 読者の方が楽しいクリスマスを過ごせることを願っている.