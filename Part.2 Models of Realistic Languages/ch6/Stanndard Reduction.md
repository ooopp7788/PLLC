### 介绍
通过 Church-Rosser 定理可以知道当一个程序可以规约为一个 value 时, 这个程序和这个 value 是等价的，所以我们可以依赖规约来对程序求值。但是即使对限制了规约也无法完全满足需求，因为可选择的规约方式太多。
一种实现，在所有规约路径集合中选择一个确定的规约步骤，并清晰知道整个规约对整个过程有益；否则我们只能搜索很多不同的规约路径。

#### Standard Reductions

