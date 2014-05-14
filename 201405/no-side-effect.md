副作用なしでWSSEリクエストヘッダをパースするScalaコードについて考えたので、それについてまとめる。

次の入力値 (ここでは`input`) から、Username、PasswordDigest、Created、Nonceを抜き出せればよい。

```
val input = """Username="uname", PasswordDigest="oCkDT0u3nv/SiC1amR2pq7JxTxw=", Created="2014-05-10T10:08:43-07:00", Nonce="OEE2WXhoZ252elFrN2NwY1lDVjIzdUhmYjk4PQ==""""
```

## ナイーブな例 (副作用あり)

深く考えずに手続き型プログラミングに慣れた人間がプログラムを書くと、次のようになるのではないかと思う。

```scala
val input = """Username="uname", PasswordDigest="oCkDT0u3nv/SiC1amR2pq7JxTxw=", Created="2014-05-10T10:08:43-07:00", Nonce="OEE2WXhoZ252elFrN2NwY1lDVjIzdUhmYjk4PQ==""""

var Username       = ""
var PasswordDigest = ""
var Created        = ""
var Nonce          = ""

val regexp = """(.*)="(.+)"""".r
val values = input.split(", ")
for (value <- values) {
  value match {
    case regexp("Username", v)       => Username = v
    case regexp("PasswordDigest", v) => PasswordDigest = v
    case regexp("Created", v)        => Created = v
    case regexp("Nonce", v)          => Nonce = v
    case regexp(k, v)                => println("Discarding " + k)
  }
}

println("Username = " + Username)
println("PasswordDigest = " + PasswordDigest)
println("Created = " + Created)
println("Nonce = " + Nonce)
```

こうしてしまうと、`Username`、`PasswordDigest`、`Created`、`Nonce`の値が実行中に書き換わってしまうのでいまいちである。

## 副作用を除去する例

`case class`を使って次のように書いてみる。パターンマッチを使うために`case class`にしている。なお、この実装のアイディアは[@ujm](https://twitter.com/ujm)からもらった。

```scala
val input = """Username="uname", PasswordDigest="oCkDT0u3nv/SiC1amR2pq7JxTxw=", Created="2014-05-10T10:08:43-07:00", Nonce="OEE2WXhoZ252elFrN2NwY1lDVjIzdUhmYjk4PQ==""""

case class WSSE(Username: String, PasswordDigest: String, Created: String, Nonce: String)
case class WSSEOption(Username: Option[String], PasswordDigest: Option[String], Created: Option[String], Nonce: Option[String])

val regexp = """(.*)="(.+)"""".r
val values = input.split(", ")
val wsseParse = values.foldLeft(WSSEOption(None, None, None, None)) { (acc, value) =>
  value match {
    case regexp("Username", v)       => WSSEOption(Some(v), acc.PasswordDigest, acc.Created, acc.Nonce)
    case regexp("PasswordDigest", v) => WSSEOption(acc.Username, Some(v), acc.Created, acc.Nonce)
    case regexp("Created", v)        => WSSEOption(acc.Username, acc.PasswordDigest, Some(v), acc.Nonce)
    case regexp("Nonce", v)          => WSSEOption(acc.Username, acc.PasswordDigest, acc.Created, Some(v))
    case regexp(k, v)                => acc
  }
}

val result = wsseParse match {
  case WSSEOption(Some(username), Some(passworddigest), Some(created), Some(nonce)) => Some(WSSE(username, passworddigest, created, nonce))
  case _ => None
}

println('result, 'is, result)
```

副作用が抜けて、だいぶ良くなったように思う。

## copyを使う例

せっかく`case class`を使っているのだから、`copy`を使うとより簡潔になる。`copy`についても[@ujm](https://twitter.com/ujm)に教わった。差分はこんな感じ。

```scala
val wsseParse = values.foldLeft(WSSEOption(None, None, None, None)) { (acc, value) =>
  value match {
    case regexp("Username", v)       => acc.copy(Username = Some(v))
    case regexp("PasswordDigest", v) => acc.copy(PasswordDigest = Some(v))
    case regexp("Created", v)        => acc.copy(Created = Some(v))
    case regexp("Nonce", v)          => acc.copy(Nonce = Some(v))
    case regexp(k, v)                => acc
  }
}
```

ここまでで満足した。
