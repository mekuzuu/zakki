# 2/1

## Go

昨日、1.18 Beta 2がリリースされていた。

https://go.dev/blog/go1.18beta2

そして、1.18の正式リリースは3月にずれ込んでいる模様。

> Because we are taking the time to issue a second beta, we now expect that the Go 1.18 release candidate will be issued in February, with the final Go 1.18 release in March.

---

# 2/6

## Vim

Git管理しているファイルの差分を左端に表示してくれるプラグイン。

https://github.com/airblade/vim-gitgutter

デフォルトの背景色が見辛くて、透明にする方法を調べて見たらこれに書いてあった。

https://stackoverflow.com/questions/15277241/changing-vim-gutter-color

```vim
highlight clear SignColumn
```

---

# 2/8

## Go

aws-sdk-go-v2のtest用のsetup処理を提供するファイルが参考になる。

https://github.com/aws/aws-sdk-go-v2/blob/main/service/internal/integrationtest/s3/setup_test.go

`testing.M`は便利。

test後のcleanup処理が要らない場合は、init関数内にsetup処理を書いていく感じでもいいのかなと思っている。

---

# 2/14

## Misc

最近このzakkiを更新できていない・・・。

## Rust

コンセプトから理解するRustという書籍を購入したので、読んでいる。

Rust独特の機能にフォーカスを当てていて、日本語も平易でわかりやすい。

読書メモを取りながらなので、読み終えるまで少し時間がかかりそう。

https://gihyo.jp/book/2022/978-4-297-12562-2

## Go

3日前くらいに1.17.7のリリースがあった。

3件のセキュリティアップデート。

https://groups.google.com/g/golang-announce/c/SUsQn0aSgPQ/m/gx45t8JEAgAJ

1つはmath/big packageの`Rat.SetString()`を使うと大量のRAMが消費され、システムがクラッシュまたはタイムアウトする可能性があるみたいなやつっぽい。

> math/big: prevent large memory consumption in Rat.SetString
An attacker can cause unbounded memory growth in a program using (*Rat).SetString
due to an unhandled overflow. 
This is CVE-2022-23772 and Go issue https://go.dev/issue/50699.

---

# 2/15

# Go

Echoの`MultipartForm`は、デフォルトでは32MBが上限になっている。

そのため、これよりも大きいデータを扱いたい場合はこんな風に明示的に指定しましょうねというお話。

```go
const MaxFileSize untyped int = 104857600

c.Request().ParseMultipartForm(MaxFileSize)
```
https://kaminashi-developer.hatenablog.jp/entry/golang-echo-multipart

同僚の人に教えてもらったのだけど、`MultipartForm`の前にForm系の処理を使っている場合は効果がないらしい。

その理由については、EchoがWrapしているGoのnet/http packageのrequest.goのForm系の処理を見ると分かる。

https://github.com/golang/go/blob/0fdc3801bfd43d6f55e4ea5bf095e1ea55430339/src/net/http/request.go

Formが初期化されていない場合は、デフォルトで32MBが設定されてしまうっぽい。

なので、`ParseMultipartForm`を一番最初に呼び出してあげれば任意のサイズを指定できるはず。

---

2/17

##Go

Go1.18のRelease Candidate1のブランチが切られていた。

いよいよという感じがしてきた。

https://github.com/golang/go/commit/cb5a598d7f2ebd276686403d141a97c026d33458

---
