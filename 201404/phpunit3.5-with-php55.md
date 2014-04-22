PHP5.5とPHPUnit3.5を一緒に使うのは困難である。素直にPHPUnitをアップデートできればいいのだが、そうもいかない場合もある。私が経験したのは次の依存関係である。

## 使用しているソフトウェア

* PHP5.5
* Zend Framework 1.x
* PHPUnit3.5

## 依存関係

* PHP5.4(以上)とPHPUnit3.5のカバレッジ出力オプション (`--coverage-html`や`--coverage-clover`など) は同時に使えない
* PHPUnit3.6(以上)とZend Framework 1.x以上は組み合わせられない
    * `Zend_Test_*`クラスが`PHPUnit_*`クラスを継承しており依存関係がある

ここで、Zend Framework 1.xをZend Framework 2.xにアップデートするのはコストが高いので、アップデートを避けて最少の変更でPHPUnit3.5にカバレッジ情報を出力させたい。

## 簡易的な解決方法

実際にカバレッジ出力オプションを付けて得られるエラーは`Class 'PHP_Token_CALLABLE' not found`などである。つまり、`callable`などのPHP5.4以降で導入されたトークンは、PHPUnit3.5では扱えないということだ。なので、これらのトークンの存在をPHPUnitに教える必要がある。PHPUnitが依存している[PHP_TokenStream](https://github.com/sebastianbergmann/php-token-stream)パッケージがこれらのトークンを管理している。

PHPUnit3.5は依存関係でPHP_TokenStream1.0.1をインストールするが、これに含まれる`PHP/Token.php`に次の変更を加えてやると、とりあえずカバレッジオプションが動くようになる。

```
$ diff -u PHP/Token.php PHP/Token.php.old
--- PHP/Token.php       2014-04-21 16:56:16.316804271 -0700
+++ PHP/Token.php.old        2010-10-27 00:11:08.000000000 -0700
@@ -235,9 +235,6 @@
 class PHP_Token_BREAK extends PHP_Token {}
 class PHP_Token_CONTINUE extends PHP_Token {}
 class PHP_Token_GOTO extends PHP_Token {}
-class PHP_Token_CALLABLE extends PHP_Token {}
-class PHP_Token_INSTEADOF extends PHP_Token {}
-class PHP_Token_TRAIT extends PHP_Token {}
 
 class PHP_Token_FUNCTION extends PHP_TokenWithScope
 {
```

上記の例は苦し紛れなので、可能であればPHPUnitをアップデートする方が *間違いなく* 賢明である。
