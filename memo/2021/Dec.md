# 12/9

## Go

GORMの[v1.22.4](https://github.com/go-gorm/gorm/releases/tag/v1.22.4 )のパッチリリースについて調べた。

issue[#4198](https://github.com/go-gorm/gorm/issues/4198 )の修正みたい。

gormが提供する[field tag](https://gorm.io/docs/models.html#Fields-Tags
)で`DEFAULT:0`などを明示すると、auto migrate時にalter tableが実行されるバグがあった模様。

issueのタイトルに`alert table`とあるが、alter tableの間違いだと思う。（多分）

---

# 12/10

## GitHub

新しいコード検索機能をテクノロジープレビューを発表したことがTwitterで話題になってた。

- https://github.blog/2021-12-08-improving-github-code-search/
- https://www.itmedia.co.jp/news/articles/2112/10/news082.html

便利そうなので、早く触ってみたい。

Rustで書かれているらしい。

## OpenAPI

openapi-generator-cli v4.1.1でGoの構造体を生成するときに、ポインタ型を生成したいケースがあってハマった。

結論としては、ポインタ型にしたいObjectに`nullable:true`を指定する。

もし`ref$`でschemaを参照している場合は、schemaを`allOf`で指定する。

```
nullable: true
allOf:
- $ref: "type_schemas.yaml#/xxx"
```

自分がハマったのは後者の方で、他の人が書いていたコードを見て自己解決。

OASのDocumentには書いてなくて、それらしきStack OverflowとGithubのIssueならあったので貼っておく。

- https://stackoverflow.com/questions/40920441/how-to-specify-a-property-can-be-null-or-a-reference-with-swagger
- https://github.com/OAI/OpenAPI-Specification/issues/1368

---

# 12/11

## Go

Go 1.17.5のリリース（セキュリティ・アップデート）について。

https://twitter.com/golang/status/1468963278416015375?s=21

HTTP2 ServerのCanonicalHeaderのキャッシュサイズを制限するパッチっぽい。

CanonicalHeaderは、HTTP Header情報をキャッシュしておくためのmapである。

```go
canonHeader  map[string]string // http2-lower-case -> Go-Canonical-Case
```

パッチを当てる前は、このmapに無制限にキャッシュすることができるため、メモリリークの脆弱性があるというものっぽい。 これが、パッチのソースコードの修正を見るに、`canonHeader`の要素数の上限が32に設定されている。

https://go-review.googlesource.com/c/net/+/369794/3/http2/server.go

```go
// maxCachedCanonicalHeaders is an arbitrarily-chosen limit on the number of
// entries in the canonHeader cache. This should be larger than the number
// of unique, uncommon header keys likely to be sent by the peer, while not
// so high as to permit unreaasonable memory usage if the peer sends an unbounded
// number of unique header keys.
const maxCachedCanonicalHeaders = 32
if len(sc.canonHeader) < maxCachedCanonicalHeaders {
sc.canonHeader[v] = cv
}
```

---

# 12/13

## Java

巷で話題のLog4jの脆弱性について。

Log4jと呼ばれるJavaのLoggingライブラリにリモートコード任意実行の脆弱性が存在するというもの。

下記の記事が参考になった。

- https://ezoeryou.github.io/blog/article/2021-12-10-log4j.html?utm_source=pocket_mylist
- https://piyolog.hatenadiary.jp/entry/2021/12/13/045541

---

# 12/14

## Java

Log4jの脆弱性について。

Publickeyの記事でRedisがLog4jを採用していると書いてあったので、調べた。（業務で使用しているため）

https://www.publickey1.jp/blog/21/javalog4j.html

結論としては、Redisの公式にもある通り、影響はなさそう。

https://redis.com/security/notice-apache-log4j2-cve-2021-44228/

RedisのJava用のClientは引っかかったぽいが、2日前にパッチリリースされているので大丈夫そう?
> Jedis is a Redis sponsored Java client. It uses the affected library in test suites only. A new Jedis release is now available that mitigates the vulnerability. End users are advised to evaluate their exposure to determine upgrade urgency.

https://github.com/redis/jedis/releases/tag/v3.7.1

---

# 12/16

## Go

Go 1.18 beta 1がリリースされた。

https://go.dev/blog/go1.18beta1

目玉はGenerics機能。Tutorialも出ているので、触ってみる。

https://go.dev/doc/tutorial/generics

---

# 12/18

## Go

公式のGenerics Tutorialを触ってみた。

mapの要素をfor文でloopして加算する関数を例に、Genericsなしとありでどう違うのか学ぶことができる。

- Genericsなし

```go
// SumInts adds together the values of m.
func SumInts(m map[string]int64) int64 {
var s int64
for _, v := range m {
s += v
}
return s
}

// SumFloats adds together the values of m.
func SumFloats(m map[string]float64) float64 {
var s float64
for _, v := range m {
s += v
}
return s
}
```

- Genericsあり

```go

// SumIntsOrFloats sums the values of map m. It supports both floats and integers
// as map values.
func SumIntsOrFloats[K comparable, V int64 | float64](m map[K]V) V {
var s V
for _, v := range m {
s += v
}
return s
}
```

`comparable`はジェネリクスの型パラメータに比較可能な型のみを受け付ける制約を付けるもの。

型制約をインターフェースとして宣言することもできる。

```go
type Number interface {
int64 | float64
}

// SumNumbers sums the values of map m. Its supports both integers
// and floats as map values.
func SumNumbers[K comparable, V Number](m map[K]V) V {
var s V
for _, v := range m {
s += v
}
return s
}
```

---

# 12/20

## Go

gomock v1.6.0で追加された`InAnyOrder`Matcherを使うと、モックメソッドの引数のスライスを順不同で検証できるというお話。

- https://budougumi0617.github.io/2021/12/20/gomock_verify_in_any_order/
- https://github.com/golang/mock/pull/546/commits

```go
mockobj.EXPECT().FindByIDs(gomock.InAnyOrder([]int{1, 2, 3}))
```

`Any`Matcherを使って検証を省略してしまっていた（もしくは自作Matcherを作る必要があった）ので、これは便利そう。

`InAnyOrder`どのようなからくりになっているのか見て見ると、二重loopで配列同士を比較している感じ。

https://github.com/golang/mock/blob/master/gomock/matchers.go#L214-L258

```go
usedFromGiven := make([]bool, given.Len())
foundFromWanted := make([]bool, wanted.Len())
for i := 0; i < wanted.Len(); i++ {
    wantedMatcher := Eq(wanted.Index(i).Interface())
    for j := 0; j < given.Len(); j++ {
        if usedFromGiven[j] {
            continue
        }
        if wantedMatcher.Matches(given.Index(j).Interface()) {
            foundFromWanted[i] = true
            usedFromGiven[j] = true
            break
        }
    }
}
```
