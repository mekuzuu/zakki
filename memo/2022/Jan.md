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


## Git CUI

Rust製のgituiを入れて使っていたら、pushできない問題に遭遇。

https://github.com/extrawurst/gitui/issues/495

調べてみるとこのissueを発見。同じような人がいるっぽい。

> ssh-add ~/.ssh/id_rsa - adding key(s) to ssh-agent fixed the error for me)

で、上記コメントにあるように、ssh-agentに秘密鍵を登録したらうまくいった。

PC再起動する度にssh時にまたパスフレーズ聞かれるので、keychainに秘密鍵を登録しておくと良さそう。

```
ssh-add -K ~/.ssh/id_rsa
```

Mac OS Sierra (10.12) 以降では、keychainに保存はされるものの、再起動時に自動では読み込まれないため、下記の設定がされていることが前提。


```.ssh/config 
Host *
  UseKeychain yes
  AddKeysToAgent yes
```

---

# 1/10

## Misc

gituiでpushできない問題についてTweetしたところ、まさかのcontributerの方からreplyがあった。

https://twitter.com/Extrawurst/status/1480196599305416713?s=20

「shell起動時にssh-addするscriptを書けばよい。issueにも書いてあるよ。」という内容。

確かにその通りだなと。（セキュリティ的に微妙という考え方もあるのかな?）

親切な方だなと思ったのと、replyもらえて素直に嬉しかった。

---

# 1/13

## Rust

The Rust Programming Languageに入門した。

https://doc.rust-lang.org/book/

最初はMicrosfoft社のRustの入門講座から入ったが、どうも自分には合わなくて途中で進めるのをやめた。

---

# 1/14

## Rust

`std::prelude`と言うモジュールがあって、ここに書いてあるものはRustプログラムに暗黙的にimportされる。
なので、useで明示的にimportしなくても使えるようになっている。

https://doc.rust-lang.org/std/prelude/index.html

例えば、このようなプログラムでは暗黙的に`use std::string;`されているため、`String`を無修飾で呼び出せる。

```rust
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

`std::prelude`以外のprelude（ex.`std::io::prelude`)は明示的にimportする必要がある。

---

# 1/15

## Go

1.18で入るfuzzing testのtutorialが公開された。

https://go.dev/doc/tutorial/fuzz

---

# 1/16

## Misc
Obsidianと言うMarkdown Editerを使いはじめた。

https://obsidian.md/

まずは日々の雑記や読書メモを一元管理できたら嬉しいなと言う感じで触り始めたばかり。

---

# 1/17

## OpenAPI

基本的なデータ構造は同じだけど、一部だけ異なるデータのOpen APIでどうやって表現するか。

みたいなことを調べていたところ、以下の記事で紹介されている手法が使えそう。

https://eng-blog.iij.ad.jp/archives/6813


例えば、こんな感じのIoTデバイスから収集したJSON形式のデータを扱うケース。

```json
[
    {
        "id": 0,
        "time": "2020-10-01T14:48:10Z",
        "device": "Light",
        "detail": {
            "switch": "Off"
        }
    },
    {
        "id": 1,
        "time": "2020-10-02T15:00:00Z",
        "device": "Thermometer",
        "detail": {
            "temperature": 26.5
        }
    }
]
```

idやtimeは共通のプロパティだけど、`detail`の構造が`device`の種類によって異なる。

このように、「データの一部（この場合はdetail）が異なるデータ構造を持つデータを、同じ種類データとして扱うデータモデリング手法を、記事内では「ポリモーフィックパターン」と定義している。

で、このポリモーフィックパターンをOpenAPIでどうやって表現するかというと、`oneOf`と`discriminator`を使う。


```yaml
{
    "components": {
        "schemas": {
            "IoTLog": {
                "type": "object",
                "properties": {
                    "id": { "type": "integer" },
                    "time": {
                        "type": "string",
                        "format": "date-time"
                    },
                    "device": { "enum": ["Light", "Thermometer"] },
                    "detail": {
                        "oneOf" : [
                            { "$ref": "#components/schemas/LightLog" },
                            { "$ref": "#components/schemas/ThermometerLog" }
                        ],
                        "discriminator": {
                            "propertyName": "device",
                            "mapping": {
                                "Light": "#components/schemas/LightLog",
                                "Thermometer": "#components/schemas/ThermometerLog"
                            }
                        }
                    }
                }
            },

            "LightLog": {
                "type": "object",
                "properties": {
                    "switch": { "enum": ["Off", "On"] }
                }
            },
            
            "ThermometerLog": {
                "type": "object",
                "properties": {
                    "temperature": { "type": "number" }
                }
            }
        }
    }
}
```

`oneOf`を使うと、schemaのどれか1つのみと一致する要素であるというのを表現できる。

`discriminator`を使うと、プロパティの値に応じて適用するschemaを指定することができる。

但し、記事によれば`discriminator`はv3.1でdeprecateを目指しているぽいので時すでに遅しと思った（すでにv3.1.0がリリースされているため）けど、issueの状態はまだopenになっていた。

https://github.com/OAI/OpenAPI-Specification/issues/2143

議論が活発すぎて全部読めてないけど、とりあえずまだdeprecateにはなってなさそう。

いずれにしてもdeprecateは避けられなさそう(JSON Schemaに準拠していくという方針があるよう）なので、動向をwatchして必要がありそう。

---

# 1/18

## OpenAPI

openapi-generatorでは、`oneOf`をうまく処理できない疑惑・・・。

色んな言語に対応してるのもあると思うけど、openなissueの数がすごい。

https://github.com/OpenAPITools/openapi-generator/issues?q=is%3Aissue+is%3Aopen+oneOf

---

# 1/20

## Rust

シャドーイングという、慣れない概念が出てきたので書いておく。

`let`で同名の変数に値を束縛（変数束縛）することができ、異なる型への束縛もできる。

変数束縛にはスコープがあり、変数束縛は定義されたブロックの内側でのみ有効。

```rust
fn main() {
  let x = 1;
  // The value of x is: 1
  println!("The value of x is: {}", x);
  
  let x = 3.5; 
  // The value of x is: 3.5
  println!("The value of x is: {}", x);
  
  let x = "hoge"; 
  // The value of x is: hoge
  println!("The value of x is: {}", x);
  
  {
    let x = 2;
    // The value of x is: 2
    println!("The value of x is: {}", x);
    
    let y = 10;
    // The value of y is: 10
    println!("The value of y is: {}", y);
  }
    
  // The value of x is: hoge
  println!("The value of x is: {}", x);  
    
  // error[E0425]: cannot find value `y` in this scope
  println!("The value of y is: {}", y);
}
```

https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=ad51915fe010957d3ff073979d4d5d4b