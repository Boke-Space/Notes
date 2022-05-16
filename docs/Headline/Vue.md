> Headline



> - ## *定时器*

`点击提交评论和评论框不能同时发生，提交评论会导致先失焦关闭评论框而导致不能使评论发表成功，需要给失焦事件添加定时器延迟`

ComementList.vue

```js
//  评论框失焦
commentBlur() {
  // 问题: 点击发布按钮, 发现点击事件不执行-排除代码问题
  // 原因: 高的评论容器-在页面点击发布一瞬间, 输入框失去了焦点, 被v-if和v-else移除了整个标签
  // 导致点击事件没来得及执行
  // 解决: 失去焦点时, 变量值, 在最后再执行
  // this.$nextTick() DOM更新(数据变化)
  setTimeout(() => {
    this.isShowCommentBox = true
  }, 100)
},
```



```js
//  发送评论
async sendComment() {
  const res = await commentSendAPI({
    artId: this.$route.query.art_id,
    content: this.commentText
  })
  this.commentList.unshift(res.data.data.new_obj)
  this.totalCount++
  this.commentText = ''
  this.skipComement()			//  跳转到评论区
},
```



> - ## *点赞与关注	True False的转化*

`ArticleDeail.vue`

```html
<!-- 已关注-->
<van-button type="info" size="mini" v-if="articleDetail.is_followed" @click="follow(true)">已关注</van-button>
<!-- 未关注-->
<van-button icon="plus" type="info" size="mini" plain v-else @click="follow(false)">关注</van-button>


<!-- 点赞(文章) -->
<!-- attitude:  -1: 无态度，0-不喜欢，1-点赞 -->
<van-button icon="good-job" type="danger" size="small" v-if="articleDetail.attitude === 1" @click="like(true)">已点赞</van-button>
<van-button icon="good-job-o" type="danger" plain size="small" v-else @click="like(false)">点赞</van-button>

```



```js
async follow(flag) {
  if (flag === true) {
    // 用户点在 "已关注" 按钮上
    // 页面   ->  显示关注按钮
    this.articleDetail.is_followed = false
    const res = await userUnFollowAPI({
      userId: this.articleDetail.aut_id
    })
    // 业务和接口 -> 取关接口
  } else {
    // 用户点在 "关注" 按钮上
    // 页面   ->  显示已关注按钮
    this.articleDetail.is_followed = true
    // 业务和接口 -> 关注接口
    const res = await userFollowAPI({
      userId: this.articleDetail.aut_id
    })
  }
},
```



```js
async like(flag) {
    if (flag === true) {
        // 用户点在了 "已点赞" 身上
        // 显示"点赞"按钮
        this.articleDetail.attitude = 0
        // 0不喜欢, -1无态度
        // 业务和接口 -> 取消点赞
        const res = await articleDisLikeAPI({
            artId: this.articleDetail.art_id
        })
        } else {
            // 用户点在了 "点赞" 身上
            // 显示"已点赞"按钮
            this.articleDetail.attitude = 1
            // 业务和接口 -> 点赞接口
            const res = await articleLikeAPI({
                artId: this.articleDetail.art_id
            })
	}
}
```



> - ## *Vue*

`$router下用于跳转路由`

`$route是路由信息对象`



`this.$router.push() 压栈 会产生历史记录，可以回退`
`this.$router.replace() 替换  不会产生历史记录`



`数组长度为0		!commentText.length`

`数组长度不为0		commentText.length`



`三元运算符渲染评论数量	若为0不显示`

```js
:content="totalCount === 0 ? '' : totalCount"
```



`通过class满足条件动态显示样式,    {' 类名' ： 条件 }`

```html
<div class="cmt-list" 
:class="{ 'art-cmt-container-1' : isShowCommentBox,'art-cmt-container-2' : !isShowCommentBox }">
```



> `isShowCommentBox: true    //显示底部评论输入框`

`失焦事件，点击外面隐藏输入框`

```js
@blur="isShowCommentBox = true"
```



`点击输入框，弹出输入框`

```js
@click="isShowCommentBox = false"
```



`通过Set过滤出历史记录重复的数据`

```js
watch: {
    // ES6新增了2种引用类型(以前 Array, Object), (新增: Set Map)
    // Set: 无序不重复的value集合体 (无下角标)
    // 特点: 你传入的数组类型, 如果有重复元素, 会自动清理掉重复元素, 返回无重复的Set对象
    historyList: {
        deep: true,
            handler() {
            const hisrorySet = new Set(this.historyList)
            // Set类型对象 -> 转回 -> Array数组类型
            const hisroryArr = Array.from(hisrorySet)
            localStorage.setItem('his', JSON.stringify(hisroryArr))
        },
    }
},
```



`坑: 路由跳转, 在watch之前执行, 所以我们要让路由跳转, 来一个定时器包裹, 让跳转最后执行`

