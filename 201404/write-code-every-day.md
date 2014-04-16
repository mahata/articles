[John Resig のブログ記事](http://ejohn.org/blog/write-code-every-day/)に触発されて、僕も (なるべく) 毎日コードを書くようにしようかと考えている。このリポジトリに含める記事も何かの形でコードと関係のあるものにしようと思う。

ところで、前に作ってて作りかけてにしたまま放置していた[webnikki](https://github.com/mahata/webnikki)というソフトウェアの開発を再開しようと考えている。最近は意識してモダンな環境を使うようにしており、かつて「Emacs + Ensime」で書いていたこのソフトウェアも、これからはIntelliJで書いていこうと思う。

この手のIDEの最初の関門はプロジェクトのはじめ方にあると思う。それぞれのIDEに癖がある。IntelliJの場合は次のようにする必要があった。環境は「Play Framework 2.1.x + Scala 2.10.x」である。

* GitHubからプロジェクトのチェックアウト
* Scalaのソースが置かれたディレクトリに移動 (このプロジェクトの場合は`playframework`ディレクトリ)
* 次のようにしてIntelliJのプロジェクトファイル群を作成

```
$ play
play> idea with-sources=yes
```

IntelliJを起動し、「File -&gt; Import Project;」から`playframework`ディレクトリを選択する。続いて「Create project from existing sources」を選択し、既に存在するプロジェクトファイルを上書きしてよいかと聞かれるダイアログでYesを選択する。後は適当にウィザードを進めていけばよい。

今はこのソフトウェアを「Vagrant + Ansible」でセットアップできるようにしている途中で、詳細の部分は変わるかもしれないけれど、IntelliJでプロジェクトをインポートする流れについてはメモをしておきたかったので、本記事を書いている。
