> UniShop	黑马优购		购物车



> - *节流阀		防止发起额外的请求*

`goods_list.vue`

```js
data() {
  return {
    // 是否正在请求数据
    isloading: false
  }
}
```



```js
// 获取商品列表数据
async getGoodsList(data) {
    this.isloading = true
    const { data: res } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
    this.isloading = false
    data && data()  //  只要数据请求完毕，就立即调用data回调函数 
    if (res.meta.status !== 200) return uni.$showMsg()
    // 通过展开运算符的形式，进行数据合并
    this.goodsList = [...this.goodsList,...res.message.goods]
    this.total = res.message.total
},
```



```js
onReachBottom() {
    if (this.pageTotal >= this.total) return uni.$showMsg('数据加载完毕')
    if (this.isloading) return
    // 让页码值自增 +1
    this.queryObj.pagenum += 1
    // 重新获取列表数据
    this.getGoodsList()
},
```



> - *防抖*

`search_detail.vue`

```js
data() {
    return {
        timer: null, // 延时器的 timerId
        keyword: '', // 搜索关键词
        searchResults: [], // 搜索结果列表
        historyList: [], // 搜索历史列表
    }
}
```



```html
<uni-search-bar @input="input()" :radius="100" cancelButton="none" focus="true"></uni-search-bar>
```



```js
// 搜索防抖处理
input(e) {
    clearTimeout(this.timer)
    this.timer = setTimeout(() => {
        this.keyword = e.trim()
        this.getSearchResults()
    }, 500)
},
```



> - *自定义封装方法*

`main.js` 中，为 `uni` 对象挂载自定义的 `$showMsg()` 方法

```js
// 封装的展示消息提示的方法
uni.$showMsg = function (title = '数据加载失败！', duration = 1500) {
  uni.showToast({
    title,
    duration,
    icon: 'none',
  })
}
```

需要提示消息的时候，直接调用 `uni.$showMsg()` 方法即可

```js
async getSwiperList() {
   const { data: res } = await uni.$http.get('/api/public/v1/home/swiperdata')
   if (res.meta.status !== 200) return uni.$showMsg()
   this.swiperList = res.message
}
```



> - *自定义封装组件*

`goods.vue`

```html
<template>
  <view class="goods-item">
    <!-- 商品左侧图片区域 -->
    <view class="goods-item-left">
      <image :src="goods.goods_small_logo || defaultPic" class="goods-pic"></image>
    </view>
    <!-- 商品右侧信息区域 -->
    <view class="goods-item-right">
      <!-- 商品标题 -->
      <view class="goods-name">{{goods.goods_name}}</view>
      <view class="goods-info-box">
        <!-- 商品价格 -->
        <view class="goods-price">￥{{goods.goods_price}}</view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    // 定义 props 属性，用来接收外界传递到当前组件的数据
    props: {
      // 商品的信息对象
      goods: {
        type: Object,
        defaul: {},
      },
    },
    data() {
      return {
        // 默认的空图片
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png',
      }
    },
  }
</script>
```



goods_list组件中调用

```html
<view class="goods-list">
  <block v-for="(item, i) in goodsList" :key="i">
    <!-- 为 goods 组件动态绑定 goods 属性的值 -->
    <goods :goods="item"></goods>
  </block>
</view>
```



> - *组件动态展示*

**props 属性，用来接收外界传递到当前组件的数据**		

​		本地文件goods.vue定义props接收数据，创建一个对象控制组件的显示与隐藏

```js
 
props: {
    // 商品的信息对象
    goods: {
      type: Object,
      default: {},
    },
    // 是否展示图片左侧的 radio
    showRadio: {
      type: Boolean,
      // 如果外界没有指定 show-radio 属性的值，则默认不展示 radio 组件
      default: false,
    },
  },    
      
```

​		

​		本地文件goods.vue通过v-if 指令控制 radio 组件的显示与隐藏

> v-if 指令控制组件的显示与隐藏	
>
> 通过数据绑定显示单选框是否勾选	True显示	False不显示

v-if

​		适用于：切换频率较低的场景。特点：不展示的DOM元素直接被移除。

v-show

​		适用于：切换频率较高的场景。特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉.

使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到

### goods.vue

------

  	`<radio v-if="showRadio" :checked="goods.goods_state"></radio>`

> - ***组件通信***		数据传递



> props	 父组件  => 子组件传递数据

`通过父组件给子组件传递函数类型的props实现：子给父传递数据`

​	父文件cart.vue传入数据`goods`，子组件goods.vue的`props`属性进行接收，通过数据绑定属性`showRadio`动态显示radio 组件，true代表显示radio组件，而在不需要显示radio组件的文件中不传入showRadio的值默认不显示radio组件。

### **父文件cart.vue**

------

  		`<goods :goods="goods" :show-radio="true"></goods>`

**子组件goods.vue**

------

```js
props: {
    showRadio: {
    	//默认情况不展示Radio组件
     	type: Boolean,
        default: false
	}
}
```



> 自定义事件	子组件  => 父组件传递数据

`通过父组件给子组件绑定自定义事件实现子给父传递数据`	也可以用ref属性实现更多需求

### 父组件cart.vue

------

​	`<goods @radio-change="radioChangeHandler()" @num-change="numChangeHandler()"></goods>`

​	父组件可以接收到子组件传来的数据

```js
radioChangeHandler(e) {
	this.updateGoodsState(e)
},
```



**子组件goods.vue**

------

​			`<radio @click="radioClickHandler()" ></radio>`

```js
radioClickHandler() {
    this.$emit('radio-change', {
        goods_id: this.goods.goods_id,
        goods_state: !this.goods.goods_state
    })
},
```



> - ***Mixin混入***		 Vue 组件中的复用功能

新建一个tabBar.js文件，里面放置重复运用的代码

```JS
import { mapGetters } from 'vuex'
export default {
	computed: {
		...mapGetters('cart',['countTotal'])
	},
	onShow() {
		this.setCartBar()
	},
	methods: {
		setCartBar() {
			uni.setTabBarBadge({
				index: 2,
				text: this.countTotal + ''
			})
		}
	}
}
```



`外部组件调用	`

```js
// 导入自己封装的 mixin 模块
import tabBar from '../../mixins/tabBar.js'

export default {
  // 将 tabBar 混入到当前的页面中进行使用
  mixins: [tabBar]
  // 省略其它代码...
}
```



> - *VueX	多组件共享数据*

`创建srore目录store.js里面配置VueX。`



> - *CSS*

> 居中

```scss
display: flex;							flex布局 纵向排列变横向排列
justify-content: space-between;			  水平两边贴紧
align-items: center;					 垂直居中
```



```SCSS
text-align: center;																	居中对齐

```



> 添加竖横线	伪类选择器

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



> 文字换行和省略

```SCSS
white-space: nowrap; 		// 文字不允许换行（单行文本）
overflow: hidden; 			// 溢出部分隐藏`
text-overflow: ellipsis;	// 文本溢出后，使用 ... 代替
```



> 边距

```SCSS
padding: 0 5px; //上下0 左右5
border-bottom: 1px solid #efefef;   //底部边
```





> - *Git*

> 提交到master

```bash
git add  文件夹/
git commit -m "描述"
git remote add 别名 github网址
git push -u	别名 master
```



> 提交到分支并合并

```bash
git checkout -b 分支名							创建分支				
git add	文件夹/
git commit -m "描述"
git merge 分支名
git push -u	别名 master
git branch -d cate							   删除分支			
```

