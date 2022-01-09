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

## Go

1.17.6のマイナーバージョンのリリースが来てた。

https://twitter.com/golang/status/1479199301352013829?s=20

https://github.com/golang/go/issues?q=milestone%3AGo1.17.6+label%3ACherryPickApproved

ぱっと見た感じ、低レイヤ系のパッチが多く正直よく分からなかった・・・。

これだけはそこそこ理解できた気がする。

https://github.com/golang/go/issues/49741


```go
package main

import (
	"io"
	"log"
	"net/http"
	"net/http/httptest"
	"strings"
	"time"
)

func handler(w http.ResponseWriter, r *http.Request) {
	data := strings.Repeat("x", 1<<16)
	tick := time.NewTicker(1 * time.Millisecond) // 1ミリ秒周期のticker。
	defer tick.Stop()
	for {
		select {
		case <-tick.C:
			n, err := io.WriteString(w, data) // httpサーバーに対して書き込みを行う。
			log.Printf("wrote %d, err %v", n, err)
			if err != nil {
				return
			}
		case <-r.Context().Done():
			log.Printf("context cancelled")
			return
		}
	}
}

func main() {
	sv := httptest.NewUnstartedServer(http.HandlerFunc(handler))
	sv.EnableHTTP2 = true
	sv.Config.WriteTimeout = 1 * time.Second // request bodyの読み込み〜responseの書き込みまでのTimeoutを1秒に設定。
	sv.StartTLS()

	resp, err := sv.Client().Get(sv.URL + "/")
	if err != nil {
		log.Fatal(err)
	}
	defer resp.Body.Close()

	select {} // block forever
}
```

HTTP/2を有効にしたHTTPサーバーに対して書き込みを行うhandlerを定義している。

このサーバーはRequest Bodyの読み込み〜Responseの書き込みまでのTimeoutを1秒に設定してある。

実際に動かしてみると分かるが、
> This program creates a HTTP2 service which fills the HTTP/2 stream's response window by failing to consume any data from the response body. The write deadline should fire after 1 second and terminate the response. It does not.

とあるように、1秒後にResponseの書き込みがCloseされることを期待しているがCloseされないというものっぽい。

---

# 1/8

## Go

Go 1.18で知っておくべきことが、Miroというツール上で可視化されていて分かりやすい。

Golang Weeklyで紹介されていた。

https://miro.com/app/board/uXjVOaLW7dY=/

## Fish

fish-shellを起動時にtmuxを起動する方法。

https://github.com/fish-shell/fish-shell/issues/4434

---

# 1/9

## Terminal Emulator

alacrittyのthemeの設定を便利にしてくれるライブラリを発見した。

https://github.com/rajasegar/alacritty-themes

何が便利かというと、`alacritty-themes`コマンドでリアルタイムにthemeを確認できる。
さらに、そのthemeを選択すると設定ファイルに反映してくれる。

---
