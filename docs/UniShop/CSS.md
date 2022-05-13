> *CSS*

> - 居中

```scss
display: flex;							flex布局 纵向排列变横向排列
justify-content: space-between;			  左右贴边对齐
align-items: center;					 垂直居中
```



```scss
justify-content: center;	 			在主轴居中对齐（若主轴是x，则水平居中）
```



```SCSS
text-align: center;						文本居中对齐
```



```SCSS
height: 50px;							文本垂直居中
line-height: 50px;						设置与高度登高
```



> - 伪类选择器

`添加竖横线`

```SCSS
// 激活项的样式
&.active {
background-color: #ffffff;
position: relative;

	// 渲染激活项左侧的红色指示边线
	&::before {
		content: ' ';
		display: block;
		width: 3px;
		height: 30px;
		background-color: #c00000;
		position: absolute;
		left: 0;
		top: 50%;
		transform: translateY(-50%);
	}

}
```



`绘制登录盒子底部的半椭圆造型`

```scss
.login-container {
    height: 325px;
    background-color: #F8F8F8;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    position: relative;
    overflow: hidden;

    &::after {
        background-color: white;
        content: '';
        display: block;
        width: 100%;
        height: 40px;
        position: absolute;
        bottom: 0;
        left: 0;
        border-radius: 50%;
        transform: translateY(50%);
    }
}
```



<img src="C:\Users\86135\AppData\Roaming\Typora\typora-user-images\image-20220510145726397.png" alt="image-20220510145726397" style="zoom:50%;" />

> - 文字换行和省略

```SCSS
white-space: nowrap; 		// 文字不允许换行（单行文本）
overflow: hidden; 			// 溢出部分隐藏`
text-overflow: ellipsis;	// 文本溢出后，使用 ... 代替
```



> - 边距

```SCSS
padding: 0 5px; //上下0 左右5
border-bottom: 1px solid #efefef;   //底部边
```



> - Flex

```scss
flex-direction: column;			    设置主轴方向默认水平		  设置column则垂直
justify-content: center;		    在主轴居中对齐默认水平对齐	修改则垂直居中
justify-content: space-around;	 	平分剩余空间
```



> 

