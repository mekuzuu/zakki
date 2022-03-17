# 3/1

## Go

labstack/echo v5のdiscussionを発見したので眺めてみた。

https://github.com/labstack/echo/discussions/2000

v5のリリースを急いではなさそうな感じ。

> Update 06.01.2022:
> Instead of release v5 on first week we will postpone it and tag v5.0.0-alpha at the end of January along with echo-contrib@v5.0.0-alpha. 
> This is mainly because we did not prepare documentation and there have been couple of small changes. 
> As this release does not bring much groundbreaking new things we are not in that hurry to release half-baked product.

1年くらいは、v4とv5の両方をサポートしてくれるとのこと。

> We support both v4 and v5 for some time (ala 1year) so current users would not be affected by our
"busywork" with APIs. From maintainers perspective it seems doable as we do not have (huge/many) changes often.
And from library/framework user perspective it should be positive as you are not forced/rushed to upgrade without
actual benefits for you.

Logger()が廃止されて、新しいロガーライブラリが提供されるっぽい。

> (Context interface) removed method Logger() - we encourage you to use your favourite Logging libraries (Log/Logrus/Zap/Zerolog etc).

Pathパラメータをパースするメソッド名がrenameされる。

明示的でわかりやすくなる。

> (Context interface) renamed method Param("id") to PathParam("id")
Reasoning for that is: Method name Param is too ambiguous. Context interface already has methods like:

`echo.Context`を引数に取る関数の第一引数がこのContextになる。

> (echo instance/middleware) function fields that have echo.Context as an argument have it as a first argument. Most notable is
default error handler signature change

---

# 3/7


## Go

3日前くらいに1.17.8のリリースがあった。

1件のセキュリティアップデートのみ。

https://groups.google.com/g/golang-announce/c/RP1hfrBYVuk/m/I7ouYouLAAAJ

具体的にどんな脆弱性があったのか把握するため、issueを見てみる。

https://github.com/golang/go/issues/51112

どうやら、regexp packageのparserでgoroutine stack overflowsを引き起こす脆弱性があった模様。

> Specifically, strings.Repeat("(", 1<<20)+strings.Repeat(")", 1<<20) is enough.

> Depth means the depth of the parse tree: (((((a))))) has depth 5, as does a***** in POSIX mode. (In Perl mode that's a syntax error.)

とあるので、ネストした括弧の数がこの問題を引き起こすトリガっぽい。

> To fix the problem I intend to cap the maximum depth of a regexp accepted by syntax.Parse at 1000, >5X what is needed by Google C++ and really about 100X what is reasonable.

そこで、ネストの数に上限を設けるとしている。上限値は1000（つまり、1000層?）が適切というかリーズナブルといっている。

これについては、このissueに対応するcommitを見ても確認することができる。

https://github.com/golang/go/commit/452f24ae94f38afa3704d4361d91d51218405c0a

---

# 3/11

## Misc

コンセプトから理解するRustを読み終えた。

雑な読書メモをgistにあげてあって、感想も忘れないうちに書くつもり。

https://gist.github.com/mudrk/507bb55dc5fb0a2257174249219c3230

---

# 3/15

## Misc

Tumblrの記事をHugoに移行した。

https://mudrk.github.io/

TumblrのMarkdawn（日本語入力）が使いづらかったのが移行理由。

Markdawnが扱えれば何でも良かったのですが、Hugoを選定した理由はGithubPagesを使って簡単に公開できそうだったのと、Goで書かれていると言うことで馴染みがあったため。

移行に辺り、参考にした記事はこちら。

https://open-groove.net/other-tools/hugo-github-pages-blog/

---

# 3/16

## Go

ついにGo 1.18がリリースされた。

https://github.com/golang/go/releases/tag/go1.18

https://go.dev/doc/go1.18

---

# 3/18

## Misc

Hugo + GithubPagesで作ったブログの運用リポジトリを作り直した。

先日参考にした記事だと、HugoのリポジトリとGithubPagesのリポジトリを分けていたのだけど、こんな面倒な運用をしなくてもいいことに気づいた。

GithubPagesのリポジトリで`main`ブランチと`gh-pages`ブランチを使うことで、1つのリポジトリで運用することができる。

詳細はHugoの公式ドキュメントを参考にした。

https://gohugo.io/hosting-and-deployment/hosting-on-github/

---
