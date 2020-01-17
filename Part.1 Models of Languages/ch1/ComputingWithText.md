#### Computeing With Text
本书将通过使用元语言实现一个此语言的解释器，来定义一种语言。
假设这个元语言有正式的规格，那么我们实现的解释器也有正式的含义，并且是可分析的。
用来定义另一种语言的元语言不一定要非常高效，因为它的目的主要是向人类解释另一种语言。
甚至对于这种元语言，我们可以直接使用程序文字领域上的`逻辑`和`集合`理论。
同样的，在物理机器上的计算（本质是依照混编二进制在内存上的位置）也可以被描述，计算可以被抽象的描述为文本上的关系

#### 1.1 定义集合
```BNF
B = t
  | f
  | (B•B)
```

集合B是由递归定义的，所以B中的元素是无限的

#### 1.2 关系 relation
一个relation 表示一个集合，其中元素都由对(pair)组成
a ≡ a: `≡` 表示 reflexive, symmetric, transitive 三种关系，也称作`等价关系`
例子:
1. 反射关系-reflexive: a R a
2. 对称关系-symmetric: a R b => b R a
3. 传递关系-transitive: a R b and b R c => a R c

如果一个 relation 是: 反射的 and 对称的 and 传递的，那么这个关系是`等价关系`(equivalence)
例子:
(f • B1) ≈r B1
(t • B1) ≈r t
B1 ≈r B1

B1 ≈r B2 => B2 ≈r B1
B1 ≈r B2 and B2 ≈r B3 => B1 ≈r B3

#### 1.3 将 relations 看做求值
上述例子是一个 t: ture f: false, • 类似 or 操作， ≈r 代表两端表达式等价
由上面等价关系可推出:(f • t) ≈r (t • t)
但 • 并非和 or 操作完全相同，我们必须证明 (B1 • t) ≈r t, 对任意B1都成立（事实证明: 这个是无法证明的）
说明: 一般情况下，在`解释器定义的语言(interpreter-defined language)`和语言的性质间，会有一些间隙，针对间隙我们必须做好保障

#### 1.4 直接求值
≈ 求值规则 并不是与我们认为的求值完全相同，它允许我们证明表达式之间是否等价，但无法从任意的B推导出 t or f
更简单的 r relation 在这种情况下更适合

* `r` 单次规约, 所以对于任意 表达式B, 由 r 映射能得到 最多一个表达式
* `~>r` 定义为 reflexive-transitive（反射-传递） 的 r, 代表多次规约, 多以对于任意 表达式B 由 `~>r` 映射可能得到 许多个表达式
* `r` 和 `~>r` 是非对称的, 代表着求值必须朝着最终结果按照一定顺序进行
规约例子 (f•(f•(t•f)))
(f•(f•(t•f))) r   (f•(t•f))
(f•(t•f))     r   (t•f)
(t•f)         r   f
用`~>r`表示: (f•(f•(t•f))) ~>r f

#### 1.5 在上下文中求值
((f • t) • f) 无法直接规约，这种形式在定义中是没有约束的
只有 (f • B) 是被定义约束的，可规约的，但此时 B 是任意值
将 r 扩展成 `->r` 用以表示 子表达式的规约，包裹子表达式的外围，称之为`上下文 Context`

B1 r B2 => B1 ->r B2
将 B1 子表达式规约为 B1'
B1 ->r B1' => (B1 • B2) ->r (B1' • B2)
将 B2 子表达式规约为 B2'
B2 ->r B2' => (B1 • B2) ->r (B1 • B2')

那么
(f • t) r t
=> (f • t) ->r t

((f • t) • f) ->r (t • f) ->r t

`->r` 代表单次规约子表达式，如果定义 `->>r` 是 `->r` 的 反射-传递扩展
那么 `->>r` 就代表的是多次规约子表达式

#### 1.6 求值 Function
->>r 带领我们离一种有用的求值定义更近了些，但是还有一定距离。
因为 ((f • t) • f) ->>r t，并且 ((f • t) • f) ->>r (t • f)
但对于求值来说，我们只对求 B 的值结果为 t or f 感兴趣，所有其他的映射（->>r 或 =r）都是无关紧要的。
所有我们定义 evalr
`evalr` relation
evalr(B) = f  if B =r f
         = t  if B =r t
现在用了另一种形式表示relation，这种方式说明它是一个函数
这种关系表示遍历每个元素，映射到（最多）一个元素
我们用函数定义是因为: 如果 `evalr` 作为一个有意义的求值器，必须是一个函数

#### 1.7 总结
|  名字  | 定义  | 解释 |
|  ----  | ----  | ---- |
| `_` | 一个表达式语法的基本关系 | 单次规约，无上下文 |
| `->` | 基本关系的兼容 | 单次规约，带上下文 |
| `->>` | 反射-传递的 `->` | 多次规约，带上下文 |
| `=` | 对称-传递的`->>` | 将产生相同值的表达式视为相等 |
| `eval` | `=` 局限于一个范围内的结果 | 完整求值 |

