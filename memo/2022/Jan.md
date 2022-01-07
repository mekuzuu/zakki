# 1/1

## Go

シグナルハンドリングとContextを使って安全にGoのプログラムを終了させるを読みなおした。

https://blog.kaznishi.com/post/190916_gracefulstop/

途中で止まったら困る処理を、シグナルハンドリングとcontextを使ってコントロールするという内容。

contextは便利。

---

# 1/3

## Go

go2linqというLINQライブラリを発見。

https://github.com/solsw/go2linq

Genericsを使用している点が特徴かもしれない。

---

# 1/4

## Terminal Emulator

Rust製のAlacrittyを入れてみた。

https://github.com/alacritty/alacritty

サクサク動いており、使い心地も良い感じ。

設定周りはここにまとめている。

https://ustair.tumblr.com/post/672412551614808064/memo-alacritty%E3%81%AE%E8%A8%AD%E5%AE%9A

---

# 1/5

## Go

go-taskのマイナーバージョンアップがあった。

https://github.com/go-task/task/releases/tag/v3.10.0

`defer`がよさげ。

taskがコケても実行してくれるところが良い。
> With the defer keyword, it's possible to schedule cleanup to be run once the task finishes. The difference with just putting it as the last command is that this command will run even when the task fails.

https://taskfile.dev/#/usage?id=doing-task-cleanup-with-defer

あと`.yaml`拡張子をサポートするようになって、ハッピーになる人も多いのでは。

## gRPC

`reserved`について。

予約・廃止するタグナンバーやフィールドは、`reserved`を使うことで再利用を防止することができる。


```proto
message Foo {
  reserved 2, 15, 9 to 11;
  reserved "foo", "bar";
}
```

https://developers.google.com/protocol-buffers/docs/proto3#reserved

---

# 1/6

## Misc

Rust製のCLIが便利で、Rustに興味を持ったのでRustをはじめようと思っている。

こちらの記事が参考になりそう。

https://blog-dry.com/entry/2021/01/23/141936

---

# 1/7

## Rust

Rustをインストールすると、bashやzshの場合は`.profile`のようなエントリポイントにPATHが自動的に設定される。

しかし、fishの場合は`.profile`のようなエントリポイントを読み込まないため、設定されない。

https://github.com/rust-lang/rustup/issues/478

じゃあどうするかというと、`~/.config/fish/config.fish`に下記を追加すればよい。

```
set -x PATH $HOME/.cargo/bin $PATH
```

---
