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