```js
//  搜索事件  跳转到搜索结果页面
searchResult() {
    if (this.kw.length > 0) {
        this.historyList.push(this.kw)
        setTimeout(() => {
            this.$router.push({
                path: `/search_result/${this.kw}`
            })
        },0)
    }
},
```



> ChannelEdit.vue

`通过三元表达式动态渲染内容`

```html
<span>我的频道
    <span class="small-title">
    点击  {{ isEdit ? '删除' : '进入' }}  频道
    </span>
</span>
<span @click="isEdit = !isEdit">  {{ isEdit ? '完成' : '编辑' }}  </span>
```

`通过v-show动态渲染删除徽标`

```html
<van-badge color="transparent" class="cross-badge" v-show="isEdit && selected.id !== 0"></van-badge>
```



`通过点击编辑触发事件添加和删除频道，通过自定义事件传递当前id给父组件Home.vue`

> 子组件ChannelEdit

------



```js
//  添加频道
addUnselectedChannel(unselected) {
  //  处于编辑状态
  if (this.isEdit === true) {
    this.$emit('add-channel', unselected)
  }
},
//  删除频道
deleteSelectedChannel(selected) {
  if (this.isEdit === true) {
    if (selected.id !== 0) this.$emit('delete-channel', selected)
  } else {
    // 切换频道
    this.$emit('close-channel') // 关闭弹出层
    this.$emit('input', selected.id)
  }
},
```



> 父组件Home

------

子组件ChannelEdit通过props接收Home传递过来的用户用户已选频道列表和用户未选频道列表

`用户未选择频道  =	所有频道 - 用户已选频道列表`

```html
<!-- 弹出层组件 -->
<van-popup v-model="show" class="edit_wrap">
    <ChannelEdit  :selectedList="userChannelList" :unselectedList="unselectedChannelList"
    @add-channel="addChannel" @delete-channel="deleteChannel" ref="editRef"
    @close-channel="closeChannel" v-model="channelId">
    </ChannelEdit>
</van-popup>
```



