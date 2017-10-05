---
title: mysql vs mongodb
type: "categories"
categories: 数据库
---

# 如何选择
mysql适合事务性和逻辑性比较强的业务，比如订单，支付、商品这些场景的，mongodb适合数据量大，计算量比较小的，有时候，mysql可以配合redis，mongodb可以配合mongodb

# 具体案例
比如我要查询手机商品的场景
```
mysql：
	需要一个mobile表，因为种类，和特性繁杂，还需要一个表存储手机的各自的特性，比如尺寸，颜色，材质

	mobile {
		id: xx
		name: '',
		price: ''
	}

	brand {
		id: xx,
		mobile_id: xxx
		brandName: xxx
	}

	property {
		id: xx,
		mobile_id: xxx
		size: xx,
		color: xx
	}

	category {
		id: xxx,
		mobile_id: xxx,
		name: xxx
	}

mongodb:
	mobile {
		id: xx,
		name: xx,
		price: xxx
	}
	后面的属性可以通过动态添加了
```

# mysql建表规则
让我们来回想下基础知识，23333....
1.一对一，一个表
2.多对一，一对多，两个表 （商品和属性的关系， 商品和品牌的关系）
3.多对多三个表(比如一个订单里面有多个商品，一个商品可存在多个订单里)

# 关系型数据库范式回顾

1NF： 字段是最小的的单元不可再分
2NF：满足1NF,表中的字段必须完全依赖于全部主键而非部分主键
3NF：满足2NF,非主键外的所有字段必须互不依赖
4NF：满足3NF,消除表中的多值依赖

## 总结
应用的范式登记越高，则表越多。表多会带来很多问题：

1 查询时要连接多个表，增加了查询的复杂度

2 查询时需要连接多个表，降低了数据库查询性能

而现在的情况，磁盘空间成本基本可以忽略不计，所以数据冗余所造成的问题也并不是应用数据库范式的理由。
因此，并不是应用的范式越高越好，要看实际情况而定。第三范式已经很大程度上减少了数据冗余，并且减少了造成插入异常，更新异常，和删除异常了。