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
