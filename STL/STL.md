#   STL六大部件

STL六大组件的交互关系: __Container__透过Allocator取得数据储存空间，__Algorithm__透过__Iterator__存取__Container__内容，__Functor__可以协助__Algorithm__完
成不同的策略变化，__Adapter__可以修饰或套接__Functor__。

#  空间配置器

