# 小程序开发

## 页面路由

- navigateTo, redirectTo 只能打开非 tabBar 页面。

- switchTab 只能打开 tabBar 页面。

- reLaunch 可以打开任意页面。

- 页面底部的 tabBar 由页面决定，即只要是定义为 tabBar 的页面，底部都有 tabBar。

- 调用页面路由带的参数可以在目标页面的onLoad中获取。

## WXML

- 数据绑定

	WXML 中的动态数据均来自对应 Page 的 data。

	1. 简单绑定
		数据绑定使用 Mustache 语法（双大括号）将变量包起来，可以作用于：
	内容
```
	<!--wxml-->
	<view> {{message}} </view>
	// page.js
	Page({
	  data: {
		message: 'Hello MINA!'
	  }
	})
```
	2. 组件属性(需要在双引号之内)
	
```
	<view id="item-{{id}}"> </view>
	Page({
	  data: {
		id: 0
	  }
	})
```

	3. 控制属性(需要在双引号之内)
	
```
	<view wx:if="{{condition}}"> </view>
	Page({
	  data: {
		condition: true
	  }
	})
```
	4. 关键字(需要在双引号之内)
	
	true：boolean 类型的 true，代表真值。
	false： boolean 类型的 false，代表假值。	
```
	<checkbox checked="{{false}}"> </checkbox>
```
	- 特别注意：不要直接写 checked="false"，其计算结果是一个字符串，转成 boolean 类型后代表真值。

	5. 运算
	
	可以在 {{}} 内进行简单的运算，支持的有如下几种方式：
	
	5.1. 三元运算
```
	<view hidden="{{flag ? true : false}}"> Hidden </view>
```
	5.2. 算数运算
	
```
	<view> {{a + b}} + {{c}} + d </view>
	Page({
	  data: {
		a: 1,
		b: 2,
		c: 3
	  }
	})
```

	5.3. 逻辑判断
	
```
	<view wx:if = "{{length > 5}}"></view>
```
	
	
	5.4. 字符串运算
	
```
	<view>{{"hello" + name}}</view>
	Page({
	  data:{
		name: 'MINA'
	  }
	})
```

	5.5. 数据路径运算
	
```
	<view>{{object.key}} {{array[0]}}</view>
	Page({
	  data: {
		object: {
		  key: 'Hello '
		},
		array: ['MINA']
	  }
	})
```
	5.6. 组合
	
	也可以在 Mustache 内直接进行组合，构成新的对象或者数组。
	
		5.6.1. 数组
```
	<view wx:for="{{[zero, 1, 2, 3, 4]}}"> {{item}} </view>
	Page({
	  data: {
		zero: 0
	  }
	})
```
	最终组合成数组[0, 1, 2, 3, 4]。
	
	5.6.2. 对象

```	
	<template is="objectCombine" data="{{for: a, bar: b}}"></template>
	Page({
	  data: {
		a: 1,
		b: 2
	  }
	})
```
	最终组合成的对象是 {for: 1, bar: 2}
	也可以用扩展运算符 ... 来将一个对象展开
```	
<template is="objectCombine" data="{{...obj1, ...obj2, e: 5}}"></template>
Page({
  data: {
    obj1: {
      a: 1,
      b: 2
    },
    obj2: {
      c: 3,
      d: 4
    }
  }
})
```
	最终组合成的对象是 {a: 1, b: 2, c: 3, d: 4, e: 5}。
	如果对象的 key 和 value 相同，也可以间接地表达。
```
<template is="objectCombine" data="{{foo, bar}}"></template>
Page({
  data: {
    foo: 'my-foo',
    bar: 'my-bar'
  }
})
```
	最终组合成的对象是 {foo: 'my-foo', bar:'my-bar'}。
	注意：上述方式可以随意组合，但是如有存在变量名相同的情况，后边的会覆盖前面，如：
```
<template is="objectCombine" data="{{...obj1, ...obj2, a, c: 6}}"></template>
Page({
  data: {
    obj1: {
      a: 1,
      b: 2
    },
    obj2: {
      b: 3,
      c: 4
    },
    a: 5
  }
})
```
	最终组合成的对象是 {a: 5, b: 3, c: 6}。
	注意： 花括号和引号之间如果有空格，将最终被解析成为字符串
```
<view wx:for="{{[1,2,3]}} ">
  {{item}}
</view>
等同于
<view wx:for="{{[1,2,3] + ' '}}">
  {{item}}
</view>
```

- 列表渲染

	wx:for
	在组件上使用 wx:for 控制属性绑定一个数组，即可使用数组中各项的数据重复渲染该组件。
	默认数组的当前项的下标变量名默认为 index，数组当前项的变量名默认为 item

```
	<!--wxml-->
	<view wx:for="{{array}}"> {{item}} </view>
	// page.js
	Page({
	  data: {
		 array: [1, 2, 3, 4, 5]
	  }
	})
```

- 条件渲染

```
	<!--wxml-->
	<view wx:if = "{{view == 'AAA'}}">AAA</view>
	<view wx:elif = "{{view == 'BBB'}}">BBB</view>
	<view wx:elif = "{{view == 'CCC'}}">CCC</view>
	<view wx:elif = "{{view == 'DDD'}}">DDD</view>
	<view wx:else = "{{view == 'EEE'}}">EEE</view>
	// page.js
	Page({
	  data: {
		  view:'DDD'
	  }
	})
```

- 模板

```
	<!--wxml-->
	<template name = "staffName">
    <view>
		  FirstName : {{firstName}}, LastName : {{lastName}}
		</view>
	  </template>
	  <template is = "staffName" data = "{{...staffA}}"></template>
	  <template is = "staffName" data = "{{...staffB}}"></template>
	  <template is = "staffName" data = "{{...staffC}}"></template>
	  // page.js
	  Page({
	  data: {
		staffA: {firstName:'Guoqian', lastName: 'Cheng'},
		staffB: { firstName: 'Shengxuan', lastName: 'Cheng' },
		staffC: { firstName: 'Shengyang', lastName: 'Cheng' }
	  }
	})
	
```