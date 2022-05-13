> UniShop	



> - ### *数组处理*

forEach	map遍历		find	filter过滤	reduce累加

```js
goods: this.cart.filter(x => x.goods_state).map(x => ({
    goods_id: x.goods_id,
    goods_number: x.goods_count,
    goods_count: x.goods_count
}))
```



```js
// 更新所有商品的勾选状态
updateAllGoodsState(state, newState) {
    state.cart.forEach(x => x.goods_state = newState)
    this.commit('cart/saveStorage')
},
```



```js
addCart(state, goods) {
    // 根据提交的商品的Id，查询购物车中是否存在这件商品
    // 如果不存在，则 result 为 undefined；否则，为查找到的商品信息对象
    const result = state.cart.find(x => x.goods_id === goods.goods_id)
    if (!result) state.cart.push(goods) // 如果购物车中没有这件商品，则直接 push
    else result.goods_count++ // 如果购物车中有这件商品，则只更新数量即可
    this.commit('cart/saveStorage')
},
```



```js
// 勾选的商品的总数量
checkedCount(state) {
    // 先使用 filter 方法，从购物车中过滤器已勾选的商品
    // 再使用 reduce 方法，将已勾选的商品总数量进行累加
    // reduce() 的返回值就是已勾选的商品的总数量
    return state.cart.filter(i => {
    	return i.goods_state
    }).reduce((total, j) => {
    	return total = total + j.goods_count
    },0)
},
    
//简写		
return state.cart.filter(x => x.goods_state).reduce((total, item) => total += item.goods_count, 0)

// 已勾选的商品的总价
checkedGoodsAmount(state) {
    // 先使用 filter 方法，从购物车中过滤器已勾选的商品
    // 再使用 reduce 方法，将已勾选的商品数量 * 单价之后，进行累加
    // reduce() 的返回值就是已勾选的商品的总价
    // 最后调用 toFixed(2) 方法，保留两位小数
    return state.cart.filter(x => x.goods_state).reduce((total, i) => total += i.goods_count * i.goods_price, 0).toFixed(2)
}
```





> - ### 节流阀		防止发起额外的请求

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



> - ### *防抖*

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



> - ### *自定义封装方法*

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



> - ### *自定义封装组件*

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



`goods_list组件中调用`

```html
<view class="goods-list">
  <block v-for="(item, i) in goodsList" :key="i">
    <!-- 为 goods 组件动态绑定 goods 属性的值 -->
    <goods :goods="item"></goods>
  </block>
</view>
```



> - ### *组件动态展示*

`props 属性，用来接收外界传递到当前组件的数据`		

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

`v-if`

​		适用于：切换频率较低的场景。特点：不展示的DOM元素直接被移除。

`v-show`

​		适用于：切换频率较高的场景。特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉.

使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到



`goods.vue`

```html
<radio v-if="showRadio" :checked="goods.goods_state"></radio>
```



> - ## ***组件通信***		数据传递



> ### props	 父组件  => 子组件传递数据

`通过父组件给子组件传递函数类型的props实现：子给父传递数据`

​	父文件cart.vue传入数据`goods`，子组件goods.vue的`props`属性进行接收，通过数据绑定属性`showRadio`动态显示radio 组件，true代表显示radio组件，而在不需要显示radio组件的文件中不传入showRadio的值默认不显示radio组件。

`父文件cart.vue`

```html
	`<goods :goods="goods" :show-radio="true"></goods>`
```



`子组件goods.vue`

```js
props: {
    showRadio: {
    	//默认情况不展示Radio组件
     	type: Boolean,
        default: false
	}
}
```



> ### 自定义事件	子组件  => 父组件传递数据

`通过父组件给子组件绑定自定义事件实现子给父传递数据`	也可以用ref属性实现更多需求

父组件可以接收到子组件传来的数据

`父组件cart.vue`

```html
<goods @radio-change="radioChangeHandler()" @num-change="numChangeHandler()"></goods>

```

​	

```js
radioChangeHandler(e) {
	this.updateGoodsState(e)
},
```



`子组件.vue`

```html
<radio @click="radioClickHandler()" ></radio>
```



```js
radioClickHandler() {
    this.$emit('radio-change', {
        goods_id: this.goods.goods_id,
        goods_state: !this.goods.goods_state
    })
},
```



> - ## ***Mixin混入***		 Vue 组件中的复用功能

新建一个tabBar.js文件，里面放置重复运用的代码

