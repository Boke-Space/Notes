> 盒子模型(Box Model)

<img src="https://guli-ddu.oss-cn-beijing.aliyuncs.com/BoxModel.png" />



> 边框（border)

边框会额外增加盒子的实际大小，因此有两种方案解决。

1. 测量盒子大小的时候，不测边框。
2. 若测量的时候包含了边框，则需要 width/height-边框宽度。
3. Content-box不算入

边框属性简写

​	`border: 5px solid pink;`

> 内边距（padding）

- 1 个值：上下左右
- 2 个值：上下，左右
- 3 个值：上，左右，下
- 4 个值：上，右，下，左，顺时针

**padding 会影响盒子实际大小**

当给盒子指定了 `padding` 值以后，发生了两件事情：

1. 内容和边框有了距离，增加内边距
2. padding 值影响了盒子实际大小

也就是说，当盒子已经有了宽度和高度，再指定内边距，会撑大盒子。

要保证盒子和效果图一样大，则让 `width/height`-多出来的内边距大小即可。

> 外边距margin

`margin` 简写方式与 `padding` 一致。

外边距可以让块级盒子 **水平居中**，但是必须满足两个条件：

1. 盒子必须指定宽度（width）
2. 盒子左右的外边距都设置为 `auto`

**外边距典型应用**

​		`width: 960px;` 

​		`margin: 0 auto;`

**使行内元素或行内块元素水平居中**

​		`text-align: center;`

**嵌套块元素垂直外边距的塌陷**

- 为父元素定义上边框
- 为父元素定义上内边距
- 为父元素添加 `overflow:hidden`

> 文字换行和省略

```CSS
white-space: nowrap; 		// 文字不允许换行（单行文本）
overflow: hidden; 			// 溢出部分隐藏`
text-overflow: ellipsis;	// 文本溢出后，使用 ... 代替
```

> 边距

`padding: 0 5px; //上下0 左右5` 

`      border-bottom: 1px solid #efefef;   //底部边框`

> 居中

`display: flex;					//flex布局`

​      `flex-direction: column;		//纵向布局`

`align-items: center; 			//垂直居中`
`justify-content: space-between; //两边贴紧，再平分剩余空间`

> 添加竖横线	伪类选择器

`// 激活项的样式`
`&.active {`
`background-color: #ffffff;`
`position: relative;`

`// 渲染激活项左侧的红色指示边线`

​	`&::before {`
​		`content: ' ';`
​		`display: block;`
​		`width: 3px;`
​		`height: 30px;`
​		`background-color: #c00000;`
​		`position: absolute;`
​		`left: 0;`
​		`top: 50%;`
​		`transform: translateY(-50%);`
​	`}`

`}`



