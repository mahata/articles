IntelliJの初期設定は簡単ではないと思う。MacでIntelliJを使い、Scalaコードを書き始めるまでに苦労したことをメモしておく。MacではHomebrewを使ってScalaをインストールするものとする。具体的なバージョンは次の通り。

* Mac 10.9 (Mavericks)
* IntelliJ 13.1 (Community Edition)
* Scala 2.10.4

IntelliJでScalaドキュメントを参照するためには、Scalaを`--with-docs`オプション付きでインストールする必要がある。次のようにする。

```
$ brew install scala --with-docs
```

この状態で新規にScalaプロジェクトを作成するとき、`User Scala distribution`に`/usr/local/Cellar/scala/2.10.4/idea`を指定する。

これでScalaの標準ライブラリはドキュメント付き(補完付き)で使えるようになる。