```JS
import { mapGetters } from 'vuex'
export default {
	onShow() {
		this.setCartBar()
	},
	watch: {
		countTotal() {
			// 调用 methods 中的 setBadge 方法，重新为 tabBar 的数字徽章赋值
			this.setCartBar()
		}
	},
	computed: {
		...mapGetters('cart',['countTotal'])
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



> - ## *VueX	多组件共享数据*

`创建srore目录store.js里面配置VueX。`

在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

`cart.js`

```js
export default {
	namespaced: true,
	state: () => ({
		cart: JSON.parse(uni.getStorageSync('cart') || '[]')
	}),

	mutations: {
		saveStorage(state) {
			uni.setStorageSync('cart', JSON.stringify(state.cart))
		},
		addCart(state, goods) {
			// 根据提交的商品的Id，查询购物车中是否存在这件商品
			// 如果不存在，则 result 为 undefined；否则，为查找到的商品信息对象
			const result = state.cart.find(x => x.goods_id === goods.goods_id)
			if (!result) state.cart.push(goods) // 如果购物车中没有这件商品，则直接 push
			else result.goods_count++ // 如果购物车中有这件商品，则只更新数量即可
			this.commit('cart/saveStorage')
		},
		// 更新购物车商品的勾选状态
		updateGoodsState(state, goods) {
			const result = state.cart.find(x => x.goods_id === goods.goods_id)
			if (result) {
				result.goods_state = goods.goods_state
				this.commit('cart/saveStorage')
			}
		},
		// 更新购物车商品数量
		updateGoodsCount(state, goods) {
			const result = state.cart.find(x => x.goods_id === goods.goods_id)
			if (result) {
				result.goods_count = goods.goods_count
				this.commit('cart/saveStorage')
			}
		},
		// 根据 Id 从购物车中删除对应的商品信息
		removeGoodsById(state, goods_id) {
			// 调用数组的 filter 方法进行过滤
			state.cart = state.cart.filter(x => x.goods_id !== goods_id)
			this.commit('cart/saveStorage')
		},
		// 更新所有商品的勾选状态
		updateAllGoodsState(state, newState) {
			state.cart.forEach(x => x.goods_state = newState)
			this.commit('cart/saveStorage')
		},
	},

	getters: {
		// 统计购物车中商品的总数量
		countTotal(state) {
			return state.cart.reduce((total, item) => total += item.goods_count, 0)
		},
		// 勾选的商品的总数量
		checkedCount(state) {
			// 先使用 filter 方法，从购物车中过滤器已勾选的商品
			// 再使用 reduce 方法，将已勾选的商品总数量进行累加
			// reduce() 的返回值就是已勾选的商品的总数量
			return state.cart.filter(x => x.goods_state).reduce((total, item) => total += item.goods_count, 0)
		},
		// 已勾选的商品的总价
		checkedGoodsAmount(state) {
			// 先使用 filter 方法，从购物车中过滤器已勾选的商品
			// 再使用 reduce 方法，将已勾选的商品数量 * 单价之后，进行累加
			// reduce() 的返回值就是已勾选的商品的总价
			// 最后调用 toFixed(2) 方法，保留两位小数
			return state.cart.filter(x => x.goods_state).reduce((total, i) => total += i.goods_count * i.goods_price, 0).toFixed(2)
		}
	},

}

```



`cart.vue`中可直接调用`VueX`中的数据

```js
import { mapState, mapMutations } from 'vuex'
computed: {
	...mapState('cart', ['cart'])
},
methods: {
    ...mapMutations('cart', ['updateGoodsState', 'updateGoodsCount', 'removeGoodsById']),
    // 商品的勾选状态发生变化
    radioChangeHandler(e) {
        this.updateGoodsState(e)
    },
    // 商品的数量发生变化
    numChangeHandler(e) {
       	this.updateGoodsCount(e)
    },
    swipeActionClickHandler(goods) {
        this.removeGoodsById(goods.goods_id)
   }
}
```



`settle.vue`中也可以直接调用`VueX`中`cart`和`user`的数据

```js
<view class="amount">
	<text>合计:￥{{checkedGoodsAmount}}</text>
</view>

<view class="btn-settle" @click="settlement()">结算({{checkedCount}})</view>

import { mapState, mapMutations, mapGetters } from 'vuex'
computed: {
    ...mapGetters('cart', ['checkedCount', 'countTotal', 'checkedGoodsAmount']),
    ...mapGetters('user', ['addStr']),
    ...mapState('cart', ['cart']),
    ...mapState('user', ['token']),
     //是否全选
	isFullCheck() {
		return this.checkedCount === this.countTotal
	},
},
methods: {
    ...mapMutations('cart', ['updateAllGoodsState']),
    ...mapMutations('user', ['updateRedirectInfo']),
    // 修改购物车中所有商品的选中状态
    changeAllState() {
        this.updateAllGoodsState(!this.isFullCheck)
    },
    //	结算购物车
    settlement() {
        if (!this.checkedCount) return uni.$showMsg('请选择要结算的商品')
        if (!this.addStr) return uni.$showMsg('请选择收获地址')
        if (!this.token) return this.delayNavigate()
    },
```

