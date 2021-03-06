# 路由树

就本质而言，当使用嵌套和 `~` 操作符组合 `Directive` 和路由时，组合的路由结构会形成一棵树。请求到来后，会被注入路由树的 `root`，然后以深度优先方式遍历所有分支，直到某个结点 `complete` 该请求或者该请求被完全 `reject`。

考虑如下例子：

```scala
val route =
  a {
    b {
      c {
        ... // route 1
      } ~
      d {
        ... // route 2
      } ~
      ... // route 3
    } ~
    e {
      ... // route 4
    }
  }
```

上面例子中，由 5 个指令组成一颗路由树：

* route 1 只有当指令 a、b、c 都允许请求通过时才能到达；
* route 2 只有当指令 a、b 允许通过，c 拒绝通过，d 允许通过时才能到达；
* route 3 只有当指令 a、b 允许通过，c、d 拒绝通过时才能到达；

可以将 route 3 视为 `catch-all` 路由，若其前面的 c、d 路由拒绝了请求，route 3 总会捕捉到请求。借助该机制，可以轻易实现复杂的过滤逻辑：只要将最特殊的场景放到前面、最通用的场景放到后面即可。
