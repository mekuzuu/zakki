# 4/5

## Go

Go 1.18で導入されたGenericsを実プロジェクトで使う場合は、使用しているlinterが対応しているか注意する必要ありそう。

`golangci-lint`もつい最近1.18に対応していないlinterを無効にする設定を追加していた。
これは、あくまで暫定対応。

https://github.com/golangci/golangci-lint/pull/2438

---

# 4/20

## Rust

Rust製のFormatter。

https://rome.tools/docs/lint/rules#about-rome

Prettierと比較して9~12倍速いらしいが、まだ開発途上。
Markdownにも対応予定っぽいので、いつか使ってみたい。

---

# 4/24

## OSS

READMEをいけてる感じにするライブラリにPRを投げてマージされた。

と言っても、PR自体はドキュメント修正のみでとてもOSSに貢献したとは言えないものである。

https://github.com/Platane/snk/pull/27

原因を調べたり分かりやすいPRを考える過程が勉強になったので、ヨシとする。

---
