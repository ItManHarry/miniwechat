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
	<view wx:for="{{array}}">
	  {{index}}: {{item.message}}
	</view>
	// page.js
	Page({
	  data: {
		array: [{
		  message: 'foo',
		}, {
		  message: 'bar'
		}]
	  }
	})
```
	使用 wx:for-item 可以指定数组当前元素的变量名，
	使用 wx:for-index 可以指定数组当前下标的变量名：

```
	<!--wxml-->
	<view wx:for="{{array}}" wx:for-index = "i" wx:for-item = "it">
	  {{i}}: {{it.message}}
	</view>
	// page.js
	Page({
	  data: {
		array: [{
		  message: 'foo',
		}, {
		  message: 'bar'
		}]
	  }
	})
```

	wx:for 也可以嵌套，下边是一个九九乘法表
	
```
<view wx:for="{{[1, 2, 3, 4, 5, 6, 7, 8, 9]}}" wx:for-item="i">
  <view wx:for="{{[1, 2, 3, 4, 5, 6, 7, 8, 9]}}" wx:for-item="j">
    <view wx:if="{{i <= j}}">
      {{i}} * {{j}} = {{i * j}}
    </view>
  </view>
</view>
```

	block wx:for
	类似 block wx:if，也可以将 wx:for 用在<block/>标签上，以渲染一个包含多节点的结构块。例如：
```
<block wx:for="{{[1, 2, 3]}}">
  <view> {{index}}: </view>
  <view> {{item}} </view>
</block>
```

wx:key
如果列表中项目的位置会动态改变或者有新的项目添加到列表中，并且希望列表中的项目保持自己的特征和状态（如 input 中的输入内容，switch 的选中状态），需要使用 wx:key 来指定列表中项目的唯一的标识符。
wx:key 的值以两种形式提供
字符串，代表在 for 循环的 array 中 item 的某个 property，该 property 的值需要是列表中唯一的字符串或数字，且不能动态改变。
保留关键字 *this 代表在 for 循环中的 item 本身，这种表示需要 item 本身是一个唯一的字符串或者数字，如：
当数据改变触发渲染层重新渲染的时候，会校正带有 key 的组件，框架会确保他们被重新排序，而不是重新创建，以确保使组件保持自身的状态，并且提高列表渲染时的效率。

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

- 引用

	WXML 提供两种文件引用方式import和include。
	- import
		import可以在该文件中使用目标文件定义的template，如：
		在 item.wxml 中定义了一个叫item的template：
```		
	<!-- item.wxml -->
	<template name="item">
	 <text>{{text}}</text>
	</template>
```
	在 index.wxml 中引用了 item.wxml，就可以使用item模板：
```
<import src="item.wxml"/>
<template is="item" data="{{text: 'forbar'}}"/>
```

	import 的作用域
	import 有作用域的概念，即只会 import 目标文件中定义的 template，而不会 import 目标文件 import 的 template。
	如：C import B，B import A，在C中可以使用B定义的template，在B中可以使用A定义的template，但是C不能使用A定义的template。
	
```
<!-- A.wxml -->
<template name="A">
  <text> A template </text>
</template>
<!-- B.wxml -->
<import src="a.wxml"/>
<template name="B">
  <text> B template </text>
</template>
<!-- C.wxml -->
<import src="b.wxml"/>
<template is="A"/>  <!-- Error! Can not use tempalte when not import A. -->
<template is="B"/>
```

	- include
	include 可以将目标文件除了 <template/> <wxs/> 外的整个代码引入，相当于是拷贝到 include 位置，如：
	
```
<!-- index.wxml -->
<include src="header.wxml"/>
<view> body </view>
<include src="footer.wxml"/>
<!-- header.wxml -->
<view> header </view>
<!-- footer.wxml -->
<view> footer </view>
```