> channel-edit添加ref(为了获取组件对象, 修改内部的isEdit变量

```js
closeChannel() {
    this.show = false
    this.$refs.editRef.isEdit = false
    //  控制子组件ChannelEdit里的isEdit,使编辑状态变为初始
    //  也可以在在子组件中closePannel()中直接设置为false
},
```



```js
unselectedChannelList() {
     const unselectedArr = this.allChannelList.filter(all => {
         const index = this.userChannelList.findIndex(user => user.id === all.id)
         if (index > -1) return false
         else  return true
     })
     return  unselectedArr
}
```



```js
 //  添加频道
async addChannel(unselected) {
    this.userChannelList.push(unselected)
    // 上面的代码出问题, 新增时, 名字被删除了
    // 原因: 所有数组里的对象, 都是同一个内存地址, 影响到ChannelEdit.vue中对象
    // 解决方法
    const newArr = this.userChannelList.filter((obj) => obj.id !== 0)
    const theNewArr = newArr.map((obj, index) => {
        const newObj = { ...obj } // 拷贝(浅拷贝)
        delete newObj.name
        newObj.seq = index
        return newObj // 让map方法把新对象收集起来形成一个新数组
    })
    const res = await updateChannelAPI({
        channels: theNewArr
    })
},
//  删除频道
async deleteChannel(selected) {
    const index = this.userChannelList.findIndex(user => user.id === selected.id)
    this.userChannelList.splice(index, 1)
    const res = await deleteChannelAPI({
        channelId: selected.id
    })
},
```



> - ## *数据刷新*	

`建议用index为key值`	本来用id作为索引值做底部下拉刷新和顶部上拉刷新发生报错，改为index后无报错。

![img](https://img-blog.csdnimg.cn/20181218141815394.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RyZWFtX3h1bg==,size_16,color_FFFFFF,t_70)

*key值是必须唯一的，如果重复就会报错 可以把key值改为index（其实就是用索引做key值），就可以避免这个情况：*

`ArticleList.vue`

> 底部下拉刷新和顶部上拉刷新

```js
data() {
    return {
        articleList: [],                  //文章列表数组
        bottomLoading: false,              // 底部加载状态
        bottomFinished: false,                  // 底部完成状态
        topLoading: false,                 // 顶部加载状态
        time: new Date().getTime(),       // 用于分页
    }
},

//  获取文章数据
async getArticleList() {
    const res = await getArticlesListAPI({
        channel_id: this.channelId,
        timestamp: (new Date()).getTime()
    })
    //  展开运算符进行数组合并
    this.articleList = [...this.articleList, ...res.data.data.results]
    this.time = res.data.data.pre_timestamp
    // 第一次 系统时间(timestamp) ->   后台 返回0-9条数据 ->携带第10条pre_timestamp值返回
    // 第二次 (timestamp)-上一次pre_timestamp (代表告诉后, 从指定的时间戳再往后找10个) 10-19条, 20条pre_timestamp返回
    if (res.data.data.pre_timestamp === null) this.bottomFinished = true // 第一次上面还是判定触底(触发onLoad方法时bottomLoading自动改true)
    if (!res.data.data.results.length) this.bottomFinished = true
},
```



```html
<van-pull-refresh v-model="topLoading" @refresh="onReFresh()">
    <van-list v-model="bottomLoading" :finished="bottomFinished" 			     	@load="onLoad()"finished-text="没有更多了" 
       :immediate-check="false" offset="50">
</van-pull-refresh>
```

 

```js
//  底部下拉刷新
async onLoad() {
        if (this.articleList.length) {
            this.getArticleList()
            this.bottomLoading = true
        } else {
        this.bottomLoading = false 
        // 如果不关闭, 底部一直是加载中状态, 下次触底会再触发onLoad
        this.bottomFinished = true
	}
},
```



```JS
//  顶部上拉刷新
async onReFresh() {
    //  清空数组 请求新的数据
    this.articleList = []
    this.time = new Date().getTime()
    this.getArticleList()
    this.topLoading = false
},
```



> - ## *组件通信*

> ### props	*父=>子=>孙*		*Home	 => 	ArticleList => 	ArticleItem*

父组件Home.vue传递channelId给子组件ArticleList.vue

ArticleList.vue通过props进行接收数据,通过id为每个导航栏各自获取自己页面数据 互不影响，当点击其他导航栏再切换回来不会进行数据刷新，当下拉底部或上拉顶部才会进行数据刷新

ArticleList.vue传递文章列表数组articleItem给孙组件ArticleItem.vue，ArticleItem.vue通过props进行接收数据

ArticleItem.vue根据传递来的数据进行渲染图片

`Home.vue`

```html
<van-tab v-for="item in userChannelList" :key="item.id" :title="item.name">
    <ArticleList :channelId="channelId"></ArticleList>
</van-tab>
```



`ArticleList.vue`

```css
props: {
    channelId: Number
},
```



```html
<ArtileItem v-for="item in articleList" :key="item.attr_id"
  :articleItem="item" @article-dislike="dislike" @article-report="report">
</ArtileItem>
```



 ``articleList: [],                  //文章列表数组`
 `bottomLoading: false,              //底部加载状态`
 `bottomFinished: false,             //底部完成状态`
 `topLoading: false,                 //顶部加载状态`
 `time: new Date().getTime(),         //用于分页`



```js
//  获取文章数据
async getArticleList() {
    const res = await getArticlesListAPI({
        channel_id: this.channelId,
        timestamp: (new Date()).getTime()
    })
    //  展开运算符进行数组合并
    this.articleList = [...this.articleList, ...res.data.data.results]
    this.time = res.data.data.pre_timestamp
    // 第一次 系统时间(timestamp) ->   后台 返回0-9条数据 ->携带第10条pre_timestamp值返回
    // 第二次 (timestamp)-上一次pre_timestamp (代表告诉后, 从指定的时间戳再往后找10个) 10-19条, 20条pre_timestamp返回

    this.bottomLoading = false // 如果不关闭, 底部一直是加载中状态, 下次触底会再触发onLoad
    if (res.data.data.pre_timestamp === null) {
        this.bottomFinished = true // 第一次上面还是判定触底(触发onLoad方法时bottomLoading自动改true)
    }
    this.topLoading = false
},
```



`ArticleItem.vue`

```css
props: {
	articleItem: Object,    //文章对象
},
```



```js
<!-- 多图 -->
<div v-if="articleItem.cover.type > 1" class="thumb-box">
    <img v-for="(imgUrl, index) in articleItem.cover.images"
    :key="index" :src="imgUrl" class="thumb">
</div>
```



> ### 自定义事件	*子	=>	父	 ArticleItem	=>	ArticleList* 

`子组件ArticleItem.vue`

```js
 // 反馈面板选择事件
onSelect(action) {
    // action绑定的数据对象, index是索引
    if (action.name === '反馈垃圾内容') {
        this.actions = secondActions
        this.cancelText = '返回' // 修改底部按钮显示文字
    } else if (action.name === '不感兴趣'){
        this.$emit('article-dislike',this.articleItem.art_id)
        this.show = false
    } else {
        this.$emit('article-report',this.articleItem.art_id, action.value)
        this.actions = firstActions
        this.show = false
    }
}
```



`父组件ArticleList.vue`

```js
//  自定义事件 不感兴趣
async dislike(id) {
    const res = await articlesDislikeAPI({
        artId: id
    })
    Notify({
        type: 'success',
        message: '反馈成功'
    })
},
//  自定义事件 反馈垃圾内容
async report(id, value) {
    const res = await articlesDislikeAPI({
        artId: id,
        type: value
    })
    Notify({
        type: 'success',
        message: '举报成功'
    })
}
```




